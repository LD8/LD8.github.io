---
layout: post
title:  "JavaScript Notes: Recap"
categories: JavaScript Notes
---

## The wired universe of JS
Thanks to Dan Abramov's amazing test course, I'm glad to be one of the guinea pigs.  
In this universe, `variables` are wires connecting the `values` and where it's been used.  
The format to assign a variable is:  
* `let var_name = expression`  
* `var_name` in small letters  
* `expression` can be complicated or primitive, values like `1` or `'value'` are called _literals_. 

In objects, properties are wires with names as well:
`let sherlock = { surname: 'Holmes', address: {city: 'London'} };`  
mental model: sherlock_wire link with an object {}, and {} has two properties: surname and address, surname pointing to the string 'Holmes' and address pointing to another object {}, this {} has a property city pointing to the string 'London'



## Primitive Values:
These values can be changed, i.e. no functions can directly change these values. They are stored in memory in their own boxes. Variables are wires linking the actual values in memory boxes and the place where it needs to be used.
* Booleans(true, false) - logical operations
* Numbers(int and float) - math calculations
    * There's only ONE value for each distinct number, i.e. so only one 7 in a JS universe(memory)
    * `NaN` => `Not a Number` is also a number for representing the value is supposed to be a number but isn't, like `0 / 0`, furthur calculation with `NaN` will give you `NaN` as well
    * `-0`, `infinite`, `-infinite` exist
* Strings("anything quoted") - texts
* Undefined(undefined) - *unintentionally* missing values
    * maybe you forget to assign a value to a variable, falling back (JS point the variable directly) to this 'undefined' value
    * No, it is not a string, it is one of its kind
    * perhaps there's an error in your code, which you didn't expect - unintentionally
* Null(null) - *intentionally* missing values
    * You know this is a 'null' value - a valid missing data - intentionally assigned
    * it is also one of its kind
    * 
* Symbols(uncommon) - to hide implementation details
* BigInts(uncommon and new) - math on big numbers

## Objects and Functions
* Objects ({} and others) - to group related data and code
    * mutable
    * can't *destroy* an object you created
    * arrays (typeof([]))
    * dates (typeof(new Date()))
    * RegEx (typeof(/\d+/))
    * class (typeof(Math))
    * other non-primitive values
* Functions (x => x*2 and others) - to refer to code
    * when variables passed into functions, variables, the wires themselves are not passed in, the values they are pointing to do

## EQUALITY in JS
* 'Same Value Equality' -- `Object.is(a, b)`: see if a and b point to the same value, returns `true` if they do
* 'Strict Equality' -- `a === b` most often used, however, with special cases - something to memorize:
    * ___`NaN` === `NaN` is ALWAYS `false`___, whereas `Object.is(NaN, NaN)` is ALWAYS `true` >>> ***`(NaN !== NaN) must be true. Since NaN is the only value that’s not equal to itself, size !== size can only mean that size is NaN.`*** <<< it was designed like this, even designed before JS existed... so...
    * `-0 === 0` and `0 === -0` are ALWAYS `true`, whereas `Object.is(0, -0)` is ALWAYS `false`
* `Loose Equality`: not recommended except:
    ```js
    if (x == null) {
    // ...
    }
    // This code is equivalent to writing:
    if (x === null || x === undefined) {
    // ...
    }
    ```
    but still debatable...

### Difference between `Object.is()` and `===`:
Mostly they are the same, but rare cases like this tell the difference:
```js
let width = 0 / 0 // NaN
let height = width * 2 // NaN
console.log(width === height) // false
console.log(Object.is(width, height)) // true
```
Because `Objects.is(a, b)` evaluates wether a and b point to the same value box in memory, meanwhile, `NaN === NaN` is always `false`

### How to check if a value is/isn't `NaN`
* `Number.isNaN(a)`
* `Object.is(a, NaN)`
* `a !== a`

They will all return `true` if `a` is `NaN`

