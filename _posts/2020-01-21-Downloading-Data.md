---
layout: post
---
# CSV  
comma-separated values
e.g.  
```
"USW00025333","SITKA AIRPORT, AK US","2018-01-01","0.45",,"48","38"
```

`import csv` to utilise the _csv.reader()_ function to read a csv file.

# notes

* remember it's _subplots()_ with 's' at the end: `fig, ax = plt.subplots()` 
* `plt.fill_between(dates, highs, lows, facecolor='cyan', alpha=0.1)`
	* _alpha_ attribute lets you adjust the opacity of the graph
	* _`plt.fill_between()`_ method lets you fill a color between two lines
	* _facecolor_ attribute lets you pick the color to fill between the lines
* _`fig.autofmt_xdate()`_ this format the labels on the x axis diagonally
* _`from datetime import datetime`_
	* `datetime.strptime(sample_time, 'format_time')`
		* `sample_time` is the time you want to convert into a recognisable object
		* `format_time` is something like this: '%Y-%m-%d', telling python the format of the `sample_time` to convert it accordingly
* use error handling to prevent 'missing data' crash the program

death_valley_highs_lows.py
```python
import csv # so we can read csv files using csv.reader() function
import matplotlib.pyplot as plt
from datetime import datetime

with open('data/death_valley_2018_simple.csv') as f:
    reader = csv.reader(f)
    header = next(reader)
    
    # for index, text in enumerate(header):
    #     print(index, text)

    highs, lows, dates = [], [], []
    for row in reader:
        date = datetime.strptime(row[2], '%Y-%m-%d')
        try:
            highs.append(int(row[4]))
            lows.append(int(row[5]))
            dates.append(date)
        except ValueError:
            print('Something wrong with the data on {}'.format(date))
        
    
    # print(highs, dates)

plt.style.use('seaborn')
fig, ax = plt.subplots() # remember it's subplotS not subplot...

ax.plot(dates, highs, c='red', linewidth=1, alpha=0.5)
ax.plot(dates, lows, c='blue', linewidth=1, alpha=0.5)
plt.fill_between(dates, highs, lows, facecolor='cyan', alpha=0.1)

ax.set_xlabel('', fontsize=12)
fig.autofmt_xdate() # this draws the date labels diagonally

ax.set_ylabel('Temperature (F)', fontsize=12)

ax.set_title('Daily High and Low Temperatures\nDeath Valley - 2018', fontsize=20)

plt.show()
```


