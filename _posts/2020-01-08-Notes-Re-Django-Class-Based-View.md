---
layout: post
---
# [Introduction: Class-based Views](https://docs.djangoproject.com/en/3.0/topics/class-based-views/)
- **WHY class-based views**:
	* _more useful in many real-world applications_: easy to extend and customise
	* _mixins_ provides a toolkit which makes class-based views are more flexible and extensible
	* _class-based generic views_: built for maximum flexibility, many hooks (default method implementations and attributes), a fresh approach to the same problems that function-based views

- **CORE**:
	respond to HTTP request methods with different class instance methods, instead of conditionally branching code inside a single view function
	
- top -> down:
	 django URL resolver sends HTTP request to a callable function --> `as_view()` function is called turns a class-based view to a function (an instance of the class) --> `setup()` is called to initialise its attributes --> `dispatch()` is called to look at the request, GET or POST etc --> match the method, give response

- Two ways to set class attributes
	1. subclassing and overriding attributes(variables) and methods in the subclass
	2. configure class attributes(variables) as keyword arguments to the as_view() call in the URLconf:
```python
urlpatterns = [
    path('about/', GreetingView.as_view(greeting="G'day")),
]
```

- **Mixins**
>Mixins are a form of multiple inheritance where behaviors and attributes of multiple parent classes can be combined.

	* an excellent way of reusing code from multiple classes, however, can be hard to read
	* only ONE parent class can inherit from _View_, and the rest should be mixins, i.e. you can only inherit from ONE _generic view_

- Handling forms:
```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from django.views import View

from .forms import MyForm

class MyFormView(View):
    form_class = MyForm
    initial = {'key': 'value'}
    template_name = 'form_template.html'

    def get(self, request, *args, **kwargs):
        form = self.form_class(initial=self.initial)
        return render(request, self.template_name, {'form': form})

    def post(self, request, *args, **kwargs):
        form = self.form_class(request.POST)
        if form.is_valid():
            # <process form cleaned data>
            return HttpResponseRedirect('/success/')

        return render(request, self.template_name, {'form': form})
```
You can then have subclass which overrides some of the attributes/methods or via URLconf configuration to customise the view.

- Decorating class-based views: **Two** ways
	1. [Decorating in URLconf](https://docs.djangoproject.com/en/3.0/topics/class-based-views/intro/#decorating-in-urlconf)
	2. [Decorating the class](https://docs.djangoproject.com/en/3.0/topics/class-based-views/intro/#decorating-the-class)
		* `@method_decorator(login_required)`can be used on `def dispatch()`method in a class OR
		* `@method_decorator(login_required, name='dispatch')` can be used directly on the class
			- `decorators = [never_cache, login_required]`
			- `@method_decorator(decorators, name='dispatch')`
			- you can use a list or a tuple of decorators to simplify the process


# [Built-in class-based generic views](https://docs.djangoproject.com/en/3.0/topics/class-based-views/generic-display/#built-in-class-based-generic-views)
### The functionality of generic views:
- Display list and detail pages for a single object. If we were creating an application to manage conferences then a TalkListView and a RegisteredUserListView would be examples of list views. A single talk page is an example of what we call a “detail” view.
- Present date-based objects in year/month/day archive pages, associated detail, and “latest” pages.
- Allow users to create, update, and delete objects – with or without authorization.

#### [All generic class-based views ref](https://docs.djangoproject.com/en/3.0/ref/class-based-views/)

### [Built-in class-based generic views](https://docs.djangoproject.com/en/3.0/topics/class-based-views/generic-display/#built-in-class-based-generic-views)
This section explains how to extend generic views and the mechanism to display objects

### Most commonly used views for displaying data
1. [DetailView](https://docs.djangoproject.com/en/3.0/ref/class-based-views/generic-display/#detailview)
2. [ListView](https://docs.djangoproject.com/en/3.0/ref/class-based-views/generic-display/#listview)

### [Form handling with class-based views](https://docs.djangoproject.com/en/3.0/topics/class-based-views/generic-editing/#form-handling-with-class-based-views)
1. Basic forms
2. Model forms
3. Models and request.user
4. AJAX example

### [Using mixins with class-based views](https://docs.djangoproject.com/en/3.0/topics/class-based-views/mixins/)
[mixins list](https://docs.djangoproject.com/en/3.0/ref/class-based-views/mixins/)
I need time to digest... will do some experiments myself with class-based views now...