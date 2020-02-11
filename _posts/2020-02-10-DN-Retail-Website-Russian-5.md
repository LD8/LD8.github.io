---
layout: post
title:  "Daily Notes: Retail Website in Russian P5"
date:   2020-02-10 15:03:17 +0800
categories: Daily Notes
---

## Django:
---
### ForeignKey.on_delete
`ForeignKey` takes in `on_delete` argument to deal with the situation where the `ForeignKey` object is being deleted. The value of `on_delete` argument represents this behaviour. There are 3 options (In the examples below, `Item` model has `Category` as a `ForeignKey`):
* `on_delete=models.CASCADE` : deleting a foreign object, i.e. a `category`, will automatically delete all of the `item`s under that category
* `on_delete=models.PROTECT`: deleteing a foreign object, i.e. a `category`, will automatically prompt 'Can Not Delete' if there's `item`s under that category, And the `item`s will be listed
* `on_delete=models.SET_NULL`: deleteing a foreign object, i.e. a `category`, will set this foreign key to `Null` in all of the `item`s originally under that category.



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