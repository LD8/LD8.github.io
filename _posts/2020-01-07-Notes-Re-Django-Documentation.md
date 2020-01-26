---
layout: post
---

## Models
- a model is like a column/row/table of an excel file, it contains essential fields and behaviours of the data stored
- they all inherite `django.db.models.Model` class
- after setting up the model classes, you can access the database through the API generated automatically by Django: see [Making queries](https://docs.djangoproject.com/en/3.0/topics/db/queries/)
- Adding apps to settings `INSTALLED_APPS`: this is to tell Django that there's new models you'd like to use and you've set them up, after that, you need to `makemigrations` and `migrate` for the models to take effect, meaning tables are created in the database for you to use later
- Model fields: determine the column type, the default HTML widget to use to render a form field, validation; [model field reference](https://docs.djangoproject.com/en/3.0/ref/models/fields/#model-field-types) lists all the fields a model class can implement
- Field common used options: 
	* null=True, database related, doesn't have to exist at all
	* blank=True(can be not filled), validation on forms won't be required
	* choices=(('s', 'small'), ('m', 'medium'), ('l','large')) a sequence of 2-tuples,
	* default: a value or a callable object, everytime the new object is created, it will be called
	* unique=True, this field must be unique throughout the table
	[common model field option reference](https://docs.djangoproject.com/en/3.0/ref/models/fields/#common-model-field-options)
- [Model Meta options](https://docs.djangoproject.com/en/3.0/ref/models/options/)
- [Model methods](https://docs.djangoproject.com/en/3.0/topics/db/models/#model-methods): define functions/methods on a model is adding custom "row-level" functionality to the object.
- [Model inheritance](https://docs.djangoproject.com/en/3.0/topics/db/models/#model-inheritance): 
	1. [Abstract base classes](https://docs.djangoproject.com/en/3.0/topics/db/models/#abstract-base-classes)
	2. [Multi-table inheritance](https://docs.djangoproject.com/en/3.0/topics/db/models/#multi-table-inheritance)
	3. [Proxy models](https://docs.djangoproject.com/en/3.0/topics/db/models/#proxy-models)


## Making Queries
- filters: [Retrieving specific objects with filters](https://docs.djangoproject.com/en/3.0/topics/db/queries/#retrieving-specific-objects-with-filters)
- `Entry.objects.all()[:5]` - first 5 entry objects (LIMIT 5)
- `Entry.objects.all()[5:10]` - (OFFSET 5 LIMIT 5)
- `Entry.objects.all()[:10:2]` - return a list of every second object of the first 10
- chaning filters:
	The result of refining a QuerySet is itself a QuerySet, so it’s possible to chain refinements together. For example:
```bash
>>> Entry.objects.filter(
...     headline__startswith='What'
... ).exclude(
...     pub_date__gte=datetime.date.today()
... ).filter(
...     pub_date__gte=datetime.date(2005, 1, 30)
... )
```
This takes the initial QuerySet of all entries in the database, adds a filter, then an exclusion, then another filter. The final result is a QuerySet containing all entries with a headline that starts with “What”, that were published between January 30, 2005, and the current day.

## Django Template Language
- [built-in filters](https://docs.djangoproject.com/en/3.0/ref/templates/builtins/#ref-templates-builtins-filters): 
	1. `{ bio|truncatewords:30}` display the first 30 words of the bio variable
	2. `{ list|join:", "}` join a list with commas and spaces
	3. `{ value|filesizeformat }` human-readable file size: If value is 123456789, the output would be 117.7 MB.
	4. `{ value|length }` returns the length of a string or a list
	5. `{ value|first }` and `{ value|last }` return the first or the last of a string/list

- Comments: `{# comment out part #}`
- you can access the methods in a class by dot notation in template as well

## [URL Dispatcher](https://docs.djangoproject.com/en/3.0/topics/http/urls/)
- `\<slug:slug\>` at the end of url dispatcher: matches any slug string consiting of ASCII letters or numbers, plus the hyphen and underscore characters.
- `from django.urls import re_path` for regex: `re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),)`
- you can pass extra options to view function: [here](https://docs.djangoproject.com/en/3.0/topics/http/urls/#passing-extra-options-to-view-functions)

## [Request and Response Objects](https://docs.djangoproject.com/en/3.0/ref/request-response/)

To be read