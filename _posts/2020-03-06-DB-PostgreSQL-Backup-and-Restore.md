---
layout: post
title:  "DATABASE: PostgreSQL Backup and Restore"
categories: Database
---
## [pg_dump](https://www.postgresql.org/docs/12/app-pgdump.html)  
```bash
# on server
$ sudo -u postgres pg_dump dbname > dumpfile
```
>The ability of pg_dump and psql to write to or read from pipes makes it possible to dump a database directly from one server to another, for example:
```bash
# from one host to another
$ pg_dump -h host1 dbname | psql -h host2 dbname
```
### `pg_dump` remotely to local








## Restore 
```bash
$ sudo -u postgres psql dbname < dumpfile
```
> By default, the psql script will continue to execute after an SQL error is encountered. You might wish to run psql with the ON_ERROR_STOP variable set to alter that behavior and have psql exit with an exit status of 3 if an SQL error occurs:
```
$ sudo -u postgres psql --set ON_ERROR_STOP=on dbname < dumpfile
```

You can always check the [official Doc](https://www.postgresql.org/docs/12/backup.html) for more on this subject.

## crontab -e


```bash
$ rsync -avz --progress --remove-source-files -e ssh va-boutique:/home/don/VA-boutique/2020-03-05--18-14-14.tar.bz2 __web_backup__/
```