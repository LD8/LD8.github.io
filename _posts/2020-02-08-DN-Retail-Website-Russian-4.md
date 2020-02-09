---
layout: post
title:  "Daily Notes: Retail Website in Russian P4"
date:   2020-02-08 10:31:17 +0800
categories: Daily Notes
---

## Django:
---
### template {{forloop.counter0}}
```html
{% for image in item.itemimage_set.all %}
<li data-target="#item{{item.pk}}Carousel" data-slide-to="{{forloop.counter0}}"></li>
{% endfor %}
```
`forloop.counter0` is 0-indexed
`forloop.counter` is 1-indexed

more variables in forloop can be found [here](https://docs.djangoproject.com/en/3.0/ref/templates/builtins/#for):
* forloop.revcounter
* forloop.revcounter0
* forloop.first
* forloop.last
* forloop.parentloop
