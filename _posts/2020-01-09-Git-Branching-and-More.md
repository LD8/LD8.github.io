---
layout: post
---
# git branch
I gather that if you are collaborating on the same project, everyone needs to work on a branch of their own master branch, so that when someone pull from your master branch, they won't get a half finished work. Meanwhile, when you push to remote origin, push from the local master branch which should always be stable, tested and working.

```bash
$ git branch
# checkout the status of branches, which branch are you currently on

$ git branch new_branch_name
# create a new branch

$ git branch -d branch_name
# soft delete a branch, i.e. if unmerged info, won't delete

$ git branch -D branch_name
# hard delete a branch, i.e. delete anyway

$ git branch -r
# show remote branches, you might have more than one remote other than origin
```

# [Undoing Changes](https://www.atlassian.com/git/tutorials/undoing-changes)
Depending on different situations, different undoing methods can be implemented. It seems that the pattern is always check the commit hash you want to reverse to, and then figure out which way is the most appropirate for that specific situation.

```bash
$ git log branch_name
# check the commit history on this branch, default current branch

# then you have several way s to undo changes
# 1: checkout --> checkout -b
$ git checkout older_commit_SHA_identifier
$ git checkout -b branch_name
# go back to the old commit point and create a new branch based on it, then switch to that branch

# 2: checkout --> revert HEAD
$ git checkout older_commit_SHA_identifier
$ git revert HEAD
# create a new commit at the HEAD of the branch based on an older commit point

# 3: reset
$ git reset
# unstage any uncommited files
$ git reset older_commit_hash
# reset back to the older commit point
# and because you've reset to an older commit, you have to force push to utilise the older version
$ git push --force ... (or $ git push -f)
# this can be dangerous in collabrations, people might pull your reset version and destroy their more advance version

# 4: reset the reset
$ git reflog
# find out all of the HEADs, should return something like this:
# 784350e HEAD@{1}: commit: minor settings twik
# 003ac4f HEAD@{2}: commit: adding heroku url in learning_logs/url.py file
# a7cd631 HEAD@{3}: commit: migration made
$ git reset --hard HEAD@{1}
# this restore the reset to the most advanced HEAD
```

# git log

```bash
$ git log
# show all the commits on current branch

$ git log --oneline
# show commit history in one line, the SHA-1 identifier would be shorter as well

$ git log branch_name
# show commit history on this branch
```

# git checkout
1. switching to/checkout other branches
2. checkout older commits after  `git log` getting the commit unique SHA-1 identifying hash

```bash
$ git checkout branch_name
# switch to some other branch

$ git checkout remote_name/branch_name
# switch to a branch of some other remote

$ git checkout coworkers/feature_branch
	Note: switching to '698ed8d'.

	You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

	If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

	Or undo this operation with:

  git switch -

	Turn off this advice by setting config variable advice.detachedHead to false

	HEAD is now at 698ed8d Initial commit

# you can either
$ git checkout -b branch_name
# or
$ git switch -c branch_name
# work the same way, creating a branch based on current commit
# this command also works as a creation command of a new branch and switch current branch to the new created branch

$ git checkout -b new_branch an_existing_branch
# this way, you create and switch to a new branch based on an existing branch, instead of the default current_branch

$ git checkout commit_SHA_hash
# see an older commit, which any edition won't affect current branch HEAD, and you are in a 'detached HEAD' state
# however, always remember to switch back to the master HEAD, then keep developing your project
$ git checkout master
```

It is recommended to create branches before editing based on any older commits instead of editing and commiting directly onto the older commit which will lead the commit to be orphaned which will be cleaned by the cleaning machanism by git every 30 days automatically. When branching out an older commit won't prompt the same issue.

# [git reset](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset)

```bash
$ git reset
# after staging files for commit, git reset can unstage the files, is the same as
$ git reset --mixed

$ git reset --hard
# DANGEROUSly unstage and discard any changes in files, i.e. staging index as well as working directory are both reset

$ git reset --hard HEAD~2
# 'HEAD~2' indicates to reset backwards 2 commits
```

# [git rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)

You can make changes on any branches however, it depends on which branch you commit your changes to, i.e. whichever branch is the active one when you commit. For example, you have branch A and master branch. If you are on your master branch, `git rebase branch_a` will only move the files on branch_a which doesn't exist on the master branch, to master branch.

```bash
$ git rebase from_branch_name to_branch_name
```
* git rebase -- d means during playback the commit will be discarded from the final combined commit block.
* git rebase -- p leaves the commit as is. It will not modify the commit's message or content and will still be an individual commit in the branches history.
* git rebase -- x during playback executes a command line shell script on each marked commit. A useful example would be to run your codebase's test suite on specific commits, which may help identify regressions during a rebase.

# git fetch
a command to download some contents from registered url (i.e. a remote)

```bash
$ git fetch remote_name branch_name
# **fetch data from remote_name, but isolate the data from local data, you can merge the fetched data with local data manually**

$ git pull remote_name
# **fetch and merge in one go**
```
therefore:
**`git fetch` is a _soft_ version of `git pull`**

# git pull

```bash
$ git checkout branch_name
# although often default, you are already working in the branch you want it to be fetch and merge into, otherwise better switch to the branch you want to pull(fech and merge) the remote data

$ git pull remote_name
# fetch remote data and merge with local data
# same as:
$ git fetch remote_name
$ git merge origin/current_branch
# pull == fetch && merge

$ git pull --rebase remote_name
# replace local data with fetched remote data
# same as:
$ git fetch remote_name
$ git rebase -i origin/master
# 'pull --rebase' == 'fetch' && 'rebase -i'
# adding '-i' allows you to run rebase interactively

$ git config --global branch.autosetuprebase always
# changes the config file to run --rebase flag everytime when git pull

$ git pull --verbose remote_name
# displays the content being downloaded and the merge details

$ git pull --no-commit remote_name
# fetches the remote content but does not create a new merge commit
```

# git push
an 'upload' command, its counterpart is 'pull' and 'fetch'

```bash
$ git push remote_name branch_name
# push this branch to this remote, it's like running 'git merge master' from inside the remote repo

$ git push remote_name --all
# push all local branches to this remote

$ git push remote_name --force
# 慎用 force remote data to be the same as your local's

$ git push remote_name :branch_name
# to delete a branch from a remote
# and to delete the corresponding local branch
$ git branch -D branch_name
```

# git merge

```bash
$ git merge master test-2
# merge master branch into test-2 branch WHEN test-2 branch is active(being checked out)
```

# git commit

```bash
$ git commit --amend
# update the previous commmit (often used to alther the previous commit message or add more changes), and then:
$ git push --force origin master
# at this point if it's not forced, git push will fail
```

# git remote

`git remote` is a way of linking other urls to your local repo, give it a name so in the future you don't have to look for the urls again, also you can track all the changes...
```bash
$ git remote
# show all the links linking to your local repo

$ git remote -v
# verbose version of git remote, listing all the urls linking to your local repo

$ git remote add remote_name remote_url
# adding a remote url to local repo with a remote name, so you can easily  pull/fetch to gain access to the url with its remote name, much more convenient

$ git remote rm remote_name
# remove a remote

$ git remote rename old_remote_name new_remote_name
# rename from old to new

$ git remote show remote_name
# inspect the remote, including its urls, branches, etc.
```

# [git clean](https://www.atlassian.com/git/tutorials/undoing-changes/git-clean)
`git clean` is a convenience method for deleting untracked files in a repo's working directory.
