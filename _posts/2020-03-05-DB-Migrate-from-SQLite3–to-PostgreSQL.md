---
layout: post
title:  "DATABASE: Migrate from SQLite3 to PostgreSQL"
categories: Database
---

# Objectives
Two databases were setup on server, `sqlite` is for development, `PostgreSQL` is for production. It is probably a stupid idea in the first place: because of an environment variable error, `sqlite` was still being used in production and user has been uploading data (files and pictures) for a while. I now need to change the database to `PostgreSQL` on the server without losing the data.

---

## **Local**: Mac OS 10.15.3
* Goal: Test restore data from sqlite to Postgresql
* To dos:
    * research how to restore data from sqlite
    * install necessary tools
    * restore data and runserver locally, see if data applied
    * move on to phase II: On server - migrating data from sqlite to Postgresql
* Tools:
    * [PostgreSQL Database](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads) install on Mac OS
    * [pgAdmin](https://www.pgadmin.org/) install on Mac OS
    * [pgloader](https://pgloader.readthedocs.io/en/latest/intro.html)



## Installation:
I used the django default `sqlite3` for development, therefore, `PostgreSQL` is not installed locally. The process of this installation is different on Mac compared to that on Ubuntu:

Download on Mac OS:
* [PostgreSQL Database](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)
* [pgAdmin](https://www.pgadmin.org/): a PostgresSQL tool for database management
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

Reference articles:
* pgloader official doc: [Migrating an SQLite database to PostgreSQL](https://pgloader.readthedocs.io/en/latest/ref/sqlite.html#sqlite-database-source-specification-from)
* 

---

# Phase II: On server - migrating data from sqlite to Postgresql

something to do in phase II goes here

---








## [PGloader](https://pgloader.readthedocs.io/en/latest/intro.html)
```bash
# on Ubuntu server, install PGLoader
$ sudo apt-get update -y
$ sudo apt-get install -y pgloader

$ sudo -u postgres pgloader db.sqlite3 postgresql:///va
# after this the website is not accessable, error: "ProgrammingError at / permission denied for relation"

# because the public tables haven't grant access to the project user:
postgres=> GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO myprojectuser;

# now the website is accessable, however, only partial db data is transferred:
$ 
```

* ## [PostgreSQL packages for Debian and Ubuntu](https://wiki.postgresql.org/wiki/Apt)
    ```bash
    $ sudo apt update
    $ sudo apt install pgloader
    # Normal the above code can get you the latest version of the package (in this case `pgloader`) installed in Ubuntu, However,
    ```
    I couldn't update `pgloader` from 3.4 to 3.6 so I tried the method to update the installation list:  
    Quickstart and News, how to install the latest packages of your distribution  
    Now the `pgloader` is v3.5, howver the latest is v3.6, not sure if this is working but worth to try