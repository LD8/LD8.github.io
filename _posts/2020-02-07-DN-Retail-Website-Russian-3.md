---
layout: post
title:  "Daily Notes: Retail Website in Russian"
date:   2020-02-07 17:30:17 +0800
categories: Daily Notes
---

## Django:
---
### in `urls.py` be careful which goes first  
```python
app_name = 'boutique'
urlpatterns = [
    # show index page
    path('', views.IndexView.as_view(), name='index'),
    
    # show a specific item
    path('item_<int:item_pk>/', views.ItemDetailView.as_view(), name='item'),

    # show categories of products for men or women
    path('<slug:gender>/', views.CategoryListView.as_view(), name='show-all'),

    # show a specific category for men or women
    path('<slug:gender>/cat_<int:category_pk>/', views.CategoryListView.as_view(), name='category'),

    # show a specific subcategory under a specific category for men or women
    path('<slug:gender>/cat_<int:category_pk>/subcat_<int:subcategory_pk>/', views.CategoryListView.as_view(), name='subcategory'),

]
```

**In this file, if you put `item` after `subcategory`, `item` view will never render the correct page, as it will be hjacked by `ListView`s before it.**

---
### models - gender choices  
example below:
```python
class Category(models.Model):
    '''Category for men's and women's items'''
    gender = models.IntegerField(choices=[
        (1, 'Women'),
        (2, 'Men'),
    ], default=1)
    name = models.CharField(max_length=100)
    description = models.CharField(max_length=300, blank=True)
    uploaded_date = models.DateTimeField(
        auto_now_add=True, null=True, blank=True)

    class Meta():
        verbose_name_plural = 'Categories'

    def __str__(self):
        return self.get_gender_display() + ' ' + self.name

    def get_category_url(self):
        return reverse('boutique:category', kwargs={'gender': self.get_gender_display(), 'category_pk': self.pk})
```
#### A couple of things to notice in this example:
* gender's choices: 
		* `choices` is in small letters
		* `choices` is a list with tuples
* access gender's choices: you can use `get_FOO_display()` to access a field's choices, checkout [this](https://docs.djangoproject.com/en/3.0/ref/models/instances/#extra-instance-methods) for more details



---
### `def get_absolute_url()` in `models.py`  

models.py  
```python
from django.urls import reverse

class SomeModel(models.Model):
	<---snip--->
	
	# it can be any name you like because it doesn't seem to be inheriting from anything
	def get_absolute_url(self):
		return reverse('app_name:view_name', kwargs={'key': self.field_name})
		# reverse returns a string for href content
```
When implementing the url into html tag, make sure SomeModel is accessable (either by iteration or it is an object passed through context):  
*html 
```html
<a href="{{ SomeModel.get_absolute_url }}">link</a>
```

### dynamic handling url: checkout this [SO question](https://stackoverflow.com/questions/60111776/django-models-py-does-get-absolute-url-function-take-conditions/60111926#60111926) I posted
based on the answer by _Mathias_, it's not possible unless installing a [django-middleware-global-request](https://pypi.org/project/django-middleware-global-request/)
Then you can:
```python
from django_global_request.middleware import get_request

class TestModel(models.Model):

    ...

    def get_absolute_url(self):
        request = get_request()

        if request.GET.get('whatever'):
            return ...
        else:
            return ...
```
> You just need to make sure, you could still access this method without any available request. So make sure it's fail save and has a fallback in case of the absence of a request (like in a shell, upgrade, etc.)

---
### `get_queryset(self)` and `get_context_data(self, **kwargs)` in `ListView` - [CCBV](https://ccbv.co.uk/projects/Django/3.0/django.views.generic.list/ListView/)
* __`get_queryset()` is more useful in `ListView`, because it returns a queryset for templates to render. `queryset`s are passed through context automagically! __
* __if a model is defined in the CBV, `get_queryset()` will automatically run and acquire `model.objects.all()`, if to override it:__
  ```python
  class SomeView(ListView):
    <---snip--->
    # context_object_name represents the result of get_queryset()
    # you can directly access this from the template even if you do not set context_object_name
    context_object_name = 'coobna'
    
    
    def get_queryset(self):
      # if you still need the default functionality of this function
      # it's like to inherite from it's superior, instead of writing a whole new function
      queryset = super().get_queryset()
      
      # before returning the queryset, you can print(queryset) to debug or for referencing purposes
      return queryset.filter(........)
      
      
    def get_context_data(self, **kwargs):
      # inherite the functionality from its 'superior'
      context = super().get_context_data(**kwargs)
      
      # now you can add more context to it
      new_context = Category.objects.all()
      context['new_context'] = new_context
      
      # you can always print out the context for debugging
      print(context)
      return context
  ```
  
* __An example of a ListView:__
  ```python
  class CategoryListView(ListView):
    '''display a list of items'''
    model = Category
    template_name = 'boutique/items.html'
    # context_object_name is actually the result of `get_queryset()`
    context_object_name = 'category_shown'
    # paginate_by = 12

    def get_queryset(self):
        # get original queryset: Category.objects.all()
        qs = super().get_queryset()

        # filter men/women
        if self.kwargs.get('gender') == 'Women':
            qs = qs.filter(gender=1)
        elif self.kwargs['gender'] == 'Men':
            qs = qs.filter(gender=2)

        if self.kwargs.get('category_pk'):
            qs = qs.filter(pk=self.kwargs.get('category_pk'))

        # print('\nqs= ', qs, '\n')
        return qs

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        # add categories for navbar link texts
        context['categories'] = Category.objects.all()

        if self.kwargs.get('subcategory_pk'):
            context['subcategory_shown'] = get_object_or_404(
                SubCategory, pk=self.kwargs.get('subcategory_pk'))
            context['item_list'] = Item.objects.filter(
                subcategory=self.kwargs.get('subcategory_pk'))
            # print('\ncontext with subcat= ', context, '\n')
            return context

        # Because context_object_name actually represents the result of `get_queryset()`
        # Therefore, if context_object_name is set to the same name as the context name
        # the following expression can be omitted
        # context['category_shown'] = self.get_queryset()
        # The benefit of this is you don't need to run get_queryset() again!!

        if self.kwargs.get('category_pk'):
            context['item_list'] = Item.objects.filter(
                category=self.kwargs.get('category_pk'))

        # print('\ncontext= ', context, '\n')
        return context
  ```
  
---
### DetailView in [CCBV](https://ccbv.co.uk/projects/Django/3.0/django.views.generic.detail/DetailView/)

example  
```python
class ItemDetailView(DetailView):
    '''display an individual item'''
    model = Item
    template_name = 'boutique/item.html'
    # no need to specify as default context_object_name depends on the model
    # they are actually the same (with lower case first letter)
    # context_object_name = 'item'
```
A couple of things to note:
* context_object_name (see comment above)
* Two lines are sufficient for displaying standard DetailView of an item
### HOWEVER:
One should be very careful about the url value passed into the CBV, checkout [my question](https://stackoverflow.com/questions/60113746/django-detailview-get-object-function-confusion) on StackO.
url.py
```python
urlpatterns = [
	path('item_<int:pk>/', views.ItemDetailView.as_view(), name='item'),
]
```
I used **`path('item_<int:item_pk>/'...)`** that's why it didn't work. For DetailView to work, you either have to pass in `<pk>` or specify in your CBV of your `pk_url_kwarg = 'item_pk'`. [docs](https://docs.djangoproject.com/en/3.0/topics/class-based-views/generic-display/#performing-extra-work)


