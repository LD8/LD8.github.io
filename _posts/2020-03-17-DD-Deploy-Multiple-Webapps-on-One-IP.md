---
layout: post
title:  "Django Deployment: Deploy Multiple Webapps under Single IP"
categories: Django Deployment
---

## Following previous post for deployment on 2020-02-29 (LEAP DAY!!)
Finish:
* Initialisation
* Creating PostgreSQL Database and User
* Host a project on server

and you'd arrive here:  

### basically do the following again but with details changed:
* create a gunicorn socket with different name `gunicorn.socket` => `myproject.socket` 
* `gunicorn.service` => `myproject.service`
* a new Nginx Configuration again
* point your new domain to the IP (takes up to 24 hours)
* configure SSL with Cerbot

### Each project with different domain name should have a separate gunicorn socket and service, as well as nginx configuration to handle the traffic.


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








