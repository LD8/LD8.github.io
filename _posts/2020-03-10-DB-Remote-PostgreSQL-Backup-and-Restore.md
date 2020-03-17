---
layout: post
title:  "DATABASE: PostgreSQL Backup and Restore (remote)"
categories: Database
---
### First, the basics of backing up PostgresSQL db. Then I'll record how I back up my own database on server for future reference.
# The Basics
## [pg_dump](https://www.postgresql.org/docs/12/app-pgdump.html) command
```bash
# on Ubuntu server
$ sudo -u postgres pg_dump dbname > dumpfile
```
## Restore 
```bash
# on Ubuntu server
$ sudo -u postgres psql dbname < dumpfile
```
> By default, the psql script will continue to execute after an SQL error is encountered. You might wish to run psql with the ON_ERROR_STOP variable set to alter that behavior and have psql exit with an exit status of 3 if an SQL error occurs:
```
$ sudo -u postgres psql --set ON_ERROR_STOP=on dbname < dumpfile
```

You can always check the [official Doc](https://www.postgresql.org/docs/12/backup.html) for more on this subject.

---
---
---
# My commands for postgres db and media files backup
Two parts: `django-archive` to backup media files; `pg_dump` remotely to backup postgres db. My commands below as a record:

### 1. [`django-archive`](https://django-archive.readthedocs.io/en/latest/): media folder backup
#### * `settings.py`: Assuming `django-archive` installed, extra settings:
```python 
ARCHIVE_DIRECTORY = os.path.join(BASE_DIR, '_backups/_archives')
ARCHIVE_FILENAME = '%Y-%m-%d'
```
#### * command on server: create `archive data`
```bash
(venv)~/project_folder$ mkdir -p _backups/_archives
(venv)~/project_folder$ python manage.py archive
```

#### * local command: pull `archive data` from the server
```bash
$ rsync -avz --progress --remove-source-files -e ssh va-boutique:/home/don/VA-boutique/_backups/_archives/"$(date '+%Y-%m-%d')".tar.bz2 /Users/peiwen_li/Documents/GitHub/VA-boutique/_backups/_archives
# --progress option will show the downloading process
# --remove-source-files option will remotely delete the source file on server
```

### 2. `pg_dump` remotely to backup postgres db
#### You can run `pg_dump` on server, then pull it to local
```bash
$ sudo -u postgres pg_dump vadb > /home/don/VA-boutique/_backups/_pgdumps/vadbdump_"$(date '+%Y-%m-%d')"
```
#### ***BUT RATHER***:
```bash
$ ssh -C va-boutique pg_dump -U postgres -h localhost vadb > vadbdump_"$(date '+%Y-%m-%d')"
# Success

# OR:

$ ssh va-boutique "pg_dump -U postgres -h localhost vadb" > vadbdump_"$(date '+%Y-%m-%d')"
# Success

$ ssh va-boutique "pg_dump -U postgres -h localhost -C --column-inserts vadb" > vadbdump_"$(date '+%Y-%m-%d')"_inserts
# option: create column inserts

# use '>' instead of '>>' to avoid writing to the end if file is not empty
```
---

## Cron Jobs
For now all I have to do is to setup a cron job on server to run `django-archive` once a week and on the same day pull/send the archive to local machine
1. create a script file: archive.sh
    ```bash 
    #!/bin/bash
    source .bashrc
    source /path/to/venv/bin/activate
    python /path/to/manage.py archive
    ``` 
2. make it executable:
    ```bash
    $ chmod -rx archive.sh
    ```
3. set up cron command: `$ crontab -e`

    Go to [crontab guru](https://crontab.guru/) for checking the crontab timer:
    ```bash
    # “At 07:00 on Wednesday.”
    0 7 * * 3 /path/to/archive.sh
    ```
---
---
## So basically, in a word, run this locally every Wednesday:
Because of the time difference between Moscow and China, better run this after mid-day in Beijing Time:
```bash
$ rsync -avz --progress --remove-source-files -e ssh va-boutique:/home/don/VA-boutique/_backups/_archives/"$(date '+%Y-%m-%d')".tar.bz2 /Users/peiwen_li/Documents/GitHub/VA-boutique/_backups/_archives && ssh va-boutique "pg_dump -U postgres -h localhost vadb" > /Users/peiwen_li/Documents/GitHub/VA-boutique/_backups/_pgdump/vadbdump_"$(date '+%Y-%m-%d')" && ssh va-boutique "pg_dump -U postgres -h localhost -C --column-inserts vadb" > /Users/peiwen_li/Documents/GitHub/VA-boutique/_backups/_pgdump/vadbdump_"$(date '+%Y-%m-%d')"_inserts
```
---
---

# My commands for postgres db restoration
Recorded how I set up local postgres database and take the database backup on server to replicate it locally.
Three parts: local pg db setup (Mac OS), get the dump file and media file, run the restoration command

## Step 1: Setting up local pg database
---

### 1. Homebrew! everything is automated
```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
That's all you have to do

---

### 2. Install Postgres
```bash
$ brew install postgresql
```
>start Postgres running, and make sure Postgres starts every time your computer starts up. Execute the following command:
```bash
$ pg_ctl -D /usr/local/var/postgres start && brew services start postgresql
```
Check what version is running:
```bash
$ postgres -V
```

---

### 3. configuration
* go in postgresql commandline
    ```bash
    $ psql postgres
    ```
* change password of the superuser
    ```postgres
    postgres=# \password
    ```
* create a db user for your application
    ```postgres
    postgres=# CREATE ROLE db_user_name WITH LOGIN PASSWORD 'quoted_case_Sensitive_password';
    ```
    #### You can use the newly created user to create database by switching to this user:
    ```bash
    $ psql postgres -U db_user_name
    ```
* create a database
    ```postgres
    postgres=# CREATE DATABASE db_name [OPTIONS];
    ```
    create a database with options, e.g.
    ```postgres
    postgres=# CREATE DATABASE db_name WITH ENCODING='UTF8' LC_CTYPE='ru_RU.UTF-8' LC_COLLATE='ru_RU.UTF-8' OWNER=postgres TEMPLATE=template0;
    ```
* grant all privileges to the user
    ```postgres
    postgres=# GRANT ALL PRIVILEGES ON DATABASE db_name TO db_user_name;
    ```
* connect to the database
    ```postgres
    postgres=> \connect db_name
    ```

---

## Step 2: Now get the backup files ready
* Get the media files from `django-archive`, and put them into media root/folter
* Get the dump file from server as well

---

## Step 3: Restoration
This is the easy part, run command
```bash
$ psql dbname < path/to/dumpfile_backup
```
# ALL DONE!!

---

refs for restoration:
* [Getting started with PostgreSQL on Mac OSX](https://www.codementor.io/@engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb) - Patrick Sears: library to install 'brew'
* [How to start a postgresql server on mac osx](https://dataschool.com/learn-sql/how-to-start-a-postgresql-server-on-mac-os-x/) - latest info for running on postgresql app