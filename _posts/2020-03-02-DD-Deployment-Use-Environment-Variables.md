---
layout: post
title:  "Django Deployment: Use environment variables"
categories: Django Deployment
---
# [Benefits of environment variables](https://hyperlane.co/blog/the-benefits-of-environment-variables-and-how-to-use-them)

- Easy configuration
- Better security
- Fewer production mistakes
### Question: Does it matter the value of 'USE_PROD_DB' is capitalised? [SO Answer](https://stackoverflow.com/questions/60482390/django-production-development-migrations-and-databases-mixed-up/60482777?noredirect=1#comment106998304_60482777)

> It does not make any difference, because when you use `os.environ.get()` you will get a string, rather than a boolean value, you can consider using django environ instead to parse boolean value directly from environment variable. 

# Usage 
* ## Use environment variables to set which DATABASE to apply locally and on server, 3 steps:
    1. Installation - [django-dotenv](https://pypi.org/project/django-dotenv/): 
        ```bash
        (venv)$ pip install django-dotenv
        ```
    2. setting up `django-dotenv`:
        * `manage.py` and `wsgi.py`(same dir as `settings.py`)
            ```python
            from dotenv import load_dotenv
            # note that this should be the folder path to your project foler on server
            project_folder = os.path.expanduser('~/myproject')
            load_dotenv(os.path.join(project_folder, '.env'))
            ```
            In `manage.py`, the code above should live inside of `main()` function  

            ---
        * In `settings.py`:
            ```python
            # if running locally (No 'USE_PROD_DB' env var got)
            if not os.environ.get('USE_PROD_DB', None):
                DATABASES = {
                    'default': {
                        'ENGINE': 'django.db.backends.sqlite3',
                        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
                    }
                }
            # if running on server
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
    3. **On server**: create a `.env` file under the same folder as `manage.py`
        ```bash
        user@ip_name:~/myproject$ echo 'export USE_PROD_DB=true' > .env
        ```
        This should create a file called `.env` under the correct foler. Django will load this file, and the variables defined in this file will be and _only_ be exported into current OS environment. Therefore, `settings.py` can catch the variable by running `os.environ.get()` or `os.getenv()`.

### So after these 3 steps, you can simply add a conditional statement in your python files to determine where some certain codes are running.

* ## add passwords in `.env` file
    For security purposes, exporting password in `.env` file and load them in wherever they need from `os` (by calling `os.environ.get('VAR')` or `os.getenv('VAR')`) would be a good idea in production.

