---
layout: post
title: Summary of the Day
---
### Django _Learning Log_ rebuild project notes:

1. no reverse match error: normally there's a typo in your code, mainly on the `url tag`, or url pattern error in url.py file

2. `import time`: `time.time()` displays the time from 1970 in seconds

3. `time` in strings: `'Note - {}'.format(time.strftime('%b %d, %Y'))`, in month date, Year order, specifics check [python doc > time.strftime()](https://docs.python.org/3/library/time.html#time.strftime)

4. Url dispatcher and regex: you can use regex with `re_path('regex-in-here/', views.some_view_function)`, `(?P\<name\>regex)` is the same as `<int: name>` if it's integer. More to read on [Django URL dispatcher](https://docs.djangoproject.com/en/3.0/topics/http/urls/)

5. Mechanism of Django: models \> urls \> views \> html \> forms, and don't forget to add your new apps in settings, your models in `admin.site.register(ModelName)`, `include('new_app.urls')`

6. forms.py: from Django import forms, from .models import ModelName, class FormName inherits(forms.ModelForm), sub-class ` class Meta:` capitalised, examples:
    ```python
    class TopicForm(forms.ModelForm):
        class Meta:
            model = Topic
            fields = ['text']
            labels = {'text': ''}

    class EntryForm(forms.ModelForm):
        class Meta:
            model = Entry
            fields = ['title', 'text']
            labels = {'title': 'Title', 'text': ''}
            widgets = {'text': forms.Textarea(attrs={'cols': 80})}
    ```
7. filter in templates:
	```html
		<!-- | is a template filter: a function modifies the value in a template variable -->
		<p>\{\{ entry.date_added | date:'M-d-y H:i' \}\}</p>
		<p>\{\{ entry.text | linebreaks \}\}</p>
   ```

8. Jekyll won't render markdown with liquid tag like this \{\%\%\} 