---
layout: post
title: Easiest Way to Connect Local Folder to GitHub
--- 
New to GitHub, still trying to figure out the mechanism and here're some notes re _how to connect local folder with a repo and vice versa_:

### 1. Create a repository
It is recommended to create a repository directly from GitHub.com by using the beautiful and minimal UI provided. So do that before anything else.

### 2. Open local terminal
Now, there are two ways to connect a local folder to a repo on the site:
* clone --> copy & paste locally --> add --> commit
* 2.2 init --> add --> commit --> git remote origin `<URL>` --> pull --> push

#### 2.1 clone --> copy & paste locally --> add --> commit

```shell
# get the empty repository you just created to a local folder 
# whose name is automatically set to be the same as your repo
$ git clone <URL>

# Here is when you copy and paste the stuff you want to upload to your repo

# adding/staging the files before commit
$ git add .

# just to check if everything has been added
$ git status

# commit
$ git commit -m 'comment goes here'

# push
$ git push origin master
# or simply git push
```
Note: `<URL>` can be HTTP url or SSH, using SSH you need to set up SSH Key first, [just in case you don't know how](https://ld8.github.io/blog/SSH_Key_Set_Up/).

You can also just use `$ git commit -a -m 'comment'` without having to `git add .` if you don't have completely new files in the folder.

#### 2.2 init --> add --> commit --> git remote origin URL --> pull --> push
This is slightly more difficult and sometimes doesn't work if you are not careful.
And also assuming you have had [git installed](https://git-scm.com/downloads).
```shell
# create a local folder which ideally should be the same as the repo name you just created
$ mkdir <REPONAME>

# Change Directory into this folder
$ cd <REPONAME>

# make it a git monitored folder, assuming you've already got git installed
$ git init

# adding/staging the files before commit
$ git add .

# just to check if everything has been added
$ git status

# commit
$ git commit -m 'comment goes here'

# set the remote destination to which the local folder is expected to connect
$ git remote origin <URL>

# pull request: make sure no conflict with the remote repo, basically all of the files on the repo have to be local first, it is recommended to git pull before git push. In fact, it'll prompt error if you have any files in the repo only and not local
$ git pull

# push
$ git push origin master
```
Note: 
* `<URL>` can be HTTP url or SSH, using SSH you need to set up SSH Key first, [just in case you don't know how](https://ld8.github.io/blog/SSH_Key_Set_Up/).
* if you `git remote origin` set to a wrong url, you can use `git remote set-url <URL>` to reset it to the correct one

#### I would recommend the 2.1 to complete newcomers.