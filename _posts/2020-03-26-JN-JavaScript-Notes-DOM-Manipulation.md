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
  document.getElementById("myBtn").onclick = changeColor; // no quotation marks needed and no function can be chained
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

## DOM Nodes

Everything in an HTML document is a node. `<html>` is a `root node`, it can have numerous children. The most common ones include `<body>` node and `<head>` node, and they are, to each other, `siblings`. `<html>` node is their `parent` node.

### common DOM Node creation methods:

- `const para = document.createElement('p')`
- `const text = document.createTextNode("This is a Text Node")`
- `para.appendChild(text)`
- `para.innerHTML = "This is a new text node"`
- `parent.insertBefore(para, child)`: insert para node before child node inside parent node
- `parent.replaceChild(para, child)`: Use para node to replace child node inside parent node
- `parent.removeChild(para)`: remove para node inside parent node

## some CSS recap:

- `relative` position:
  - This element still takes its place, with its height and width intact
  - it can be moved around with position properties like `left: 10%` or `top:10%`, The original position remains in the DOM tree, meaning it still occupies as the original before it's moved
- `absolute` position:
  - This removes the element from the DOM tree, as if it never happened
  - its zero position refers to the first parent node which isn't in static position, OR refers to the viewport/window if all nodes are in default position
- `static` position: default poisiton, goes with the flow, its position can NOT be manipulated

## DOM Animation

### If a child is to be animated, the parent element must be `relative` position, and the child should be `absolute` position.

You can use the traditional method:

- `let id = setInterval(func, 10);`
- `clearInterval(id);`  
  example code:

  ```js
  animateBox.addEventListener("click", moveAcross);

  function moveAcross() {
    let pos = 0;
    let id = setInterval(frame, 1);
    function frame() {
      if (pos > 500) {
        clearInterval(id);
      } else {
        pos++;
        animateBox.style.top = pos + "px";
        animateBox.style.left = pos + "px";
      }
    }
  }
  ```

### Another method is `Element.animate()`, checkout the **[Github Repo here](https://github.com/web-animations/web-animations-js)**

Example code:

```js
var animation = aBox.animate(
  {
    opacity: [0.8, 1],
    transform: ["scale(0.1)", "scale(1)"]
  },
  {
    direction: "alternate",
    duration: 500,
    iterations: Infinity
  }
);
```

## Window Object

Each tab in a Web Browser has its own window Object.

### Window Properties:

- `window.innerWidth`
- `window.innerHeight`

  ### Example of Usage:

  ```js
  // create the node, has to be declared before adding the listener
  const pMonitor = document.createElement("h2");
  pMonitor.style.cssText = "text-align: center";
  document.body.insertBefore(pMonitor, animateDiv);

  // add event listener
  window.addEventListener("resize", updateSizeText);
  // run the update function to update the text shown in the node above
  updateSizeText();

  function updateSizeText() {
    pMonitor.textContent =
      "window's Height: " +
      window.innerHeight +
      ";\n window's Width: " +
      window.innerWidth;
  }
  ```

### use window Object to **OPEN** a window

```js
const openVABtn = document.querySelector("#openVABtn");
openVABtn.addEventListener("click", openVA);
let newWindowObj;
function openVA() {
  newWindowObj = window.open(
    "https://va-boutique.com",
    "newWindow",
    "menubar=false,location=true,resizable=true,scrollbars=true,width=800,height=500,top=-1000,left=00"
  );
}
```

### use window Object to **CLOSE** a window

```js
const closeVABtn = document.querySelector("#closeVABtn");
closeVABtn.onclick = closeWindow;
function closeWindow() {
  newWindowObj.close();
}
```

### usage of `window name` ("newWindow" in the above example)

Use an anchor tag's `target` attribute to open a new link inside the `"newWindow"`.

```html
<a target="newWindow" href="https://DonLee.online">Visit Don Lee</a>
```

### `move()` a window and gain `focus()`:

