---
layout: post
title:  "Daily Notes: Deployment Troubleshooting and Configuration"
categories: Daily Notes
---

## Django.mo? Partial Translation When deployed

When deploying my django app, all the translation works perfectly locally. I translated everything in `django.po` and `python manage.py compilemessages` locally. However, it doesn't work somehow on server. Only part of the website is translated on VPS.

* At first, Ubuntu 18.4 prompt error when I tried to compile:

  ```bash
  (venv)$ django-admin makemessages -l ru -i venv
  (venv)$ django-admin compilemessages
  # CommandError: Can't find msgfmt. Make sure you have GNU gettext tools 0.15 or newer installed.
  
  # easy fix:
  $ sudo apt-get update
  $ sudo apt-get install -y gettext libgettextpo-dev
  
  # Then you can run command compilemessages, however, the website is still partly in English, didn't help... so what went wrong?
  ```

  

* It turned out I needed to restart the server:

  ```bash
  $ sudo service gunicorn restart
  ```

  Then everything will be applied, and it only takes a second!

---

## Use environment variables to set which DATABASE to apply

1. In `settings.py`:

```py
if not os.environ.get('USE_PROD_DB', None):
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        }
    }
else:
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'NAME': 'db_name',
            'USER': 'db_user',
            'PASSWORD': 'db_password',
            'HOST': 'localhost',
            'PORT': '',
        }
    }
```

2. On server:

   ```bash
   export USE_PROD_DB=true
   ```

**This basically means do something in this specific environment, and do the other in any other environment... ** judged by the environment variable 'USE_PROD_DB' exported on that environment

In this case: when it's not on server, use `sqlite3`, otherwise, use `postgresql_psycopg2`

### Question: Does it matter the value of 'USE_PROD_DB' is capitalised? [SO Answer](https://stackoverflow.com/questions/60482390/django-production-development-migrations-and-databases-mixed-up/60482777?noredirect=1#comment106998304_60482777)

> It does not make any difference, because when you use `os.environ.get()` you will get a string, rather than a boolean value, you can consider using django environ instead to parse boolean value directly from environment variable. 

[Benefits of environment variables](https://hyperlane.co/blog/the-benefits-of-environment-variables-and-how-to-use-them):

- Easy configuration
- Better security
- Fewer production mistakes

