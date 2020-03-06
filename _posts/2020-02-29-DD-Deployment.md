---
layout: post
title:  "Django Russian Retail Website Deployment"
categories: Django Deployment
---

## Finally the [Deployment on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-18-04)
**Before**:
	Don't forget the [official checklist](https://docs.djangoproject.com/en/3.0/howto/deployment/checklist/) before your deployment. And delete all migrations files in `migrations` folder except for `__init__.py` for a clean start. And do not forget to `pip freeze > requirements.txt` before commiting your project to be cloned to the server.

---

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
6. add root user and grant privilege: ([more](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04) about initial server setup with Ubuntu 18.4)
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

---

**Creating PostgreSQL Database and User**:  

```sql
# Log into an interactive Postgres session by typing:
$ sudo -u postgres psql

# create a database for the project:
postgres=> CREATE DATABASE myproject;
# careful here, if you need utf-8 encoding, you have to specify it:
postgres=> CREATE DATABASE new_db_name WITH ENCODING='UTF8' LC_CTYPE='ru_RU.UTF-8' LC_COLLATE='ru_RU.UTF-8' OWNER=postgres TEMPLATE=template0

# create a db user for the project:
postgres=> CREATE USER myprojectuser WITH PASSWORD 'password';


# modify a few connection parameters for the user created

# 1. setting the default encoding to UTF-8
postgres=> ALTER ROLE myprojectuser SET client_encoding TO 'utf8';

# 2. setting the default transaction isolation scheme to “read committed”, which blocks reads from uncommitted transactions
postgres=> ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';

# 3. setting the timezone. By default, our Django projects will be set to use UTC
postgres=> ALTER ROLE myprojectuser SET timezone TO 'UTC';

# give our new user access to administer our new database:
postgres=> GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser;

# quit PostgresSQL prompt:
postgres=> \q

# useful command - see list of roles:
postgres=> \du

# to delete a database
postgres=> DROP DATABASE database_name;
```
**Some useful sql commands** [here](https://www.postgresqltutorial.com/psql-commands/)

*Note*: it doesn't matter whether you set your database name to capital letters or not when you setting up, they will convert uppercase to lowercase anyway... However, the password is case sensitive...

---

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
        ```bash
        $ cd ~/project_directory/`
        $ echo "export SECRET_KEY=seekratevalue" > .env
        ```
        You can edit `.env` file and adding more environment variable, or you can use commandline like so:
        ```bash
        # you can also directly export one-off environment variable directly on commandline
        $ export CAPITAL_NAME=true
        # it doesn't matter whether you add quotation marks for the value when you export, even when they are strings, the value 'true' will always be a string

        # if you export directly in command, be careful of ! as it needs to be excaped \!
        $ export PASSWD_DB = something\!and@#others
        ```
        But I would recommand to use `.env` file if you set it up already. Keep everything in one place.
        
	* in `wsgi.py` file in the same directory of `settings.py`, as well as `manage.py` file
        ```python
        import os
        from dotenv import load_dotenv
        project_folder = os.path.expanduser('~/my-project-dir')  # adjust as appropriate
        load_dotenv(os.path.join(project_folder, '.env'))
        ```
        Be very careful the path of the folder, or you won't get the correct env variable. A way to check whether you have the variable in your environment is go to `python manage.py shell` and `import os`, like below:

	* finally in `settings.py`
        ```python
        import os
        SECRET_KEY = os.getenv("SECRET_KEY")
        ```
	
11. `$ python manage.py migrate` no need to `make migrations` in production

12. `$ python manage.py collectstatic` to collect static files

13. Allow port 8000 to test locally: `$ sudo ufw allow 8000`

14. `$ python manage.py runserver 0.0.0.0:8000`, then you should be able to visit the site through 'your_ip:8000' port.

15. `$ gunicorn --bind 0.0.0.0:8000 myproject.wsgi` to bind it with `unicorn`, testing Gunicorn's ability to serve the project. You would still be able to visit the site through the same port just this time, it would be served by gunicorn (Note: `myproject` in `myproject.wsgi` is the folder where `wsgi.py` file exists)

16. `$ deactivate` now we can exit the virtual environment

---

**Gunicorn: Creeating systems Socket and Service Files**

1. `$ gunicorn --bind 0.0.0.0:8000 myproject.wsgi` Following up the binding: 

2. `$ sudo nano /etc/systemd/system/gunicorn.socket` **system socket** creation

   ```nano
   # /etc/systemd/system/gunicorn.socket
   [Unit]
   Description=gunicorn socket
   
   [Socket]
   ListenStream=/run/gunicorn.sock
   
   [Install]
   WantedBy=sockets.target
   # save and close
   ```

3. `$ sudo nano /etc/systemd/system/gunicorn.service` **system service file** for Gunicorn:

   ```nano
   [Unit]
   Description=gunicorn daemon
   Requires=gunicorn.socket
   After=network.target
   
   [Service]
   User=sammy
   Group=www-data
   WorkingDirectory=/home/sammy/myprojectdir
   ExecStart=/home/sammy/myprojectdir/myprojectenv/bin/gunicorn \
             --access-logfile - \
             --workers 3 \
             --bind unix:/run/gunicorn.sock \
             myproject.wsgi:application
   
   [Install]
   WantedBy=multi-user.target
   
   # Note: User is the system user with writing privilege, so change that, also change 'WorkingDirectory' and 'ExecStart', DO NOT forget 'project_folder_contains_wsgi.wsgi:application'
   ```

4. Start and enable Gunicorn socket, so that connections can be handled by gunicorn.service:

   ```bash
   $ sudo systemctl start gunicorn.socket
   $ sudo systemctl enable gunicorn.socket
   ```

5. Status Check:

   ```bash
   $ sudo systemctl status gunicorn.socket
   # Check the status of the process to find out whether it was able to start
   
   $ file /run/gunicorn.sock
   /run/gunicorn.sock: socket
   # check for the existence of the gunicorn.sock file within the /run directory
   
   $ sudo journalctl -u gunicorn.socket
   # Check the Gunicorn socket’s logs by typing for debugging
   ```

6. Test Socket Activation:

   ```bash
   $ sudo systemctl status gunicorn
   # gunicorn.service won't be active since there's no connections made, so this output should be 'Active: inactive (dead)'
   
   $ curl --unix-socket /run/gunicorn.sock localhost
   # To test the socket activation mechanism, we can send a connection to the socket through curl by typing the above, and you should be able to see the HTML output, meaning that Gunicorn is serving your Django app
   
   $ sudo systemctl status gunicorn
   # You can verify that the Gunicorn service is running by typing the above
   
   # check the logs for additional details:
   $ sudo journalctl -u gunicorn
   
   # Check your /etc/systemd/system/gunicorn.service file for problems. If you make changes to the /etc/systemd/system/gunicorn.service file, reload the daemon to reread the service definition and restart the Gunicorn process by typing:
   $ sudo systemctl daemon-reload
   $ sudo systemctl restart gunicorn
   ```

   Note: Make sure everything above is debugged before continue 

---

**Nginx: Configuration to Proxy Pass to Gunicorn**

After Gunicorn is set, Nginx is to be configured to pass traffic to the process

1. `$ sudo nano /etc/nginx/sites-available/myproject` create a **server block** for Nginx (_myproject_ name is case sensitive)

2. Inside: open a new _server block_; to ignore problems with finding a favicon; tell Nginx to find static assets and media assets:

   ```nano
   server {
       listen 80;
       server_name server_domain_or_IP;
   
       location = /favicon.ico { access_log off; log_not_found off; }
       location /static/ {
           root /home/sammy/myprojectdir;
       }
       location /media/ {
           root /home/sammy/myprojectdir;
       }
       
       location / {
           include proxy_params;
           proxy_pass http://unix:/run/gunicorn.sock;
       }
   }
   
   # save and close the file
   ```

3. Afterwards:

   ```bash
   $ sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled 
   # Enable the file by linking it to the `sites-enabled` directory
   
   $ sudo nginx -t
   # Test your Nginx configuration for syntax errors by typing the above
   
   # If no errors are reported, go ahead and restart Nginx by typing:
   $ sudo systemctl restart nginx
   ```

   

4. Firewall settings:

   ```bash
   # Finally, we need to open up our firewall to normal traffic on port 80. Since we no longer need access to the development server, we can remove the rule to open port 8000 as well:
   $ sudo ufw delete allow 8000
   $ sudo ufw allow 'Nginx Full'
   
   # check the registered profiles to be managed by UFW
   $ ufw app list
   Available applications:
   	Nginx Full
     Nginx HTTP
     Nginx HTTPS
     OpenSSH
     
   # make sure that the firewall allows SSH connections
   $ ufw allow OpenSSH
   # enable the firewall by typing 
   $ ufw enable
   # type "y" and press `Enter` to proceed
   
   # You can see that SSH connections are still allowed by typing:
   $ ufw status
   # you should be able to see all apps allowed by the firewall
   
   # Note: you can log in by using a user's name as well
   $ ssh sammy@your_server_ip
   ```



---

## Make sure:

1. You have run [initial server setup for Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)

2. Your domain names ([A-record](https://docs.ispsystem.com/ispmanager-lite/domain-names/resource-records#Resourcerecords-A-record)) are pointing to your VPS's IP address: [How to](https://www.hostinger.com/tutorials/dns/how-to-point-domain-to-vps)

   ```bash
   # basically if you run:
   $ dig NS +short your_domain.com
   # shows the desired host name servers and:
   
   $ dig A +short your_domain.com
   # shoes your VPS IP, then you are good to go
   ```

   You might need to wait for the change to take effect, it takes up to 24 hours if you requested DNS propagation (changed your DNS in any way)

3. Nginx installed: [How to](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)

---

## **[SSL with _Let's Certbot_ on _Nginx_](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04)**

```bash
# installation
$ sudo apt install python-certbot-nginx

# open the server block file created earlier
$ sudo nano /etc/nginx/sites-available/myproject
# 'myproject' name is case sensitive
```

**find the existing `server_name` line, and make sure it looks like this:**

```nano
...
server_name example.com www.example.com;
...
```

Save and exit.

```bash
# always verify the syntax of your configuration edits:
$ sudo nginx -t

# Once your configuration file’s syntax is correct, reload Nginx to load the new configuration:
$ sudo systemctl reload nginx

# check your ufw (firewall) status
$ sudo ufw status

# if there's no 'Nginx Full' or simply 'Nginx HTTP':
$ sudo ufw allow 'Nginx Full'
$ sudo ufw delete allow 'Nginx HTTP'

# Now when you run 'ufw status' check, it should be looking like this:
To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

**Finally, Obtaining an SSL Certificate from Cerbot**

```bash
$ sudo certbot --nginx -d example.com -d www.example.com
```

Note:

1. This runs `certbot` with the `--nginx` plugin
2. using `-d` to specify the names we’d like the certificate to be valid for

> Try reloading your website using `https://` and notice your browser’s security indicator. It should indicate that the site is properly secured, usually with a green lock icon. If you test your server using the [SSL Labs Server Test](https://www.ssllabs.com/ssltest/), it will get an **A** grade.

```bash
# You can check certificate's auto-renewal by typing:
$ sudo certbot renew --dry-run
```

Further reading: [Certbot doc](https://certbot.eff.org/docs/)

---

**Troubleshooting Nginx and Gunicorn**

Near the end of [this post](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-18-04), there's a session of troubleshooting.

---








