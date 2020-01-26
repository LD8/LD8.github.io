---
layout: post
title: 404 and 500 Customisation - Django
---
### Simple 3 Steps
1. add a folder `templates` under the root folder of your project; ideally it should be the same directory as your `manage.py` file
2. create `404.html` and `500.html` files in `templates` folder and customize however you please
3. go to `settings.py` change one little thing:
```python
# assuming you haven't changed your BASE_DIR
# this is the default:
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

TEMPLATES = [{
	'DIRS': [os.path.join(BASE_DIR, 'templates')],
}]
```
You have to make sure that the correct html files can be properly referred to. And that's is!!


