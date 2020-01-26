---
layout: post
title: SSH Key Set Up Step by Step
--- 
This is my first time to push a repo without using an http link so I need to generate a new SSH key.
Followed the [instruction](https://help.github.com/en/enterprise/2.17/user/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) on github:

### Step 1 create a new ssh key, using the provided email as a label
```shell
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Press Enter when:
> Enter a file in which to save the key (/Users/you/.ssh/id_rsa):
# which accepts the default file location which is ~/.ssh/ folder

# Type a secure passphrase
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```
Note: What's a [passphrase](https://help.github.com/en/enterprise/2.17/user/authenticating-to-github/working-with-ssh-key-passphrases)

### Step2: Add your SSH key to the ssh-agent
This is essentially a step to let mac know that you've got a key and it's at your disposal.
```shell
# start the ssh-agent
$ eval "$(ssh-agent -s)"
> Agent pid <somenumbers>

# create a config file to load keys into the ssh-agent and store passphrases in your keychain automatically
$ echo "Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_rsa" > ~/.ssh/config
  
# Add the SSH PRIVATE key to the ssh-agent and store the passphrase in the keychain manually
$ ssh-add -K ~/.ssh/id_rsa
```
To be honest, I'm not quit sure why you have to add the SSH key to ssh-agent when you've already created a config file which automatically does that... Is it because the last step is to add the PRIVATE Key explicitly?

### Step 3: Add the new SSH key to your Github account
This is quit straight forward. Follow the steps [here on GitHub official Help](https://help.github.com/en/enterprise/2.17/user/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account).

Windows and Linux user please refer to the [instruction](https://help.github.com/en/enterprise/2.17/user/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) on github..