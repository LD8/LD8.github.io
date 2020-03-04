---
layout: post
title:  "Django Deployment: How to Backup and Restore Postgres DATABASE"
categories: Django Deployment
---
# Objectives
Two databases were setup on server, `sqlite` is for development, `PostgreSQL` is for production. It is probably a stupid idea in the first place: because of an environment variable error, the database on the server was still using `sqlite` and user has been uploading data (files and pictures) for a while. I need to change the database to `PostgreSQL` on the server without losing the data.

---

# Phase I: backup and test restore locally
## **On Server**: Ubuntu 18.04 LTS  
* Goal: To backup exisiting `sqlite3` database on server
* To dos:
    * research how to backup databases 
    * find and install necessary tools
    * backup database waiting to be transferred remotely
* Tools: 
    * [`django-archive`](https://pypi.org/project/django-archive/)==0.1.6 easy to use and setup
    * <s>[`django-dbbackup`](https://pypi.org/project/django-dbbackup/)==3.2.0</s> couldn't setup backup folder path correctly somehow

## **Local**: Mac OS 10.15.3
* Goal: Test restore data from sqlite to Postgresql
* To dos:
    * research how to restore data from sqlite
    * install necessary tools
    * restore data and runserver locally, see if data applied
    * move on to phase II: On server - migrating data from sqlite to Postgresql
* Tools:
    * [PGloader](https://pgloader.readthedocs.io/en/latest/intro.html)

---

# On Server: Backup
## On Server commandline:
* install `django-archive` package, [link here](https://django-archive.readthedocs.io/en/latest/index.html), good thing about this package is that it not only dump all the database data but also backup all the media data
    ```bash
    (venv)$ pip install django-archive
    ```
* setting.py file
    ```python
    INSTALLED_APPS = (
        # ...
        'django_archive',
    )
    ```
VOILA! simple as that. I think it's better than `django-dbbackup` which is more difficult to setup.
```bash
(venv)$ python manage.py archive
# by default, this will create a file inside your project folder, same folder your venv lives
```
## Now download the file from server ==> 

# Local: Pull backed-up data
## Local commanline:
```bash
# rsync backup file from remote to local
MacBook-Pro$ rsync -avz -e ssh va-boutique:/home/don/VA-boutique/2020-03-03--09-51-25.tar.bz2 __web_backup__/
# now be careful here, if you want to save your file into the target folder, there's no need to type slash before the target folder, otherwise it won't work if you don't have the writing privilege
receiving file list ... done
2020-03-03--09-51-25.tar.bz2

sent 38 bytes  received 12240068 bytes  21760.19 bytes/sec
total size is 12235494  speedup is 1.00
# it took me around 10 mins to receive a 12MB zip file
```
## Now the backup file is in your local folder, next ==>

# Local: Restore
## Installation:
I used the django default `sqlite3` for development, therefore, `PostgreSQL` is not installed locally. The process of this installation is different on Mac compared to that on Ubuntu:
```bash
# Mac OS 10.15.3
(venv)$ brew install postgresql

# this could take a while
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> New Formulae
......
==> postgresql
To migrate existing data from a previous major version of PostgreSQL run:
  brew postgresql-upgrade-database

To have launchd start postgresql now and restart at login:
  brew services start postgresql
Or, if you dont want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres start

(venv)$  brew services start postgresql

# install PostgreSQL drivers for python 3
(venv)$ pip install psycopg2-binary

# Migration
(venv)$ python manage.py migrate

```

```bash
# create a database
(venv)$ createdb va
(venv)$ brew install pgloader
```

## In local `settings.py`:
replace `sqlite3` DATABASE setting with:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'myproject',
        # 'USER': 'db_user',
        # 'PASSWORD': 'db_password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```










[pgAdmin](https://www.pgadmin.org/): a PostgresSQL tool for database management

[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)

---
## [Benefits of environment variables](https://hyperlane.co/blog/the-benefits-of-environment-variables-and-how-to-use-them):

- Easy configuration
- Better security
- Fewer production mistakes
Notes: 
    ```bash
    # it doesn't matter whether you add quotation marks for the value when you export, even when they are strings
    $ export CAPITAL_NAME=true
    # the value 'true' will be a string anyway

    # if you export directly in command, be careful of ! as it needs to be excaped \!
    $ explort PASSWD_DB = something\!and@#others
    ```





---
# [django-cron](https://github.com/Tivix/django-cron)
backup using django-cron