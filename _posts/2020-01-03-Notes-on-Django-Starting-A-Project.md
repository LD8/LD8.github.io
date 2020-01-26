---
layout: post
---
#### A bit chaotic this afternoon
something to note here when rebuilding or constructing a new project on Django
1. **NoReverseMatch error**: this happens too often, a few reasons
	- wrong liquid tag: easy to spot, either missing parts of the tag or wrong url
	- incorrect argument format: `topic.pk` instead of `topic_pk` in liquid tag
	- view function: missing _argument_ passing in render **context** as well as in **redirect('url', pk=pk)**

2. Do not mess with migrations folders, I uninstalled django and reinstalled it to make the migration error go away. The reason I messed with them was because the `models.py` file isn't working properly. `makemigrations` doesn't seem to work either. Because I'd made some changes in models but `makemigrations` won't spot the difference so wouldn't apply the changes to database. I changed from `date_created=DateField(auto_now_add=True)` to `date_added=models.DateTimeField(auto_now_add=True)` 
	The solution is `date_added=models.DateTimeField(auto_now_add=True, null=True, blank=True)` The additional optional arguments complete the fucntion so when it migrates, no date time is required while it's being migrated. So that solved the problem
	
3. In `form` action attribute should be the url of the view function which handles the form submission. I made a mistake pointing action to the url after the form is submitted, which resulted in whatever info being submitted went missing... So fixed that silly one... The view function will redirect the user to the correct url after submission. However, I'm not sure if \<input type="hidden" name="next" value="url liquid tag" \> is still needed inside the form though...
