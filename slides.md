---
fonts:
  mono: monospace
---

# JavaScript Common Mistakes

---

<img style="margin: auto;height: 100%;" src="https://i.redd.it/0l0eovogweq91.jpg"/>

---

# Incorrect References to `this`

Wow! I hate `this` more!


````md magic-move

```javascript
const myObject = {
  value: 'My Object Value',
  myMethod: function() {
    setTimeout(function() {
      console.log(this.value); // Incorrect: this refers to window or undefined
    }, 100);
  }
};

myObject.myMethod(); // Output: undefined (or error if strict mode) or empty string in a browser.
```


```javascript
const myObject = {
  value: 'My Object Value',
  myMethod: function() {
    setTimeout(() => {
      console.log(this.value); // Correct: arrow function keeps this context.
    }, 100);
  }
};

myObject.myMethod();  // Output: "My Object Value"
```

```javascript
const myObject = {
  value: 'My Object Value',
  myMethod: function() {
    const self = this; // Capture this
    setTimeout(function() {
      console.log(self.value); // Correct
    }, 100);
  }
};
myObject.myMethod();  // Output: "My Object Value"
```

````

---

# Incorrect References to `this`

**Explanation**

Inside a regular function, `this` often refers to the global object (window in browsers) or is `undefined` in strict mode. In methods of an object, it refers to the object itself. However, in callbacks and closures, `this` can easily become lost or refer to the wrong context.

**Best Practices**

* Use arrow functions (`=>`) when you need to preserve the lexical `this` (the `this` of the enclosing scope).
* Use `.bind(this)` to explicitly set the context of a function.
* Capture `this` in a variable (e.g., `const self = this;`) and use that within a callback if you need to use the `function` keyword.
* Do not use `this`. Prefer functional programming™.

---

# Variable Scope Confusion

`var` vs `let` vs `const`


````md magic-move

```javascript
function myFunction() {
  console.log(x); // Output: undefined (due to hoisting)
  if (true) {
    var x = 10;
  }
  console.log(x); // Output: 10
}

myFunction();
```


```javascript
function myFunction() {
  //console.log(x); // Output: ReferenceError
  if (true) {
    let x = 10;
    console.log(x) // Output: 10
  }
  //console.log(x); // Output: ReferenceError
}
myFunction();
```

```javascript
function myFunction() {
    const x = 10;
    console.log(x); // Output: 10
  }
myFunction();
```

````

---

# Variable Scope Confusion

**Explanation**

`var` declarations are function-scoped, meaning they're visible throughout the function, even before their actual declaration within the code, which is known as hoisting. `let` and `const` are block-scoped (scoped to a block of code enclosed in curly braces `{}`), and aren't accessible before their declaration, which avoids a lot of confusion.

**Best Practices**

*   Always prefer `let` and `const` over `var`.
*   Use `const` by default for variables that don't need to be reassigned. Use `let` for variables that may need to be reassigned.
*   Declare variables as close to where they are used to avoid confusion and to make the code easier to reason about.

---
layout: image-right
image: 'https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fjaeke4ce8b5czn0yf65.png'
---

# Type Coercion

Wanna Crush your JS knowledge?

````md magic-move

```javascript
// string '1' is converted to number 1
console.log(1 == '1'); // Output: true
console.log(0 == false); // Output: true
console.log('' == false); // Output: true
console.log([] == false); // Output: true
console.log(null == undefined); // Output: true
```


```javascript
// Use strict equality to avoid type coercion
console.log(1 === '1'); // Output: false
console.log(0 === false); // Output: false
console.log('' === false); // Output: false
console.log([] === false); // Output: false
console.log(null === undefined); // Output: false
```

````

---

# Type Coercion

**Explanation:** JavaScript will automatically convert data types in certain operations. This can lead to unexpected behavior, especially in comparisons.

**Best Practices:**
* Use strict equality `===` and strict inequality `!==` to compare values. This prevents type coercion.
* Be aware of how type coercion happens.
* Play https://js-crush.vercel.app/


