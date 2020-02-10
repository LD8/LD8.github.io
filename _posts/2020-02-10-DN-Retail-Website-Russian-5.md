---
layout: post
title:  "Daily Notes: Retail Website in Russian P5"
date:   2020-02-10 15:03:17 +0800
categories: Daily Notes
---

## Django:
---
### {\% verbatim %\}
you can use {\% verbatim %\} to stop template tags from laoding:
{% verbatim %}
{% verbatim %}
	{% something here won't be effective %}
		{% for q in queryset %}
		{% endfor %}
	{% endverbatim %}
{% endverbatim %}



## Python
---
### why @property decorator? [SO question ref](https://stackoverflow.com/questions/58558989/what-does-djangos-property-do)

* the function within the class can be accessed like a class' property instead of a function()
  1. you don't have to call it with `()` to gain access to the result
  2. you can pass parameter into it and process existing class values
    ```python
    class Person(models.Model):
      first_name = models.CharField(max_length=50)
      last_name = models.CharField(max_length=50)
      birth_date = models.DateField()
      
      @property
      def full_name(self):
        "Returns the person's full name."
        return '%s %s' % (self.first_name, self.last_name)
      
      @full_name.setter
      def full_name(self, value):
         names = value.split(' ')
         self.first_name = names[0]
         self.last_name = names[1]
    ```
  

## VS Code
---
Shortcuts - Find and replace:
* Find: command + F
* Add next occurrence: command + D
* Add ALL occurrences: option + Enter
* Replacement: command + option + F
* Replace: command + Enter