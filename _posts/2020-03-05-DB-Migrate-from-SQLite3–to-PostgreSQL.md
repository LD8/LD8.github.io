---
layout: post
title:  "DATABASE: Migrate from SQLite3 to PostgreSQL"
categories: Database
---
# Objectives
Two databases were setup on server, `sqlite` is for development, `PostgreSQL` is for production. It is probably a stupid idea in the first place: because of an environment variable error, `sqlite` was still being used in production and user has been uploading data (files and pictures) for a while. I now need to change the database to `PostgreSQL` on the server without losing the data.

---

## **Server**: Ubuntu 18.04
* Goal: To migrate/transfer SQLite3 data to PostgreSQL
* To dos:
    * research how to backup sqlite databases 
    * find and install necessary tools
    * backup database waiting to be transferred remotely
* Tools: 
    * [`django-archive`](https://django-archive.readthedocs.io/en/latest/)==0.1.6 easy to use and setup, for backing up database as well as media files
    * <s>[`django-dbbackup`](https://pypi.org/project/django-dbbackup/)==3.2.0</s> couldn't setup backup folder path correctly somehow
    * <s>[pgloader](https://pgloader.readthedocs.io/en/latest/intro.html)</s> Actually didn't get you use it

---

# Process
After a whole day of running round the circles and walking into deadends, I finally figured out. At times, I felt I was so close to the solution of the problem yet an error throw me off the edge... But I finally solved it after 2 days of work. It's actually quite easy.

## 1: DUMP  
Make sure in `settings.py`, your database is still pointing to `db.sqlite3` if you use default django database.
```bash
(venv)$ python manage.py dumpdata > datadump.json
```
You will have a `datadump.json` file in your project directory

## 2. NEW DATABASE SETTING
point your database to your new database to be created
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'db_name',
        'USER': 'db_user',
        'PASSWORD': os.environ.get('DB_PASSWD'),
        'HOST': 'localhost',
        'PORT': '',
    }
}
```
## 3. CREATE NEW DATABASE WITH 'UTF-8' ENCODING
Some of the data is written in Russian (Cyrillic), ***wrong encoding of the database*** can be disastrous! Apparently, ***you can only set the encoding of a database when you create it. It can not be altered afterwards.*** 

Here's how to create a database support all language input ==>
```postgres
$ sudo -u postgres psql
postgres=# CREATE DATABASE new_db_name WITH ENCODING='UTF8' LC_CTYPE='ru_RU.UTF-8' LC_COLLATE='ru_RU.UTF-8' OWNER=postgres TEMPLATE=template0;
# note: 
# 1. I need Russian locale encoding: 'ru_RU.UTF-8'; 
# 2. you have to specify 'template0' if your template0 and template1 encoding is not 'UTF8'

postgres=# GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;
# don not forget to grant access before exiting the psql shell
# you can use \l to check existing databases
postgres=# \l
postgres=# \q
```
check out [CREATE DATABASE](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) for more

## 4. MIGRATE DATA
We need to create the tables in the new database:
```bash
(venv)$ python manage.py migrate --run-syncdb
```

## 5. EXCLUDE CONTENTTYPES
Before loading dumped data, we need to exclude contenttype data, for avoiding integrity errors:
```bash
(venv)$ python manage.py shell
>>> from django.contrib.contenttypes.models import ContentType
>>> ContentType.objects.all().delete()
>>> quit()
```
##### Thanks for [this post by coderasha](https://dev.to/coderasha/how-to-migrate-data-from-sqlite-to-postgresql-in-django-182h)

## 6. LOADDATA
And finally:
```bash
(venv)$ python manage.py loaddata datadump.json

Installed 541 object(s) from 1 fixture(s)
```
However:
### TROUBLESHOOTING:
You will likely to run into many problems and errors on the way, but don't worry, read them and analyze them, google and stackoverflow, here's a few:

* >UnicodeEncodeError: 'latin-1' codec can't encode characters in position 0-2: ordinal not in range(256)  

    This happened when I tried to `loaddata` (step 6). Step 3 is the fix.

* >character with byte sequence 0xd0 0x90 in encoding "UTF8" has no equivalent in encoding "LATIN1"

    This is caused by the system LANGUAGE setting. The system can not recognize/find the cyrillic charactors in "LATIN1" encoding/'library'. If you need to support other languages, use 'utf-8' EVERYWHERE!!!

    QUICK FIX LANG = UTF-8 IN UBUNTU  
    The system default (LANG=en_US) didn't support cyrillic at all, so try the command below and logout login again:
    ```bash
    # if you want to generate say Russian cyrillic language locale in UTF-8
    $ sudo locale-gen "ru_RU.UTF-8"
    # also try this to permanently change all of your locale to en_US.UTF-8
    $ sudo update-locale LANG="en_US.UTF-8" LANGUAGE="en_US.UTF-8"

    $ locale
    # run locale see if everything equals to 'en_US.UTF-8'
    ```
    ##### Thanks for [this post by perlgeek](https://perlgeek.de/en/article/set-up-a-clean-utf8-environment)

* >users.models.DoesNotExist: Problem installing fixture '...' matching query does not exist

    SOME TABLE MISSING  
    These tables are likely be the ones whoes creation depends on others. In my case, a profile table wounldn't be initiated unless a User instance is created. Hence, I created a superuser before I ran Step 6 above. Problem solved.

---
---
---

# Many many other failed attempts
Although these attempts didn't become the solution to this problem, they were the lessons I missed:
## 1. Installation of pgloader:
### [pgloader](https://pgloader.readthedocs.io/en/latest/intro.html): By default, `apt` found the latest version of `pgloader` is v3.4.1
```bash
# on Ubuntu server, install PGLoader
$ sudo apt-get update -y
$ sudo apt-get install -y pgloader

$ sudo -u postgres pgloader db.sqlite3 postgresql:///db_name
```
This will prompt an error because of a bug of some sort in the outdated version. After researching for a while, I found out that pgloader needed to be updated:

---

## 2. Update PostgreSQL packages list for Ubuntu: check this [article: PostgreSQL packages for Debian and Ubuntu](https://wiki.postgresql.org/wiki/Apt): 
I couldn't update `pgloader` from 3.4 to 3.6 so I tried the method in the article to update the installation listk, then:
```bash
# uninstall current old version of pgloader:
$ sudo apt-get purge --auto-remove pgloader

# install the new one:
$ sudo apt update
$ sudo apt install pgloader
# Normal the above code can get you the latest version of the package
```
### Now the `pgloader` is v3.5, although not v3.6 but it's enough to do the following ==>

---

## 3. Now you will be able to migrate the SQLite database to the PostgreSQL:
```bash
$ sudo -u postgres pgloader db.sqlite3 postgresql:///db_name
KABOOM!
KILLED
# the website now is not accessable, error: "ProgrammingError at / permission denied for relation..."

# because the public tables haven't grant access to the project user, type:
$ sudo -u postgres psql db_name
db_name=> GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO myprojectuser;
```
### Now the website can be accessed normally, however ==>

---


## 4. Restoring the database with the correct encoding 'UTF-8'
For details of the problem you can checkout the [post](https://stackoverflow.com/questions/60538965/django-database-from-sqlite-to-postgres-not-fully-migrated-lacking-languag?noredirect=1#comment107100611_60538965), here's the solution:

### - if you want to keep the existing database, dump the data before renaming it:
```bash
$ sudo -u postgres pg_dump dbname > dbname.bak
$ sudo -u postgres psql
postgres=> ALTER DATABASE db_name RENAME TO temp_name
```

Reference articles:
* pgloader official doc: [Migrating an SQLite database to PostgreSQL](https://pgloader.readthedocs.io/en/latest/ref/sqlite.html#sqlite-database-source-specification-from)

## This was the dead end. I spent too much time on pgloader, but the solution was actually extremely easy, didn't need to use this sophisticated package at all...