---

# Undefined vs Null

A tale of two _nothings_: It was the best of PL, it was the worst of PL.


````md magic-move
```javascript
let myVar;
console.log(myVar == null); // Output: true
console.log(myVar === null); // Output: false
console.log(typeof myVar) // Output: undefined

const myObj = {};
console.log(myObj.prop == undefined) // Output: true
console.log(myObj.prop === undefined) // Output: true

```

```javascript
let myVar;
console.log(myVar === undefined); // Output: true

let myOtherVar = null;
console.log(myOtherVar === null);  // Output: true
console.log(myOtherVar === undefined); // Output: false
console.log(typeof myOtherVar) // Output: object
```
````
---

# Undefined vs Null

**Explanation:** `undefined` means a variable has been declared but has not yet been assigned a value or a property does not exist on an object. `null` is an assignment value representing no value or no object. Although `null == undefined` is true due to type coercion, they are not identical (`null === undefined` is false).

**Best Practices:**
*   Use `=== undefined` to check if a variable has been declared but not assigned.
*   Use `=== null` to check if a variable has been specifically set to no value.
*   Be clear about the meaning of both and use the correct check based on the scenario.

---

# Incorrect Use of `parseInt`

`parseInt` can bite you in a `map`.

````md magic-move

```javascript
console.log(parseInt('010'));  // Output: 10 or 8 (depending on the environment/browser).
console.log(parseInt('0x10')); // Output: 16 (it interprets hexadecimal)
console.log(parseInt('0b10', 2)); // Output: 2 (it interprets binary)
array.map(parseInt); // Incorrect: parseInt takes two arguments, the second one is the radix.
```


```javascript
console.log(parseInt('010', 10));  // Output: 10
console.log(parseInt('0x10', 16));  // Output: 16
console.log(parseInt('0b10', 2)); // Output: 2
array.map(x => parseInt(x, 10)); // Correct
```
````

---

# Incorrect Use of `parseInt`

**Explanation**

`parseInt` can produce unexpected results if you don't specify the radix (base).  If the input string starts with "0x" it will assume it is hexadecimal, which could cause unintended consequences.

**Best Practices**
*   Always specify the radix when using `parseInt`. Use radix 10 for decimal numbers.
* Do not write `arr.map(parseInt)`.

---

# NaN Comparisons

Did you remember SQL's `NULL`?

````md magic-move
```javascript
console.log(NaN == NaN); // Output: false
console.log(NaN === NaN); // Output: false

let result = Math.sqrt(-1); // NaN
console.log(result == NaN); // Output: false
```


```javascript
console.log(isNaN(NaN)); // Output: true
console.log(Number.isNaN(NaN)); // Output: true

let result = Math.sqrt(-1); // NaN
console.log(Number.isNaN(result)); // Output: true
```
````

---

# NaN Comparisons

**Explanation**

`NaN` (Not-a-Number) is a special value representing the result of invalid mathematical operations.  `NaN` is never equal to itself or any other value.

**Best Practices**

* Remember that `NaN` is a `number`
*   Use `isNaN()` or `Number.isNaN()` to check if a value is `NaN`. Prefer `Number.isNaN` if you have to check the actual type of `NaN`
*   `isNaN()` will try to convert the value to a number first. `Number.isNaN()` doesn't do type conversion, and is safer.

---

# Incorrect Use of `for...in`

`for...in` vs. `for...of`


````md magic-move

```javascript
const myArray = ['a', 'b', 'c'];
for (let index in myArray) {
  console.log(index); // Output: '0', '1', '2', 'myCustomProperty' (if added to Array.prototype)
}
Array.prototype.myCustomProperty = function () {};
 for (let index in myArray) {
  console.log(index); // Output: '0', '1', '2', 'myCustomProperty'
}
```

