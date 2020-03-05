---
layout: post
title:  "DATABASE: SQLite Backup and Restore"
categories: Database
---
You are going to witness: backup an SQLite database on the server, then download it, restore the data locally at the end. This practice is for me to gain confidence in backing up important data.

## **On Server**: Ubuntu 18.04
* Goal: To backup exisiting `sqlite3` database on server
* To dos:
    * research how to backup sqlite databases 
    * find and install necessary tools
    * backup database waiting to be transferred remotely
* Tools: 
    * [`django-archive`](https://pypi.org/project/django-archive/)==0.1.6 easy to use and setup
    * <s>[`django-dbbackup`](https://pypi.org/project/django-dbbackup/)==3.2.0</s> couldn't setup backup folder path correctly somehow


## **Local**: Mac OS 10.15.3
* Goal: Test restore sqlite data
* To dos:
    * research how to restore sqlite
    * install necessary tools
    * restore data and runserver locally, see if data applied
    * move on to phase II: On server - migrating data from sqlite to Postgresql

---

# Step 1: backup db.sqlite3 on server
## 1. Use `django-archive` to backup database as well as the files uploaded in `media` folder:
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

## 2. Use `.dump` to backup sqlite3 database:
Simply execute the code
```bash
$ sqlite3 db.sqlite3 .dump > db.dump
# this will backup the sqlite3 database called 'db.sqlite3' which is the default and create a file named 'db.dump' in current folder
```

### Now we have created 2 major files: 
1. `django-archive` file ensures that all the data is intact including the uploaded media files
2. `db.dump` file has all the information about the tables and admin logs etc.
### Download the file from server ==> 

---

# Step 2: Download the backup files
Use `rsync` function to download the backup files:
```bash
$ rsync -avz -e ssh user@ip_add:~/remote_dir/file local_dir/
# now be careful here, if you want to save your file into the target folder, there's no need to type slash before the target folder, otherwise it won't work if you don't have the writing privilege
receiving file list ... done
2020-03-03--09-51-25.tar.bz2

sent 38 bytes  received 12240068 bytes  21760.19 bytes/sec
total size is 12235494  speedup is 1.00
# it took me around 10 mins to receive a 12MB zip file
```

More on `rsync`:
* [The Many Uses of `Rsync`](https://mediatemple.net/blog/tips/many-uses-rsync/)
* More `rsync` commands: [checkout this post](https://www.tecmint.com/rsync-local-remote-file-synchronization-commands/)
* More `rsync` flags: [checkout this post](https://www.linuxtechi.com/rsync-command-examples-linux/) <-- This post documented better with clearer appearance

### Now the backup file is in your local folder, next ==>
---

# Step 3: restore backup
SQLite makes it super easy to restore the data. Once you have the `.dump` file, all you have to do is:
```bash
$ sqlite3 db_name < db.dump
```
Note that the `db_name` should not be the same as your current sqlite database name (default `db.sqlite3`), it will cause conflict import. 

Simply restore the data into a new database by using a new `db_name` in the code above. 

Then, change the `settings.py` file, hook database to your new `db_name`:
```python
DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(BASE_DIR, 'db_name'),
        }
    }
```
Done!!