---
layout: post
title:  "Daily Notes: Retail Website in Russian"
date:   2020-01-26 10:50:17 +0800
categories: Daily Notes
---

# Django:

---
## templates builtins!!!
very useful [**template shortcuts, tags and many more...**](https://docs.djangoproject.com/en/3.0/ref/templates/builtins/)



## Class based views: Generic VIEWS
learning source: 
[codingforentrepreneurs](https://www.codingforentrepreneurs.com/projects/class-based-views/handling-exceptions-objects)
[generic views official documentation](https://docs.djangoproject.com/en/3.0/ref/class-based-views/generic-display/)



## to iterate through a folder
[StackO: iterate through a static image folder in django](https://stackoverflow.com/questions/37270170/iterate-through-a-static-image-folder-in-django):
> This isn't something Django has built in. But Django is just Python, and you can use normal Python file functions to get your list in the view:
> ```python
> files = os.listdir(os.path.join(settings.STATIC_ROOT, "styles/jamia"))
> ```



## filter in queryset
When you know what to look for:
`category_handbags = Category.objects.filter(name='Handbags')`
furthur reading: [Database Functions](https://docs.djangoproject.com/en/3.0/ref/models/database-functions/)

#### * filter()
> By using filter(), we can retrieve a QuerySet of just those books that were published within the last 90 days period, like so:
>
> ```python
> from datetime import datetime, timedelta 
> Book.objects.filter(date_published__gte=datetime.now() - timedelta(days=90)).count()
> # __isnull
> Book.objects.filter(author__isnull=True).count()
> # __exact
> Book.objects.filter(title__exact='').count()
> ```

#### * exclude()
>if we use the exact same date_published example above but swap out filter() for exclude(), we’d expect the resulting book count to be the inverse: from 3 of 20 to now 17 of 20:
> ```python
> from datetime import datetime, timedelta
> Book.objects.exclude(date_published__gte=datetime.now() - timedelta(days=90)).count()
> # combining filters
>Book.objects.exclude(author__isnull=True).exclude(title__exact='').count()
> ```



## `categories.count()` vs `len(categories)`  
`len()` loads all the data in memory then do the counting  
`count()` is normally faster when the data is not pre loaded
be careful not to count a queryset manager like `categories.first().count()` will prompt `AttributeError`; however, `categories.first().subcategory_set.count()` will tell you the number of its subcategories correctly and it's the same as `len(categories.first().subcategory_set.all())`, as you can see, ALL of the subcategories are read to count the length.


# Django - Images:

---


## ImageField
To add `ImageField` in a model and to load the image in the database onto your web:
ref: [William Vincent](https://wsvincent.com/django-image-uploads/)
ref: [GeeksforGeeks](https://www.geeksforgeeks.org/imagefield-django-models/)



## uploaded images in wrong orientation:  
photos taken by phones have this problem sometimes, the [post](https://medium.com/@giovanni_cortes/rotate-image-in-django-when-saved-in-a-model-8fd98aac8f2a) by Giovanni solved the problem:  
```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from PIL import Image, ExifTags
from django.db import models
import os

class Item(models.Model):
    ...
    image_1 = models.ImageField(upload_to='items/')
    image_2 = models.ImageField(upload_to='items/', blank=True)
    image_3 = models.ImageField(upload_to='items/', blank=True)
    image_4 = models.ImageField(upload_to='items/', blank=True)
    image_5 = models.ImageField(upload_to='items/', blank=True)
    ...

# solves the problem that uploaded images are in wrong orientation
def rotate_image(filepath):
    try:
        image = Image.open(filepath)
        for orientation in ExifTags.TAGS.keys():
            if ExifTags.TAGS[orientation] == 'Orientation':
                break
        exif = dict(image._getexif().items())

        if exif[orientation] == 3:
            image = image.rotate(180, expand=True)
        elif exif[orientation] == 6:
            image = image.rotate(270, expand=True)
        elif exif[orientation] == 8:
            image = image.rotate(90, expand=True)
        image.save(filepath)
        image.close()
    except (AttributeError, KeyError, IndexError):
        # cases: image don't have getexif
        pass


@receiver(post_save, sender=Item, dispatch_uid="update_image_item")
def update_image(sender, instance, **kwargs):
    dic = {
        'image_1': instance.image_1,
        'image_2': instance.image_2,
        'image_3': instance.image_3,
        'image_4': instance.image_4,
        'image_5': instance.image_5,
    }
    for k, instanceImage in dic.items():
        if instanceImage:
            BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
            fullpath = BASE_DIR + instanceImage.url
            rotate_image(fullpath)

```
furture reading: [FIX UPLOADED IMAGES IN PYTHON WEBAPPS](https://www.lfchosting.com/fix-uploaded-images-python-webapps/)

## image files in image field deleted but the file remains in the MEDIA foler
[Cleanup Files (and Images) On Model Delete in Django](https://timonweb.com/posts/cleanup-files-and-images-on-model-delete-in-django/)



### ref: Online Retail websites
* [asos](https://www.asos.com/women/): most referenced in item displaying style, categorisation filtering, most responsive
* [LOUIS VUITTON](https://us.louisvuitton.com/eng-us/homepage): most elegant in wording and minimalistic in styling
* [BOTTEGA VENETA](https://www.bottegaveneta.com/us): not responsive enough on laptop
* [ACCESSORIZE LONDON](https://global.accessorize.com/en-cn/ru?skipRedirection=true): not responsive enough on laptop
* [KUPIVIP](https://www.kupivip.ru/search?q=&page=2&quantity_per_page=60): looks cheap



# django-bootstrap4

## containers, row and columns  
containers and grid [examples](https://getbootstrap.com/docs/4.4/examples/grid/#containers)  
`col-sm-12`: when page is 'small' width, occupy 12 columns  
`col-md-6`: when page is 'medium' width, occupy 6 columns  
`col-lg-4`: when page is 'large' width, occupy 4 columns  
`col-4`: At ALL time, occupy 4 columns, unless other rules applied  

`col-md-auto`: when page is 'medium' width, occupy whatever columns remain  

## Default grid settings: [docs/4.4/layout/grid](https://getbootstrap.com/docs/4.4/layout/grid/)

```css
$grid-columns:      12;
$grid-gutter-width: 30px;

$grid-breakpoints: (
  xs: 0,      // Extra small screen / phone
  sm: 576px,  // Small screen / phone
  md: 768px,  // Medium screen / tablet
  lg: 992px,  // Large screen / desktop
  xl: 1200px  // Extra large screen / wide desktop
);

$container-max-widths: (
  sm: 540px,
  md: 720px,
  lg: 960px,
  xl: 1140px
);
```



# Simply Python:

---


## to iterate through a dictionary:  
#### `dict.keys()` method and `dict.items()` method  
```python
# using dict.keys() method
D1 = {1:'a', 2:'b', 3:'c'} 
for key in D1.keys():
	print(k, D1[k])
# 1 a
# 2 b
# 3 c

# using dict.items() method
for key, value in D1.items()
	print(key, value)
# same result
```
