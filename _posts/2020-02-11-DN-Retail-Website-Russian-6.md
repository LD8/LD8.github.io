---
layout: post
title:  "Daily Notes: Retail Website in Russian P6"
date:   2020-02-11 19:49:17 +0800
categories: Daily Notes
---

## Django:
---
### delete one table in django sqlite: [SO comment](https://stackoverflow.com/a/17041402/11901269)
```bash
$ python manage.py dbshell
# if you do not know which table to delete/drop, run:
sqlite > SELECT * FROM sqlite_master WHERE type='table';
# if you already know:
sqlite > DROP TABLE appname_modelname;

# DO NOT FORGET the semi-colon at the end, otherwise it will prompt '...>' signalling the expression is unfinished
```

## Bootstrap
---
### to override default
simply add an id to body tag
```html
<body id="bootstrap-override">
```
and refer to this id when overriding default css