---
layout: post
title:  "Daily Notes: Mosh Setup"
categories: Daily Notes
---

I couldn't stand the lagging on SSH anymore. In order to have a better connection with the VPS, I decided to use `Mosh`.

Official Site: https://mosh.org/

## Installation

* Install mosh locally/client side: you can download the package for your OS on the website or you can use package control whichever suits you better.

* Server side, I'm on Ubuntu 18.04 LTS for my VPS so here's what I did:

  ```bash
  $ sudo apt-get install mosh
  # install it first
  
  # or if your Ubuntu doesn't come with updated apt:
  # $ sudo apt-get install python-software-properties
  # $ sudo add-apt-repository ppa:keithw/mosh
  # $ sudo apt-get update
  # $ sudo apt-get install mosh
  
  # firewall config, support for mosh:
  $ sudo ufw allow 60000:61000/udp
  
  # then you can check your firewall status:
  $ sudo ufw status
  ```

  [more info: an article on digitalocean.com](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-mosh-on-a-vps)

Now you can connect to VPS, still on SSH, but the command becomes: `mosh user@server_ip_address` instead of `ssh ...`

## **Troubles?**

I ran into a problem after `mosh` command:

```bash
The locale requested by LC_CTYPE=UTF-8 isn't available here.
Running `locale-gen UTF-8' may be necessary.
```

If you have the same issue, here's an easy fix:

```bash
# run this code on both your client/local and server:
$ export LC_ALL="en_US.UTF-8"
```

I tried `$ export LC_ALL="en_CA.UTF-8"` and for some reason it doesn't work... Why does it have to be `US`??