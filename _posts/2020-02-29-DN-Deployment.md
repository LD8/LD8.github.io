---
layout: post
title:  "Daily Notes: Django Russian Retail Website Deployment"
categories: Daily Notes
---

## Finally the Deployment
**Before**:
	Don't forget the [official checklist](https://docs.djangoproject.com/en/3.0/howto/deployment/checklist/) before your deployment. And delete all migrations files in `migrations` folder except for `__init__.py` for a clean start.

**Initialisation**:

1. connect to the server with SSH: [setting up SSH](https://serversforhackers.com/c/configuring-ssh-locally)
2. update `apt` package index
	```bash
	$ sudo apt update
	```
3. install python: Ubuntu 18.04 came with python 3.6, you can verify this by typing:
	```bash
    $ python3 -V
    Python 3.6.9
	```
4. other files to install: `pip`, the Python development files needed to build Gunicorn later, the Postgres database system and the libraries needed to interact with it, and the Nginx web server:
	```bash
	$ sudo apt install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx curl
	```
5. upgrade pip and install virtual env
	```bash
	$ sudo -H pip3 install --upgrade pip
	$ sudo -H pip3 install virtualenv
	```
6. add root user and grant privilege:
	```bash
	$ adduser username
	......
	$ usermod -aG sudo username
	# privilege granted for this user
	```
	### in Ubuntu, 'root' directory and 'user home' directory are different
    `root@0-0-0-0:/root$ command under root directory with ultimate root user signed in`
	### 'user home' directory is 
    `username@0-0-0-0:/root$ command under root directory using 'user' signed in`
    `username@0-0-0-0:~$ command under user's home directory, and it's usually in /root/home/username/ directory`



**Creating PostgreSQL Database and User**:  

```bash
# Log into an interactive Postgres session by typing:
$ sudo -u postgres psql

# create a database for the project:
postgres=# CREATE DATABASE myproject;

# create a db user for the project:
postgres=# CREATE USER myprojectuser WITH PASSWORD 'password';


# modify a few connection parameters for the user created

# 1. setting the default encoding to UTF-8
postgres=# ALTER ROLE myprojectuser SET client_encoding TO 'utf8';

# 2. setting the default transaction isolation scheme to “read committed”, which blocks reads from uncommitted transactions
postgres=# ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';

# 3. setting the timezone. By default, our Django projects will be set to use UTC
postgres=# ALTER ROLE myprojectuser SET timezone TO 'UTC';

# give our new user access to administer our new database:
postgres=# GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;

# quit PostgresSQL prompt:
postgres=# \q

# useful command - see list of roles:
postgres=# \du
```
*Note*: it doesn't matter it's capital letters or not when you setting up, the database seems only understand lowercase letters, they will convert uppercase to lowercase anyway... However, the password is case sensitive...

**Host a project on server**
Do not forget to `pip freeze > requirements.txt` before commiting your project to be cloned to the server

*cloning*:

1. `$ su username` Switch to a User which you just created and have the root privilege
2. `$ cd ~/` go to the user's home directory
3. if you haven't installed git: `$ sudo apt install git`
4. `$ git clone repo/HTTP/address` CLONE the repo
5. `$ cd project_directory` go to this project's directory
6. `$ virtualenv venv` create the virtual environment
7. `$ source venv/bin/activate` activate the virtual environment
	**Note**: in venv, you can use pip instead of pip3, python instead of python3
8. `(venv)$ pip install -r requirements.txt` install all packages
9. `(venv)$ nano project/foler/settings.py` adjust a few things in settings:
	* **Note**: Of course you can choose to edit the file locally and commit to GitHub, then pull it to your server, the important things are the following:
	* `STATIC_ROOT = os.path.join(BASE_DIR, 'static/')` Normally you wouldn't have to set this up locally when developing
	* change the database from `sqlite3` to `postgres`
		```python
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'NAME': 'myproject_database_name',
            'USER': 'myproject_database_user',
            'PASSWORD': 'myproject_database_user_password',
            'HOST': 'localhost',
            'PORT': '',
        }
    }
   	```
  * `ALLOWED_HOSTS = ['your_server_domain_or_IP', 'second_domain_or_IP', . . ., 'localhost']` be careful here, at the end, include 'localhost' as you'll be proxying connections through a local Nginx instance, whatever that is...

10. setting up `SECRET_KEY` (followed [this blog](https://help.pythonanywhere.com/pages/environment-variables-for-web-apps/))
	* `$ pip install python-dotenv` or simply included in your `requirements.txt` file: `echo python-doctenv >> requirements.txt` before pulling the repo
	* on server: 
	``bash
	$ cd ~/project_directory/`
	$ echo "export SECRET_KEY=sekritvalue" >> .env
	```
	* in `wsgi.py` file in the same directory of `settings.py`, as well as `manage.py` file
	​```python
	import os
	from dotenv import load_dotenv
	project_folder = os.path.expanduser('~/my-project-dir')  # adjust as appropriate
	load_dotenv(os.path.join(project_folder, '.env'))
	```
	* finally in `settings.py`
	```python
	import os
	SECRET_KEY = os.getenv("SECRET_KEY")
	```
	
11. `$ python manage.py migrate` no need to `make migrations` in production

12. `$ python manage.py collectstatic` to collect static files

13. Allow port 8000 to test locally: `$ sudo ufw allow 8000`

14. `$ python manage.py runserver 0.0.0.0:8000`















  ```

  ```