```javascript
const myArray = ['a', 'b', 'c'];
for (const element of myArray) {
  console.log(element);  // Output: 'a', 'b', 'c'
}

const myArray2 = ['a', 'b', 'c'];
for (let i=0; i<myArray2.length; i++){
    console.log(myArray2[i]) // Output: 'a', 'b', 'c'
}

const myObject = { a: 1, b: 2, c: 3 };
for (const key of Object.keys(myObject)) {
  console.log(key, myObject[key]); // Output: 'a' 1, 'b' 2, 'c' 3
}

for(const key in myObject){
    console.log(key, myObject[key]) // Output: 'a' 1, 'b' 2, 'c' 3
}
```
````

---

# Incorrect Use of `for...in`

**Explanation**

The `for...in` loop iterates over all enumerable properties of an object, including inherited properties on its prototype chain. This can lead to unintended behavior when you expect it to only iterate over the properties of an object you're using, or when trying to iterate over an array

**Best Practices**
*   Use `for...of` for iterating over arrays or any iterable objects.
*   Use `Object.keys` to get an array of an objects properties, if you need to use `for...of`
*   Use `for...in` only for iterating over the properties of objects when you need to get inherited properties as well.

---

# Using `with` Statement

`with` statement is BAD. Period.

````md magic-move

```javascript
const myObject = { value: 10 };
with (myObject) {
  console.log(value); // Output: 10
}
```

```javascript
const myObject = { value: 10 };
let value = 20;
with (myObject) {
  console.log(value); // Output: 10
}
console.log(value) // Output: 20
```

```javascript
 const myObject = { value: 10 };
 console.log(myObject.value); // Output: 10
```
````

---

# Using `with` Statement

**Explanation**
The `with` statement adds a given object to the scope chain while evaluating a block of code, which can cause confusion and lead to hard-to-debug code.

**Best Practices**
*   Avoid using the `with` statement. It is not recommended in modern JavaScript and considered bad practice.
*   Access object properties directly using dot notation `myObject.value`.

---

# Incorrect Use of `delete` Operator

You almost never need `delete`.

````md magic-move

```javascript
let myVar = 10;
delete myVar; // Doesn't delete the variable
console.log(myVar); // Output: 10

const myArray = [1,2,3]
delete myArray[1];
console.log(myArray); // Output: [1, empty, 3]
console.log(myArray.length); // Output: 3
```

```javascript
let myVar = 10;
myVar = undefined;
console.log(myVar); // Output: undefined

const myArray = [1,2,3]
myArray.splice(1,1);
console.log(myArray); // Output: [1, 3]
console.log(myArray.length); // Output: 2
```
````

---

# Incorrect Use of `delete` Operator

**Explanation**

The `delete` operator removes properties from an object. It does not remove variables or array elements. For array elements, it actually replaces them with undefined, resulting in a sparse array, and keeping the array length.

**Best Practices**
*   Use `obj.prop = undefined` or `{...newObj, prop} = obj` to remove object properties.
*   Set variables to `undefined` or reassign them to remove their value.
*   Use array methods like `splice()` to remove array elements.

---

# Incorrect Use of `typeof` Operator

`typeof` is weird.

````md magic-move

```javascript
console.log(typeof null);   // Output: 'object' (incorrect)
console.log(typeof []);     // Output: 'object' (incorrect for arrays)
```

```javascript
console.log(null === null); // Output: true
console.log(Array.isArray([])); // Output: true
console.log(typeof 'hello') // Output: string
console.log(typeof 10) // Output: number
console.log(typeof true) // Output: boolean
```

````

---

# Incorrect Use of `typeof` Operator

**Explanation**

The `typeof` operator has some quirks.  It will return "object" for `null` which is incorrect, and the `typeof` an array will also return "object" even though arrays are a special type of object.

**Best Practices**
*   Use `=== null` to check for null.
*   Use `Array.isArray()` to check for arrays
*   Use the `typeof` operator for primitive data type checking, such as number, string, boolean, or undefined.

---

# Thanks

The slides are based mostly on Google Gemini and ChatGPT.
