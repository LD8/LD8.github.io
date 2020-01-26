---
layout: post
---
* Collapsible button:

```html
<button class="btn btn-outline-success shadow-sm mb-2" 
        data-toggle="collapse" 
        data-target="#topicMenu" 
        aria-expanded="false" 
        aria-controls="topicMenu" 
        style="width:90%;">
  			- Other Topics -</button>
        <!-- aria-controls tag is for accessability purposes-->

<div class="collapse" id="topicMenu">
  stuff to collapse
</div>
```



* Django template: to include other templates

  ```html
  {\ include 'htmlfile' with variable1 variable2 \}
  ```

  In the included file, you can use variables to realise flexible design

  

* Adding a footer:

  All you need to do is to set the position of the footer tag to `sticky`, and `top: 100%`, VOILA! The key is to push the footer element a-whole-viewport-height length from the top.

* Warning before deleting 'POST':

  ```javascript
  <script>
          $(document).on('click', '.confirm-delete', function () {
              return confirm('Are you sure you want to delete this?');
          });
  </script>
  ```

  And then add the class `confirm-delete` to the element controlling the deletion e.g. a button named 'delete' in *edit_post* view.

  

* [FontAwesome](https://fontawesome.com/)

  A great source for signage, brands, general icon content, which makes your website come to live. You can even `fa-spin` the icon. Many feature to discover...

* Need to learn more javascript, jQuery and React framework this month in order to create a more vibrant and exciting website/content... Do I need to know AJAX as well?

