---
layout: post
title:  "Daily Notes: RWiR - Search Combo"
categories: Daily Notes
---

## [SO: Django - form not validating](https://stackoverflow.com/questions/60412189/django-form-not-validating)

I posted this yesterday, however, no one answered me on SO. So I experimented a bit and searched for a while online, finally I figured it out... Most of the seemingly unsolvable mystery caused by the holes in the knowledge. Luckily, they can be patched:
1. apparently, you need to [bind uploaded images to a form](https://docs.djangoproject.com/en/3.0/ref/forms/api/#binding-uploaded-files-to-a-form), that is:
	* adding `enctype="multipart/form-data"` in the `form` tag
	* when you use the form, you need to bind the file data.
2. use `request.FILES` as well as `request.POST` in `views.py` working out the logic

---

## [SO: Bootstrap 4 - table -column sizing](https://stackoverflow.com/questions/37924104/table-column-sizing)

```html
<thead>
     <tr>
           <th class="w-25">25</th>
           <th class="w-50">50</th>
           <th class="w-25">25</th>
     </tr>
</thead>
```
or
```html
<table class="table table-bordered">
        <thead>
            <tr class="d-flex">
                <th class="col-3">25%</th>
                <th class="col-3">25%</th>
                <th class="col-6">50%</th>
            </tr>
        </thead>
        <tbody>
            <tr class="d-flex">
                <td class="col-sm-3">..</td>
                <td class="col-sm-3">..</td>
                <td class="col-sm-6">..</td>
            </tr>
        </tbody>
</table>
```

---

## [Django - build_absolute_uri, official doc](https://docs.djangoproject.com/en/3.0/ref/request-response/#django.http.HttpRequest.build_absolute_uri)
```bash
>>> request.build_absolute_uri()
'https://example.com/music/bands/the_beatles/?print=true'
>>> request.build_absolute_uri('/bands/')
'https://example.com/bands/'
>>> request.build_absolute_uri('https://example2.com/bands/')
'https://example2.com/bands/'
```
[_SO post_](https://stackoverflow.com/questions/2345708/how-can-i-get-the-full-absolute-url-with-domain-in-django)

---

## [DetailView kwarg setting](https://stackoverflow.com/questions/60113746/django-detailview-get-object-function-confusion)
if you wish not use `pk` as the default queryset kwarg, you can change it by defining `pk_url_kwarg = 'ref'` in the view.

---

## [*args and **kwargs](https://www.geeksforgeeks.org/args-kwargs-python/)
* `*args` and `**kwargs` can represent a number of arguments when defining a function
* they can also be used to call a function requires positional parameters 

---

## [SO: prevent repeat form submission](https://stackoverflow.com/questions/15671335/prevent-multiple-form-submissions-in-django)
Easy fix on client side: `onclick="this.disabled=true,this.form.submit();"` attribute added on submit button, however, server side should be double prove as well... check the post
