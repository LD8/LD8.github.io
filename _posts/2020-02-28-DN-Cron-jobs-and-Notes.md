---
layout: post
title:  "Daily Notes: RWiR - Cron Jobs and Notes"
categories: Daily Notes
---

## Cron jobs research notes
[Dillon Head's post: beginners guid to cron](https://mediatemple.net/blog/news/complete-beginners-guide-cron-part-1/?utm_source=reddit&amp;utm_campaign=blog&amp;utm_medium=social&amp;utm_term=linkpreview&amp;utm_content=cronpart1repub)
1. connect to server via SSH
2. `$ crontab -l` to check cron jobs under current user
3. `$ crontab -e` to edit cron table
_(note: if using `vim`, after the command above, enter `i` to insert content)_
4. `* * * * * cd director && /user/bin/python3 test.py` run this `test.py` python file once very minute
### additionally:
5. if you want to see the status: `* * * * * cd director && /user/bin/python3 test.py >> test.out`
6. after the cron job has been executed: `tail -f test.out` to check the status if there's anthing printed out from `test.py` file

---

## [rsyncing for remote backups](https://mediatemple.net/blog/tips/many-uses-rsync/)

---

## write log

```python
from datetime import datetime

file_name = datetime.now().strftime('%d-%m-%Y')
with open(file_name, 'w') as f:
	f.write('something to write for the first line')
```

---

## datetime module study
`from datetime import datetime`
```bash
>>> datetime
<class 'datetime.datetime'>
>>> 
>>> datetime.now()
datetime.datetime(2020, 2, 28, 12, 50, 35, 225544)
>>> 
>>> datetime.now().strftime('%d')
'28'
>>> 
>>> datetime.now().strftime('%d-%m')
'28-02'
>>> 
>>> datetime.now().strftime('%d-%m-%y')
'28-02-20'
>>> 
>>> datetime.now().strftime('%d-%m-%Y')
'28-02-2020'
>>> 
>>> datetime.now().strftime('%d-%m-%Y %H:%M:%S')
'28-02-2020 12:51:25'
```

---

## useful bash command
```bash
$ whereis python3
/usr/bin/python3
```

---

## vim basic
* * * * * (/usr/bin/python3 /Users/peiwen_li/Documents/GitHub/VA-boutique/manage.py send_mail >> ~/cron_mail.log 2>&1)
输入：
: w filename （将文章以指定的文件名filename保存）
: wq (输入「wq」，存盘并退出vim)
: q! (输入q!， 不存盘强制退出vim)

---

## get a file path in mac: press option key after right click the file, easy!

---


