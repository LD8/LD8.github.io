---
layout: post
title:  "Daily Notes: Retail Website in Russian"
date:   2020-02-05 09:50:17 +0800
categories: Daily Notes
---

## Django:
---
### Animate your element
* [**Animate.css**](https://daneden.github.io/animate.css/)
* [**wowjs**](https://wowjs.uk/docs.html)
* [StackO: how to animate an image](https://stackoverflow.com/questions/46547695/how-to-animate-a-image-with-waypoint-or-wow-with-bootstrap-4)

---
### after cloned a django project:
execute the following command
```bash
$ python3 -m venv venv
$ source venv/bin/activate
(venv) $ pip install -r requirement.txt
(venv) $ pythong manage.py runserver 127.0.0.1:8080 #(or some other port)
```

---
### Django admin site

* `$ python manage.py creatsuperuser` creates users with attr `is_superuser` or `is_staff`
* `class ModelAdmin` from `django.contrib.admin`
  - if you need to make changes to the default admin interface, you need to create a object of `ModelAdmin` like so: `class AuthorAdmin(admin.ModelAdmin):`, this represent `Author` model on admin dashboard
  - instead of registering like this: `admin.site.register(Author)`, you have to use a decorator to your `AuthorAdmin` class like so:
    ```python
    from django.contrib import admin
    from .models import Author
    from myproject.admin_site import custom_admin_site
    
    # Reader and Editor objects have to have a ForeignKey field pointing at Author
    @admin.register(Author, Reader, Editor, site=custom_admin_site)
    class AuthorAdmin(admin.ModelAdmin):
    	pass
    ```
    
  - Create [InlineModelAdmin](https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#inlinemodeladmin-objects) object to display extra info on a model in admin add page.



