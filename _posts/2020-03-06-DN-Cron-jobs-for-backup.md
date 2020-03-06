---
layout: post
title:  "Daily Notes: Cron Jobs setting for Backups"
categories: Daily Notes
---

## Cron jobs research notes
1. connect to server via SSH
2. `$ crontab -l` to check cron jobs under current user
3. `$ crontab -e` to edit cron table
_(note: if using `vim`, after the command above, enter `i` to insert content)_
4. `* * * * * cd director && /user/bin/python3 test.py` run this `test.py` python file once very minute  
(ref: [Dillon Head's post: beginners guid to cron](https://mediatemple.net/blog/news/complete-beginners-guide-cron-part-1/?utm_source=reddit&amp;utm_campaign=blog&amp;utm_medium=social&amp;utm_term=linkpreview&amp;utm_content=cronpart1repub))
### additionally:
5. if you want to see the status: `* * * * * cd director && /user/bin/python3 test.py >> test.out`
6. after the cron job has been executed: `tail -f test.out` to check the status if there's anthing printed out from `test.py` file

---

## Setting up for sending mail - [django-mailer](https://pypi.org/project/django-mailer/)
### Installation
```bash
(venv)$ pip install django-mailer
```
### `Settings.py`
```python
# settings.py

EMAIL_BACKEND = "mailer.backend.DbBackend"
# specify you are actually using django default mail backends to send emails
MAILER_EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'

EMAIL_USE_SSL = True
EMAIL_HOST = 'smtp.mail.ru'
EMAIL_PORT = 465
EMAIL_HOST_USER = 'example@email.com'
EMAIL_HOST_PASSWORD = 'email_psswd'
DEFAULT_FROM_EMAIL = 'Official Email <{}>'.format(EMAIL_HOST_USER)
```
### bash command:
```bash
(venv)$ python manage.py send_mail
```
### bash command ==> crontab -e
According to the official document, there should be a few cron jobs to be setup, including a `send_mail` command and 2 logging command:
```bash
*       * * * * (/path/to/your/python /path/to/your/manage.py send_mail >> ~/cron_mail.log 2>&1)
0,20,40 * * * * (/path/to/your/python /path/to/your/manage.py retry_deferred >> ~/cron_mail_deferred.log 2>&1)
0 0 * * * (/path/to/your/python /path/to/your/manage.py purge_mail_log 7 >> ~/cron_mail_purge.log 2>&1)
```
So edit the crontab:
```bash
$ crontab -e
```
Refer to the format below:
```bash
*/5 * * * * source /home/ubuntu/.bashrc && source /home/ubuntu/work/your-project/bin/activate && python /home/ubuntu/work/your-project/src/manage.py runcrons > /home/ubuntu/cronjob.log
# execute this command every 5 mins
```
---

## An alternative way: DIY shell script
#### [This SO post](https://stackoverflow.com/questions/8779951/how-do-i-run-a-shell-script-without-using-sh-or-bash-commands) helped me to understand this
Basically, you create a text file and write a "shebang" and a couple command instead of writing everything in crontab:
* the format of the script file (everything is a must)  
    create a script file called `script.sh`
    ```bash
    #!/bin/bash
    source /home/ubuntu/.bashrc
    source /home/ubuntu/project/venv/bin/activate
    python /home/ubuntu/project/manage.py send_mail >> /home/ubuntu/logs/cron_mail.log 2>&1
    ```
* make your file executable
    ```bash
    $ chmod +x script.sh
    ```
* modify your path to add the directory where your script is located
    ```bash
    $ export PATH=$PATH:/appropriate/directory
    ```
    >Typically, you want `$HOME/bin` for storing your own scripts
    
---
## Troubleshooting:
### Checkout the [Why crontab scripts are not working]() post
* Cron not running 
    ```bash
    $ pgrep cron
    ```
    If you see no number, then cron is not running. sudo /etc/init.d/cron start can be used to start cron.
    ```bash
    $ sudo /etc/init.d/cron start
    [ ok ] Starting cron (via systemctl): cron.service.
    # OR
    $ sudo service cron start
    ```
    Also you could use systemctl in modern Linux, e.g.
    ```bash
    $ sudo systemctl start cron
    ```
    ### It seems that a new server just set up requires turning on cron manually.
* Crontab file has to end in a new line.. it's a silly one