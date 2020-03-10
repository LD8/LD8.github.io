---
layout: post
title:  "DATABASE: PostgreSQL Backup and Restore (remote)"
categories: Database
---
### First, the basics of backing up PostgresSQL db. Then I'll record how I back up my own database on server for future reference.
# The Basics
## [pg_dump](https://www.postgresql.org/docs/12/app-pgdump.html) command
```bash
# on server
$ sudo -u postgres pg_dump dbname > dumpfile
```
## Restore 
```bash
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
$ rsync -avz --progress --remove-source-files -e ssh va-boutique:/home/don/VA-boutique/_backups/_archives/"$(date '+%Y-%m-%d')".tar.bz2 /Users/peiwen_li/Documents/GitHub/VA-boutique/_backups/_archives && ssh va-boutique "pg_dump -U postgres -h localhost vadb" > vadbdump_"$(date '+%Y-%m-%d')" && ssh va-boutique "pg_dump -U postgres -h localhost -C --column-inserts vadb" > vadbdump_"$(date '+%Y-%m-%d')"_inserts
```
ALL DONE!!