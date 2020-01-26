---
layout: post
---
# process
It's actually easier than I thought it would be. All you have to do is:
1. to make a API call, 

   * It normally takes two arguments for `request.get()` to work:
     1. url: an API link string like this: `'https://api.github.com/search/repositories?q=language:python&sort=stars'`
     2. headers: `{'Accept': 'application/vnd.github.v3+json'}`
   * Pass the two arguments in like so: `requests.get(url, headers=headers)
   * Then you need to store it in a variable:

2. `get` the data, 

   * `r = requests.get(url, headers=headers)`, `r` here is the original data from the source, however, `type(r)` is `<class 'requests.models.Response'>`
   * `type(r.json())`, however, is `<class 'dict'>`. So the real data is this dictionary: `dict = r.json()`

3. and then process the data by inspecting the structure and the content, 

   * The only way you can process the data is to look through its documentation or inspect the data yourself
     * Some data is unreadable, so you need to create a new file and dump the original data in it so it's in a readable format for you: `json.dump(origin, target, indent=4)` 
     * You can also install an extension on Chrome Browser called `JSONView` which immensely increases the readability of JSON files on browser

4. extract the useful data, store them in variables,

   * You need to analyse the data and find out which ones you need to render a chart
   * JSON files consist of dictionaries, meaning you can easily access anything using key-value pairs
   * Access the data and store them in variables for plotly to render

5. use plotly or other data visualisation libraries to render the data

   * Now I've learnt method `offline.plot()` imported from `plotly`, which is quite useful. It requires two arguments to work:

     1. `{'data': data, 'layout': layout}`
     2. `filename='xxxx.html'`

     * Result: `offline.plot({'data': data, 'layout': layout}, filename='xxxx.html')`
     * OR: 

     ```python
     fig = {'data': data, 'layout': layout}
     offline.plot(fig, filename='top_python_repos.html')
     ```

     

   * data

   ```python
   data = [{
       'type': 'bar',
       'x': repo_links,
       'y': repo_starcounts,
       'hovertext': repo_descriptions,
       'marker': {
           'color': 'rgb(60,100,150)',
           'line': {
               'width': 1.5,
               'color': 'rgb(25,25,25)'
           },
       },
       'opacity': 0.6,
   }]
   ```

   * Data notes:
     * There can be many data sets rendered into one figure, so each individual data set needs to be in a `[list]`
     * EVERYTHING relating to the content displaying in the figure goes here! Including the style of the figure: 'marker'

   

   ​	

   * layout

   ```python
   layout = {
       'title': 'Top 30 Python Repos',
       'titlefont': {
           'size': 28,
       },
       'xaxis': {
           'title': 'Repositories', 
           'titlefont': {'size': 24},
           'tickfont': {'size': 14},
       },
       'yaxis': {
           'title': 'Stars', 
           'titlefont': {'size': 24},
           'tickfont': {'size': 14},
       },
   }
   ```

   * Layout notes:
     * All the titles, title's fonts, sizes can be specified here
     * Be careful how they nested, it seems that other than 'title', every key needs to have its own dict as their value, check [plotly](https://plot.ly/python/) documentation for future information
     * Be careful of the commas, don't miss any





# 'requests' package

```bash
$ python -m pip install --user requests
```

# to check API search limit on Github

checkout this [rate_limit link](https://api.github.com/rate_limit)

# Concept  

A web **API** is a part of a website designed to interact with the programs which use very specific URLs to request certain information from that website. 

This kind of request is called _an API call_.

The requested data will be returned in an easily processed format, such as JSON or CSV. 

Most apps that rely on external data sources, such as apps that integrate with social media sites, rely on API calls. 

An API call:  
`https://api.github.com/search/repositories?q=language:python&sort=stars`  
explain: "The question mark after repositories signals that we’re about to pass an argument. The `q` stands for query, and the equal sign (=) lets us begin specifying a query (q=). By using `language:python`, we indicate that we want information only on repositories that have Python as the primary language. The final part, &sort=stars, sorts the projects by the number of stars they’ve been given." (2018, _'Python Crash Course'_)

# code

```python
import requests
from plotly.graph_objs import Bar
from plotly import offline

# prepare to make an API call: store url and headers in vars
url = 'https://api.github.com/search/repositories?q=language:python&sort=stars'
# explicitly ask to use this version of github API, 
# you can find it here: https://developer.github.com/v3/
headers = {'Accept': 'application/vnd.github.v3+json'}

# make the API call using get() function, 
# and store the response in a variable:
r = requests.get(url, headers=headers)

# check whether the call is successful:
print('Status code: {}'.format(r.status_code))

# type(r) is <class 'requests.models.Response'>
# type(r.json()) is <class 'dict'>
response_dict = r.json()

# print(response_dict.keys())
# dict_keys(['total_count', 'incomplete_results', 'items'])

repos = response_dict['items']

repo_links, repo_descriptions, repo_starcounts = [], [], []

for repo in repos:
    repo_name = repo['name']
    repo_url = repo['html_url']
    repo_links.append(f'<a href="{repo_url}">{repo_name}</a>')
    owner = repo['owner']['login']
    description = repo['description']
    repo_descriptions.append(f"{owner}<br>{description}")
    repo_starcounts.append(repo['stargazers_count'])

data = [{
    'type': 'bar',
    'x': repo_links,
    'y': repo_starcounts,
    'hovertext': repo_descriptions,
    'marker': {
        'color': 'rgb(60,100,150)',
        'line': {
            'width': 1.5,
            'color': 'rgb(25,25,25)'
        },
    },
    'opacity': 0.6,
}]

layout = {
    'title': 'Top 30 Python Repos',
    'titlefont': {
        'size': 28,
    },
    'xaxis': {
        'title': 'Repositories', 
        'titlefont': {'size': 24},
        'tickfont': {'size': 14},
    },
    'yaxis': {
        'title': 'Stars', 
        'titlefont': {'size': 24},
        'tickfont': {'size': 14},
    },
}

fig = {'data': data, 'layout': layout}
offline.plot(fig, filename='top_python_repos.html')
```