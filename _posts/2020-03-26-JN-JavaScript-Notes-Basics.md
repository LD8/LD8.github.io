---
layout: post
title: "JavaScript Notes: Basics"
categories: JavaScript Notes
---

I decide to spend 3 hours to review all of the basics of JS, and make comments and record the basics of the stuff I don't quit remember.
Thanks for the amazing [FCC video](https://www.youtube.com/watch?v=PkZNo7MFNFg) by Beau Carnes!

## Comment

```js
// single line comment
or;
/* multi line
comment */
```

## var, let and const

var can be global
let is a block variable
const can never be changed

## naming vars

declare camelCase

## use backflash to escape

`var myStr = "this is \"a string\" called myStr!"`

```js
/****
CODE    OUTPUT
\'      single quote
\"      double quote
\\      backslash
\n      newline
\r      carriage return
\t      tab
\b      backspace
\f      form feed
****/
```

## common properties

- String.length

## Array

- mutable
- ```js
  -front  +front         +back       -back
  shift() unshift("dog") push("dog") pop()
  ```
- `JSON.stringify(Arr)` to print a readable array

## return in functions

if a function does not return anything, the return value is 'undefined', meaning `console.log(thisfunction())` will be 'undefined'

## scope

vars with the same name: local scope take the precedence

## switch() {}

```js
function caseInSwitch(val) {
  var answer = "";
  // what switch function does is to compare the val param with what's after the case key word
  switch (val) {
    case 1:
    case 5:
    case 9:
      answer = "alpha";
      break;
    //will immediately break out of the loop, return, when answer is assigned
    case 2:
      answer = "beta";
      break;
    case 3:
      answer = "gamma";
      break;
    default:
      answer = "something";
      break;
  }

  return answer;
}
```

## Object

- use bracket notation to access properties when property name has _space_ in it
- use bracket notation to access properties when a variable is in the bracket
- `delete object.key` to delete prperty
- replace switch function with a dict/object
  = `object.hasOwnProperty(Prop)` to check if an object has the property 'Prop'
- an object can be within an array and vice versa

## while loop

```js
let i = 0;
while (i < 5) {
  // do something
  i++;
}
```

## for loop

```js
for (let i = 0; i < 6; i += 2) {
  // do something
}
```

## do...while loop

```js
let i = 0;
do {
  // do something
  i++;
} while (i < 5);
```

## number

- Math.random(): produce from 0-0.9999999...
- Math.floor(): 9.9=>9, 12.4=>12, 34.8=>34

```js
function randomRange(myMin, myMax) {
  // generate random number between myMin and myMax
  let num = Math.floor(Math.random() * (myMax - myMin + 1) + myMin);
  return num;
}
```

## convert string to integer

```js
parseInt(str); // a decimal number
parseInt(str, 2); // convert a binary number into a decimal number
```

## ternary operator

```js
// condition ? statement-if-true : statement-if-false
function () {
    // return a === b ? true : false;
    // or
    return a === b ? true : a > b ? 'a is greater than b' : 'b is greater than a';
}
```

## "use strict";

to avoid common coding mistakes and unsafe actions, usually at the top of the js file or at the top of a function

## let

- declare variables with `let`: can not declare twice with the same name, however, `var` can
- `var` variable can not be block scoped, `let` variables are

## const

- use all capital letters for a const variable name
- read only
- can not be reassigned
- an array can be mutated, even it's declared with `const` key word
- object can be mutated, even it's declared with `const` key word, however
  - **`Object.freeze(OBJ_NAME)`** can prevent `OBJ_NAME` to mutate

## Array.concat()

```js
const arr1 = [1, 2];
const arr2 = [3, 4];
arr1.concat(arr2); // returns a new array, arr1 and arr2 remain unchanged
```

## arrow function

- When a function takes another function as an argument, it's a good place to use arrow function, e.g. inside `arr.filter(num => num > 0).map(x => x**2)`
- Higher order arrow functions:

  ```js
  const increment = (function() {
    return function increment(number, value = 1) {
      return number + value;
    };
  })();

  console.log(increment(5, 2)); // 7
  console.log(increment(5)); // 6
  ```

## Rest Operator with Function Params

```js
const sum = function() {
  return function sum(x, y, z) {
    const args = [x, y, z];
    return args.reduce((a, b) => a + b, 0);
  };
};

console.log(sum(1, 2, 3)); // 6

// Rest operator
const sum = function() {
  return function sum(...args) {
    // any number of arguments will be processed
    return args.reduce((a, b) => a + b, 0);
  };
};

console.log(sum(1, 2, 3, 4)); // 10 and any number of arguments will be processed
```

## Spread Operator to evaluate arrays in-place

```js
const arr1 = [1, 2, 3, 4];
let arr2;

arr2 = arr1; // POINTING: This only points arr2 to arr1's value

arr2 = [...arr1]; // SPREADING: This creates a new array, spreading/copying every item in arr1
```

## Destructuring assignment to assign variables from objects

```js
const TEMP = {
  today: 28,
  tomorrow: 32
};

const { today: tempretureToday } = TEMP;

console.log(tempretureToday); // 32
```

### If the object is nested, then you need to destruct multiple times:

```js
const FORCAST = {
  today: { min: 72, max: 83 },
  tomorrow: { min: 73.3, max: 85 }
};

const {
  tomorrow: { max: maxOfTomorrow }
} = FORCAST;
console.log(maxOfTomorrow); // 85
```

## Destructuring assignment to assign vars from arrays

```js
const [z, x, , , y] = [1, 2, 3, 4, 5, 6];
console.log(z, x, y); // 1, 2, 5
```

### use this technique to 'swap' values between 2 vars

```js
let a = 8,
  b = 6;
(() => {
  "use strict";
  [a, b] = [b, a];
})();

console.log(a); // 6
console.log(b); // 8
```

## Destructureing assignment with Rest Operator

```js
const source = [1, 2, 3, 4, 5, 6, 7];

function removeFirstTwo(list) {
  const [, , ...newList] = list;

  return newList;
}

console.log(removeFirstTwo(source)); // [3,4,5,6,7]
```

## Destructuring Assignment to pass an Object as a function's params

```js
const myObj = {
  max: 33,
  min: 12,
  something_else: "something",
  a_lot_other_info: "..."
};

const half = (function() {
  return function half({ max, min }) {
    return (max + min) / 2.0;
  };
})();

console.log(half(myObj)); // 22.5
```

Instead of passing in the entire object, this only pass in the property you need, commonly used with API calls.

## Create strings using template literals (\` string \${obj.prop.value} \`)

```js
const person = {
    name: 'D L',
    age: 22
}

const string = `Hello, my name is ${person.name}!
I am ${person.age} years old.`;

console.log(string); /* "Hello, my name is D L!
I am 22 years old."
```

The result format will be exactly the same as defined. Remember to add dollar sign before {var} inside template literals.

## Write concise object literal declarations using simple fields

```js
const createPerson = (name, age, gender) => {
  return {
    name: name,
    age: age,
    gender: gender
  };
};

// to make it much simpler

const createPerson = (name, age, gender) => ({ name, age, gender });

console.log(createPerson("DL", 22, "male")); // {name: "DL", age: 22, gender: "male"}
```

## Write concise declarative functions

```js
const bike = {
  gear: 2,
  // can be simplified
  setGear: function(newGear) {
    "use strict";
    this.gear = newGear;
  }
};

const bike = {
  gear: 2,
  // simplified <---------
  setGear(newGear) {
    "use strict";
    this.gear = newGear;
  }
};

bike.setGear(3);
console.log(bike.gear); // 3
```

## Use class Syntax to define a constructor function

Old constructor function:

```js
var SpaceShuttle = function(targetPlanet) {
  this.targetPlanet = targetPlanet;
};
var zeus = new SpaceShuttle("Jupiter");

console.log(zeus.targetPlanet);
```

NEW WAY with class:

```js
class SapceShuttle {
  constructor(targetPlanet) {
    this.targetPlanet = targetPlanet;
  }
}
var zeus = new SpaceShuttle("Jupiter");

console.log(zeus.targetPlanet);
```

Wrapped in a function:

```js
function makeClass() {
  class SpaceShuttle {
    constructor(name) {
      this.targetPlanet = targetPlanet;
    }
  }
  return SpaceShuttle;
}
const SpaceShuttle = makeClass();
const zeus = new SpaceShuttle("Jupiter"); // an instance of the class
console.log(zeus.targetPlanet);
```

## Use getters and setters to control access to an object

So that the user won't have direct access to the obejct value

```js
function makeClass() {
  class Thermostat {
    constructor(temp) {
      this._temp = (5 / 9) * (temp - 32);
    }

    // getter
    get temperature() {
      return this._temp;
    }
    // setter
    set temperature(updatedTemp) {
      this._temp = updatedTemp;
    }
  }
  return Thermostat;
}

const Thermostat = makeClass();
const thermos = new Thermostat(76);

// use getter to get the temperature
let temp = thermos.temperature;
console.log(temp); // 24.44

// use setter to update the temperature
thermos.temperature = 30;
temp = thermos.temperature;
console.log(temp); // 30
```

BE CAREFUL how getter and setter are defined and used. They act as properties.

## export, import

### export:

- you can `export const funcName = () => {...}` directly before defining a variable or a function
- you can also `export { funcName };` at the end of the file
- export a default: `export default function subtract(x, y) { return y-x; }`

### import:

- you can `import { funcName } from "./file_name";`
- you can import all `import * as var_name from "./file_name";`, var_name is the variable/object_name stores the imported everything in
- import a default export: `import subtract from "./file_name";` no curly brackets needed

#####  require is an old way of use code from another file

## map, filter, reduce
