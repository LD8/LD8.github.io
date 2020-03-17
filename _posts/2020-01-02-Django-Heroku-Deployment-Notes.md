---
layout: post
---
## Heroku installation to local
Register a heroku account. And then simply follow the instruction on [Heroku.com](https://devcenter.heroku.com/articles/heroku-cli) to install Heroku and Heroku CLI on your local computer

## Install required packages
```bash
# to manage the database that Heroku uses
(venv)$ pip install psycopg2==2.7.* 

# handles config of the completed app to run properly on Heroku servers
(venv)$ pip install django-heroku 

# provides a server capable of serving apps in a live environment
(venv)$ pip install gunicorn
```

## requirements.txt
```bash
(venv)$ pip freeze > requirements.txt
```

## runtime.txt
```bash
(venv)$ python --version
Python 3.7.4
(venv)$ echo 'python-3.7.4' > runtime.txt
```
runtime.txt specifies a python version for Heroku to use for your app. Note that the text in runtime.txt should be a one liner and all in lowercase. DO NOT FORGET THE 

## settings.py
```python
# Heroku settings
# Note: this settings of Heroku should go after BASE_DIR delaration
import django_heroku
django_heroku.settings(locals())

# securing the live project
if os.environ.get('DEBUG') == 'TRUE':
	DEBUG = True
elif os.environ.get('DEBUG') == 'FALSE':
	DEBUG = False
# now you can set environment variables on Heroku after pushing this to heroku master
```
this setting is easy to forget because you probably only need to set up once for each project.


## Procfile
```bash
(venv)$ echo 'web: gunicorn app_name.wsgi --log-file -' > Procfile
```
> This line tells Heroku to use gunicorn as a server and to use the settings in `project_name/wsgi.py` to launch the app. The log-file flag tells Heroku the kinds of events to log.

## Install Git
```bash
(venv)$ git --version
(venv)$ git config --global user.name "your_username"
(venv)$ git config --global user.email "user_email@example.com"
```

## .gitignore
```bash
(venv)$ echo 'venv' > .gitignore
```

## cmd+shift+.
to make hidden file visible

## committing your project before pushing to Heroku
```bash
# just in case you haven't done so
(venv)$ git init

(venv)$ git add .
(venv)$ git commit -am 'comments here'
# just to check everything is clear and ready to go
(venv)$ git status
```

## pushing to Heroku
```bash
(venv)$ heroku login
# to create a new app on heroku and a new url will be automatically generated which you can change later
(venv)$ heroku create

(venv)$ git push heroku master
# just like how you do it on github
```

## check status, migration and heroku bash
```bash
# check the heroku Project Status
(venv)$ heroku ps
=== web (Free): gunicorn learning_log.wsgi --log-file - (1)
# this is why you set up the weirdass _Procfile_

# migrate to create the database
(venv)$ heroku run bash
~ $ python manage.py migrate

# create super user
~ $ python manage.py createsuperuser
..snip..

# exit heroku bash
~ $ exit
```

## Renaming URL
```bash
# renaming the default silly url
(venv)$ heroku apps:rename ANY-NAME-YOU-WANT
```

## OPEN
```bash
# open the new website
(venv)$ heroku open
```
## setting environment variables on Heroku
```bash
(venv)$ heroku config:set DEBUG='FALSE'
```

## deleting a project on Heroku
```bash
(venv)$ heroku apps:destroy --app appname
```
