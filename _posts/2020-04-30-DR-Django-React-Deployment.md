---
layout: post
title: "React Django Deployment"
categories: React Django
---

This is a post about React Django Deployment on VPS Ubuntu LTS 18.04. I couldn't find any detailed guide or tutorials for newbies so I guess sharing my experience is the best I can do for now.

## GitHub pull

Basically, everything is up in the repo. All you need is to pull/clone your repo into your Ubuntu directory. Install virtual environment for Django, run `pip install -r requirements.txt`, go to your `frontend` app where your frontend React files live and run `npm install`.

But before that you might need to install `node.js` and `npm`, which should have been a piece of cake however, Ubuntu didn't make it easy.

## Install `node` and `npm` on Ubuntu 18.04

The node.js version on Ubuntu is quite old (8+). So follow these steps:

```bash
$ sodu apt install curl
```

install `curl` and download and execute the Node.js 10.x installer:

```bash
$ curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
```

Then install the package. This will automatically install `npm` as well. Thanks for the [post](https://joshtronic.com/2018/05/08/how-to-install-nodejs-10-on-ubuntu-1804-lts/) by Josh Tronic.

```bash
$ sudo apt install nodejs
```

Check your version by typing

```bash
$ node -v
v10.20.1
$ npm -v
6.14.4
```

## Notes on deployment

- migrattion: remember to ignore migration files when you FIRST deploy the Django app because if you do not and you've made many migrations locally for the mistakes you made during the development process: when deploying the app, migration changes can not be detected, hence no migrations will be made.
- imported image urls: somehow after webpack compiles the js files to `main.js`, Django `collectstatic` command copies all the necessary static files into the `static` folder in root folder, HOWEVER, the url for those imported images (`import BG from './assets/img/bg.jpg'`) in js files can not maintain the correct link (to `http://www.domain.com/static/img/bg.jpg`), instead, it links to `http://www.domain.com/bg.jpg`. I still can't figure out whether it's a webpack.config issue or python settings issue...
- `main.js` is 700kb it's apparently too slow to load from a Russian VPS server to local (China). I'll try to split the file ([source](https://webpack.js.org/guides/code-splitting/))
