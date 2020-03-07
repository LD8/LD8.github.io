---
layout: post
title:  "Daily Notes: Django QuerySet Boost Efficiency"
categories: Daily Notes
---
> * In general, the results of a QuerySet aren’t fetched from the database until you “ask” for them. When you do, the QuerySet is evaluated by accessing the database. --[lazy qeury](https://docs.djangoproject.com/en/3.0/topics/db/queries/#querysets-are-lazy)  

> * [When querysets are evaluated](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#when-querysets-are-evaluated)

> * [Caching and QuerySets](https://docs.djangoproject.com/en/3.0/topics/db/queries/#caching-and-querysets)  
    >1. In general, attributes that are not callable will be cached.
    >2. In general, callable attributes cause DB lookups every time

## `with` template tag [link here](https://docs.djangoproject.com/en/3.0/ref/templates/builtins/#with)
>To make use of the caching behavior of QuerySet, you may need to use the with template tag.

## Use foreign key values directly [link here](https://docs.djangoproject.com/en/3.0/topics/db/optimization/#use-foreign-key-values-directly)
If you only need a foreign key value, use the foreign key value that is already on the object you’ve got, rather than getting the whole related object and taking its primary key. i.e. do: `entry.blog_id` instead of `entry.blog.id`

## [@cached_property](https://docs.djangoproject.com/en/3.0/ref/utils/#django.utils.functional.cached_property)
It is up to you to implement caching when required. Say in your model and there's an expensive computation method in this model, you can cache this method so that if it's used once, it won't be loaded twice for it to use again.

## Database Execution
When a query will be executed:
* iteration: `for i in Blog.objects.all(): ...`
* get: 
    * get an object with a unique field: `e = Entry.objects.get(pk=5)` and then another separate query if below:
    * get a related object: `b = e.blog`
    * ### to avoid: `e = Entry.objects.select_related('blog').get(pk=5)` only hit database once if more queries on `Blog` model/table
* 

## `select_related()` [link here](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#select-related)
## `prefetch_related()` [link here](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#prefetch-related)

## diff between `if query` and if `query.exist()`
Does the same thing but the mechanisms are different, hence, the efficiency.
* When to use:
    * Just want to check whether the queryset is/isn't empty - has at least 1 result
    * Just want to check whether `a in b`? - more efficient: `b.filter(id=a.id).exist()`
    * Additional to the above, in a large queryset

* when to not use
    * NOT when you know you will run this query anyway later


* Refs:
    * ref1: [official doc](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#django.db.models.query.QuerySet.exists)
    * ref2: [SO post](https://stackoverflow.com/questions/3432673/get-distinct-values-of-queryset-by-field)

---

### Overall ref django official doc:
* #### [Database access optimisation](https://docs.djangoproject.com/en/3.0/topics/db/optimization/#database-access-optimization)
* [QuerySet API reference](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#distinct)
* [models](https://docs.djangoproject.com/en/3.0/topics/db/models/)
* [Making queries](https://docs.djangoproject.com/en/3.0/topics/db/queries/)