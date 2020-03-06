---
layout: post
title:  "Daily Notes: RWiR - DJango Logging, datetime module"
categories: Daily Notes
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


