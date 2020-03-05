---
layout: post
title:  "DATABASE: Migrate from SQLite3 to PostgreSQL"
categories: Database
---
## [PGloader](https://pgloader.readthedocs.io/en/latest/intro.html)
```bash
# on Ubuntu server, install PGLoader
$ sudo apt-get update -y
$ sudo apt-get install -y pgloader

$ sudo -u postgres pgloader db.sqlite3 postgresql:///va

postgres-# load database
postgres-#   from sqlite:///home/don/VA-boutique/2020-03-03--09-51-25.tar.bz2
postgres-#   into postgresql:///va
postgres-#  with include drop, create tables, create indexes, reset sequences
postgres-# 
postgres-#   set work_mem to '16MB', maintenance_work_mem to '512 MB';
ERROR:  syntax error at or near "database"
LINE 1: load database


postgres=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO myprojectuser;
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