---
layout: post
title:  "Daily Notes: Useful Commands"
categories: Daily Notes
---
### Commands are difficult to remember, and easy to forget, so here's a common command repository
* UPDATE pip packages
    ```bash
    $ pip install --upgrade django-mailer==2.0.1
    # pip <command> <option> <package-name>==<version.number>
    ```
* COPY file from one dir to another
    ```bash
    $ cp -i path/to/file.py path/to/dir/
    # -i is for interactive, will be asked to replace any files
    ```
* CREATE a directory with this date:
    ```bash
    # create a directory with this date:
    $ mkdir "$(date '+%Y-%m-%d')"
    ```
* RSYNC a remote file to local
    ```bash
    # rsync a remote file to local
    $ rsync -avz -e ssh user@ip_add:~/remote_dir/file local_dir/
    # '-e' is essential for creating an ssh connection
    ```
---
# Ubuntu Commands
* ### uninstall a package and remove its dependencies completely
    ```bash
    $ sudo apt-get purge --auto-remove packagename
    ```
* ### check out the packages installed
    ```bash
    $ dpkg --list
    ```
* ### switch user
    ```bash
    $ su username
    ```
--- 

## [The Many Uses of Rsync](https://mediatemple.net/blog/tips/many-uses-rsync/)
>It’s faster than scp (Secure Copy) because rsync uses remote-update protocol which allows to transfer just the differences between two sets of files. First time, it copies the whole content of a file or a directory from source to destination but from next time, it copies only the changed blocks and bytes to the destination.
```bash
# basic format
# $ rsync <options> <source> <destination>

$ rsync -av path/to/directory1/ /path/to/directory2/ 
$ rsync -av path/to/directory1 /path/to/directory2/ 
# there's a difference: copy the files in that folder or the folder itself
```
### **The flags/options**:
* -a: archive mode, archive mode allows copying files recursively and it also preserves symbolic links, file permissions, user & group ownerships and timestamps
* -v: As with many other commands, this option asks for verbose output. This is especially useful when copying large amounts of data.  
* -r : copies data recursively (but don’t preserve timestamps and permission while transferring data
* -z : compress file data
* -h : human-readable, output numbers in a human-readable format
* –delete: This flag isn’t used here but it is a common feature of rsync. This option deletes any files or folders in the destination that aren’t at the source. Use with extreme caution!
* -h or –help: This prints a help page that has useful information about using rsync.

### **zip, copy remotely, remove source files**
```bash
# use tar command to archive the files, django-archive is used to backup databases in my project
$ tar -zcvf backup1.tar.gz path/to/files/
# or create with a timestamp
$ tar -zcvf "$(date '+%y-%m-%d').tar.gz" path/to/files/

$ rsync -avz --remove-source-files path/to/backup1 user@ip_add:/path/to/backups/
# with a timestamp
$ rsync -avz --remove-source-files "$(date '+%y-%m-%d').tar.gz" user@ip_add:/path/to/backups/

```
### **crontab -e**
```nano
* * * * * rsync -avz path/to/directory1/ user@ip_add:/path/to/directory2/
```

* ### **More rsync commands: [checkout this post](https://www.tecmint.com/rsync-local-remote-file-synchronization-commands/)**
* ### **More rsync flags: [checkout this post](https://www.linuxtechi.com/rsync-command-examples-linux/)** <-- This post documented better with clearer appearance

### locale
```bash
$ locale

$ sudo update-locale LANG="en_US.UTF-8" LANGUAGE="en_US.UTF-8"
```
---


```bash
# cron tab to backup

# first create backup using django-archive
# on server
$ cd /home/don/VA-boutique/
$ source venv/bin/activate
(venv)$ python manage.py archive

# pull backup from source weekly
$ mkdir "$(date '+%Y-%m-%d')"
$ rsync -avz -e ssh --progress va-boutique:/home/don/VA-boutique/var/backup "$(date '+%Y-%m-%d')"/
```

