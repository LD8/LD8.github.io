---
layout: post
title:  "Daily Notes: Useful Commands"
categories: Daily Notes
---



```bash
# update a pip package
$ pip install --upgrade django-mailer==2.0.1
# pip <command> <option> <package-name>==<version.number>

# create a directory with this date:
$ mkdir "$(date '+%Y-%m-%d')"

# rsync a remote file to local
$ rsync -avz -e ssh va-boutique:/remote_dir /local_dir
# '-e' is essential for creating an ssh connection

# cron tab to backup

# first create backup using django-dbbackup
# on server
$ cd /home/don/VA-boutique/
$ source venv/bin/activate
(venv)$ python manage.py dbbackup
# creation of backup in a folder /var/backup/

# pull backup from source weekly
$ mkdir "$(date '+%Y-%m-%d')"
$ rsync -avz -e ssh va-boutique:/home/don/VA-boutique/var/backup /"$(date '+%Y-%m-%d')"/
```