```js
function move() {
  window.move(50, 0); // push the window to the right by 50px
  window.focus(); // pop the window to the top of the screen
}
```

## use const to define a function: POSITION matters

`const funcName = () => {...}`  
This function can not be accessed before it's been declared, however:  
`function funcName() {...}`  
CAN be accessed anywhere in the document.

## Pop up boxes: window objects method

There are 3 kinds of pop up boxes, and ALL are methods of the `window` Object. The position of the pop up boxes can NOT be controled. Use with caution.

- `window.alert("message")`
  ```js
  alert("CAN NOT ACCESS");
  ```
- `window.confirm("message")`: let user choose 'yes' or 'cancel', it returns a boolean
  ```js
  if (confirm("want to quit?")) {
    // redirect to another page
  } else {
    // e.g. encourage user to stay
  }
  ```
- `window.prompt("message")`: this can be used to interact with users, a bit like `input()` method in python

  ```js
  let userName = prompt("Please type in your name");
  // userName will catch the input from user

  if (userName == null || userName === "") {
    console.log("user cancelled the prompt");
  } else {
    console.log("Hello " + userName + "!");
  }
  ```

## `cookies` VS `localStorage` VS `sessionStorage`

### The comparison table

|                        | cookies            | localStorage | sessionStorage |
| ---------------------: | ------------------ | ------------ | -------------- |
|           **Capacity** | 4kb                | 10mb         | 5mb            |
|           **Browsers** | HTML4/HTML5        | HTML5        | HTML5          |
|    **Accessible from** | Any window         | Any window   | same tab       |
|            **Expires** | Manually set       | Never        | On tab close   |
|   **Storage Location** | Browser and server | Browser only | Browser only   |
| **Sent with requests** | yes                | No           | No             |


### USAGE: You can check in **`Application tab`** in the `Developer Tools` with Chrome Browser:

- ### localStorage examples:
  
  ```js
  localstorage.setItem("breakfast", "cereal"); // items are defined by key-value pairs
  // to retrieve value
  console.log(localStorage.getItem("breakfast")); // cereal
  ```

- ### sessionStorage examples:

  ```js
  sessionstorage.setItem("breakfast", "cereal");// items are defined by key-value pairs
  // to retrieve value
  console.log(sessionStorage.getItem("breakfast")); // cereal
  ```

#### The Storage does not clear the storage data automatically, to clear the data:
```js
// to clear the value stored in 'breakfast'
localStorage.removeItem('breakfast');
// clear all the items
localStorage.clear()
```

- ### cookie examples:  
  * a simple cookie
    ```js
    document.cookie = "hello=true"; // defined by key-value pair as well
    ```
  * cookie with `expiration date`
    ```js
    document.cookie = "person=don; expires=Fri, 31 Dec 9999 23:59:59 GMT";
    ```
  * cookie with `expiration date` and `path`
    ```js
    document.cookie = "person=don; expires=Fri, 31 Dec 9999 23:59:59 GMT; path=/";
    ```
  * to `delete` a cookie  
    Set the `person` to `blank` and set an passed date to `expires`
    ```js
    document.cookie = "person=; expires=Fri, 31 Dec 1970 23:59:59 GMT; path=/";

    ```

## `history` property of the `window` Object

`window.history` has many methods, the most common ones:

- `history.back()`: go back one page in history
- `history.forward()`: go forward one page in history
- `history.go(-2)`: go back a number of pages in history (if the value is negative) or go forward (if the value is positive)
- `history.replaceStage('something to pass on to the new page', null, 'a_link_or_to_replace_partial_domain_name_in_string')`: replace current history page with the third value passed in, and pass on the first value as a state, you can `console.log(history.state)` to check it
- `history.pushState(null, null, a_link_or_to_replace_partial_domain_name_in_string')`: to create a new history page with the link value passed in  
  **Note**: the last two methods will NOT refresh the page or go to the link passed in, it merely changed the appearance of the link in browser
