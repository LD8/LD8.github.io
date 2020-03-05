---
layout: post
title:  "DATABASE: PostgreSQL Backup and Restore"
categories: Database
---
You can always check the [official Doc](https://www.postgresql.org/docs/12/backup.html) for more of this subject.
## [pg_dump](https://www.postgresql.org/docs/12/app-pgdump.html)  
```bash
$ pg_dump dbname > dumpfile

# The ability of pg_dump and psql to write to or read from pipes makes it possible to dump a database directly from one server to another, for example:
$ pg_dump -h host1 dbname | psql -h host2 dbname
```


## Restoring the Dump  
```bash
$ psql dbname < dumpfile

# By default, the psql script will continue to execute after an SQL error is encountered. You might wish to run psql with the ON_ERROR_STOP variable set to alter that behavior and have psql exit with an exit status of 3 if an SQL error occurs:
$ psql --set ON_ERROR_STOP=on dbname < dumpfile
```


