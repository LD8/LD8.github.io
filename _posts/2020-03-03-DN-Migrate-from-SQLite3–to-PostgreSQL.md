---
layout: post
title:  "Django Deployment: Migrate from SQLite3 to PostgreSQL"
categories: Daily Notes
---
## [PGloader](https://pgloader.readthedocs.io/en/latest/intro.html)
```bash
# on Ubuntu server, install PGLoader
$ sudo apt-get update -y
$ sudo apt-get install -y pgloader


postgres-# load database
postgres-#   from sqlite:///home/don/VA-boutique/2020-03-03--09-51-25.tar.bz2
postgres-#   into postgresql:///va
postgres-#  with include drop, create tables, create indexes, reset sequences
postgres-# 
postgres-#   set work_mem to '16MB', maintenance_work_mem to '512 MB';
ERROR:  syntax error at or near "database"
LINE 1: load database
```

