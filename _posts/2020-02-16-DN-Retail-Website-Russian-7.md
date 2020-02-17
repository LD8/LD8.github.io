---
layout: post
title:  "Daily Notes: Retail Website in Russian P6"
date:   2020-02-16 13:49:17 +0800
categories: Daily Notes
---

## General knowledge
---
### url in css:
* Because css is a static file, so the url inside is better be static as well. so just use relative url for url() in css files.
* for some reason it's slow to load a file when you pupdate it in css. sometime I have to restart the local server for it to take effect

## Django
---
* ### where should `signal.py` file lives: 
[article by Vitor Freitas](https://simpleisbetterthancomplex.com/tutorial/2016/07/28/how-to-create-django-signals.html) & [Django official doc](https://docs.djangoproject.com/en/3.0/topics/signals/)
1. create `signal.py` file in the same directory as `models.py` and `urls.py` etc.
2. edit the file, write your code
3. add following code in `apps.py` file under the same directory
	```python
	def ready(self):
		import appName.signals # noqa
	```
4. register in `settings.py`, in `INSTALLED_APPS`, register `'appName.apps.appNameConfig',` for signal.py to work properly

follow the four steps

---
* ### models - `DateTimeField(auto_now=True)` and `DateTimeField(auto_now_add=True)`
  * `DateTimeField(auto_now=True)` -- update everytime this model instance is edited
  * `DateTimeField(auto_now_add=True)` -- set the time once when this model instance is created

---
* ### SO ask and answered [Django - logout view customisation fail](https://stackoverflow.com/questions/60256470/django-logout-view-customisation-fail/60256968?noredirect=1#comment106584955_60256968)

---
* ### context processor
	* adding a context processor to your project if you find repetitive bahavior in passing the same context to different views:
		1. in the same directory of `models.py` create a file named `context_processors.py`
    2. create a function: 
    ```python
    from .models import Category
		from django.template.context_processors import request

    def category_context_processor(request):
        categories = Category.objects.all()
        return {'categories': categories}
    ```
    3. add this processor to `settings.py`
    ```python
    TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                ... # other processors
                ... # add code below:<app-name>.<file-name>.<function-name>
                'app-name.context_processors.category_context_processor',
            		],
        		},
    		},
		]
    ```
Now you don't have to pass `categories` to each view now! And this code will run on every request on the site.
