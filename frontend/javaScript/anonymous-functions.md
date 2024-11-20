# JavaScript Anonymous Functions

## Summary

- Anonymous functions are functions without names
- Anonymous functions can be arguments of other functions or as an immediately invoked function execution.

## Introduction to JS anonymous functions

An anonymous function is a function without a name. The following shows how to define an anonymous function:

```js
(function () {
  //...
});
```

Note that if you don't place the anonymous function inside the parentheses `()`, you'll get a syntax error. The parentheses `()` make the anonymous function an expression that returns a function object.

An anonymous function is not accessible after its initial creation. Therefore, you often need to assign it to a variable.

```js
let show = function () {
  console.log("Anonymous function");
};

show();
```

In this example, the anonymous function has no name between the `funcion` keyword and parentheses `()`.

Because we need to call the anonymous function later, we assign the anonymous function to the `show` variable.

Since the whole assignment of the anonymous function to the show variable makes a valid expression, you don't need to wrap the anonymous function inside the parentheses `()`.

## Using anonymous functions as arguments

In practice, you often pass anonymous functions as arguments to other functions.

Example:

```js
setTimeout(function () {
  console.log("Execute later after 1 second");
}, 1000);
```

We pass an anonymous function in to the `setTimeout()` function. The `setTimeout()` function executes this anonymous function one second later.

> Note that functions are first-class citizens in JavaScript. Therefore, you can pass a function to another function as an argument.

## Immediately invoked function execution (IIFE)

If you want to create a function and executre it immediately after the declaration, you can declare an anonymous function like this:

```js
(function () {
  console.log("IIFE");
})();
```

Output:

```js
IIFE;
```

## Arrow functions

ES6 introduced arrow function expressions that provide a shorthand for declaring anonymous functions.

Example:

```js
let show = function () {
  console.log("Anonymous function");
};
```

...can be shortened using the following arrow function:

```js
let show = () => console.log("Anonymous function");
```

Similarly, the following anonymous function:

```js
let add = function (a, b) {
  return a + b;
};
```

...is functionally equivalent to the following arrow function:

```js
let add = (a, b) => a + b;
```
