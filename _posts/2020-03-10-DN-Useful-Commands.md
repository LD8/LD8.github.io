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
    # dir name: 2020-03-10
    $ mkdir "$(date '+%Y-%m-%d--%H-%M-%S')"
    # dir name: 2020-03-10--11-03-01

    $ mkdir "$(TZ=UTC-8  date '+%Y-%m-%d--%H-%M-%S')"
    # dir name: 2020-03-10--13-10-59 (Beijing time)
    $ mkdir "$(TZ=UTC-0  date '+%Y-%m-%d--%H-%M-%S')"
    # dir name: 2020-03-10--05-11-05 (Greenwich time)
    ```
* RSYNC a remote file to local through pipe
    ```bash
    # rsync a remote file to local
    $ rsync -avz -e ssh user@ip_add:~/remote_dir/file local_dir/
    # '-e' is essential for creating an ssh connection
    ```

* check mac environment variable
    ```bash
    $ printenv
    ```
    check a specific variable
    ```bash
    $ echo $variable_name
    ```
    If setting more permanent environment variables:
    >For system-wide operations, it should be in `/etc/profile`  
    >For user based operations, it should be in `~/.bash_profile`  
    >For non-login interactive shells, it should be in `~/.bashrc`   
    >For better understanding, you better check out this: [Unix Introduction — Shell](https://medium.com/@youngstone89/unix-introduction-shell-980212852897)  
* Look for files: `whereis` 
    ```bash
    $ whereis python3
    /usr/bin/python3
    ```

* locale
    ```bash
    # check your locale setting
    $ locale
    # update it
    $ sudo update-locale LANG="en_US.UTF-8" LANGUAGE="en_US.UTF-8"
    ```

* make nested dir
    ```bash
    $ mkdir -p _backups/_archives
    ```
--- 

## vim basic
```bash
输入：
i (insert, start editing)
: w filename （将文章以指定的文件名filename保存）
: wq (输入「wq」，存盘并退出vim)
: q! (输入q!， 不存盘强制退出vim)
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
* ### **More rsync commands: [checkout this post](https://www.tecmint.com/rsync-local-remote-file-synchronization-commands/)**
* ### **More rsync flags: [checkout this post](https://www.linuxtechi.com/rsync-command-examples-linux/)** <-- This post documented better with clearer appearance
