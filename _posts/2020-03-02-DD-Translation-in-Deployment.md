---
layout: post
title:  "Django Deployment: Translation Error in Deployment"
categories: Django Deployment
---

## Django.mo? Partial Translation When deployed

When deploying my django app, all the translation works perfectly locally. I translated everything in `django.po` and `python manage.py compilemessages` locally. However, it doesn't work somehow on server. Only part of the website is translated on VPS.

* ### At first, Ubuntu 18.4 prompt error when I tried to compile:

  ```bash
  (venv)$ django-admin makemessages -l ru -i venv
  # DO NOT forget to ignore virtualenv folder
  (venv)$ django-admin compilemessages
  # CommandError: Can't find msgfmt. Make sure you have GNU gettext tools 0.15 or newer installed.
  
  # easy fix:
  $ sudo apt-get update
  $ sudo apt-get install -y gettext libgettextpo-dev
  
  # Then you can run command compilemessages, however, the website is still partly in English, didn't help... so what went wrong?
  ```
* ### DO NOT forget to delete the `fuzzy` tag in `.po` file, for everything to be properly displayed/translated
  
* ### It turned out I needed to restart the server:

  ```bash
  $ sudo service gunicorn restart
  ```

## Now everything will be applied, and it only takes a second!
