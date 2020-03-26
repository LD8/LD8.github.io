---
layout: post
title: "JavaScript Notes: DOM Manipulation Recap"
categories: JavaScript Notes
---

## [DOM manipulation](https://www.youtube.com/watch?v=eaLKqoB9Fu0&list=PLWKjhJtqVAbllLK6r2dnGjUVWB_cFNcuO&index=2&t=0s)

### **Selection**

- select the element with `id="div1"`:
  ```js
  const div1 = document.getElementById("div1");
  ```
- select elements with `class="unicycle"`:

  ```js
  const unicycle = document.getElementsByClassName("unicycle");
  ```

  be careful `Elements` is plural

- select elements with `class="unicycle"` inside the div `id="div1"`:

  ```js
  const unicycle = div1.getElementsByClassName("unicycle");
  ```

  be careful `Elements` is plural

- select all `<p></p>` elements:

  ```js
  const paragraphs = document.getElementsByTagName("p");
  ```

- `.querySelector`: returns the FIRST element within the document that matches the specified group of selectors, or null if no matches are found  
  This is more concise and a more modern way to select elements.

  ```js
  const queryUnicycle = document.querySelector(".unicycle");
  const queryDiv2 = document.querySelector("#div2");
  ```

- `.querySelectorALL`: returns an array of objects with the elements that match the specified group of selectors  
  This is more concise and a more modern way to select elements.

  ```js
  const queryAll = document.querySelectorAll(".unicycle, #div2");
  ```

  ### **looping**: You can use a `forEach()` method to loop through all the element selected

  ```js
  let all_images = document.querySelectorAll(".article_image");

  all_images.forEach(image => {
    console.log("image: ", image);
  });
  ```

  ### You can always preview the select result by using `console.table()`:

  ```js
  let all_images = document.querySelectorAll(".article_image");

  console.table(all_images);
  ```

### **Changing** elements

- `var.innerHTML`

  ```js
  const queryAll = document.querySelectorAll(".unicycle, #div2");

  let text = "<h1>Hello World</h1>";

  for (let i = 0; i < queryAll.length; i++) {
    queryAll[i].innerHTML = text;
  }
  ```

  However, `.innerHTML` can be subject to cyber attack, so:

- `var.textContent`

  ```js
  const queryUnicycle = document.querySelector(".unicycle");

  let text = "<h1>Hello World</h1>";

  queryUnicycle.textContent = text;
  // will be replaced by plain text including <h1></h1> tags
  ```

- ### changing style:

  - `var.style.color = 'red';`
  - `var.style.background = 'red';`
  - `var.style.boxShadow = "2px 2px 5px 1px blue";`

  Following settings will remove original _inline style_ from the tag and replace it with the following settings, or you can do the above to set one style at a time to avoid this behavior:

  - `var.style.cssText = "color: blue; border: 1px solid black";`
  - `var.setAttribute("style", "color: red; border: 1px solid blue;");`

  **Refs**:

  - `console.log(var.style);`, any var would do the trick, you can expand the output in the console to check out what styles have already and haven't been assigned to this variable, however, only including the inline style settings
  - `console.log(window.getComputedStyle(var));` will get all the styles including the style defined in css files
  - [more css style keyword refs](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Properties_Reference)

## DOM EVENT Handlers

### **`onclick`** event applied in html tags:

- `onclick="this.innerHTML='Passion Fruit!'"`
  ```html
  <h1 onclick="this.innerHTML='Passion Fruit!'">What's my favorite food?</h1>
  ```
- `onclick="changeColor(this)"`
  ```html
  <h1 onclick="changeColor(this)">What's the color "Peru"?</h1>
  ```
  ```js
  function changeColor(obj) {
    obj.style.color = "Peru";
  }
  ```

### use `id` to invoke a JS function:

- `document.getElementById("myBtn").onclick = changeColor;`
  ```js
  function changeColor(obj) {
    obj.style.color = "Peru";
  }
  document.getElementById("myBtn").onclick = changeColor;
  ```

### **`onload`** event on a <body> tag

### **`oninput`** event in `<input />` tag

- `<input type="text" id="words" oninput="func()" />`
  do something on every keyboard stroke

### **`onmouseover`** and **`onmouseout`** events

```html
<div onmouseover="mOver(this)" onmouseout="mOut(this)" class="box">
  Mouse Over Me
</div>
```

```js
function mOver(obj) {
  obj.innerHTML = "GET OFF ME!";
}

function mOut(obj) {
  obj.innerHTML = "Thank You! 😺";
}
```

### All DOM Events: [w3school.com ref](https://www.w3schools.com/jsref/dom_obj_event.asp)

## **`addEventListener(event, function, useCapture)`**

`useCapture` is default by `false` -- take effect from inside -> outside
if set to `true` -- from outside -> inside

```js
// myP is a child of myDiv
const myDiv = document.getElementById("myDiv");
const myP = document.getElementById("myP");

myDiv.addEventListener("click", changeBackground, true);
myP.addEventListener("click", changeTextContent, true); //default useCapture value is false

function changeBackground(obj) {
  obj.style.background = "Peru";
}

function changeTextContent(obj) {
  obj.textContent = "Peru";
}

myP.addEventListener("mouseover", changeSomething);
```

Different to `onclick`, `addEventListener()` can add more than one function to the element, `onclick` can only apply one and the latter takes precedence.

## **`removeEventListener(event, function, useCapture)`**

Remember to pass in _exactly_ how corresponding `addEventListener`'s argument are set.
