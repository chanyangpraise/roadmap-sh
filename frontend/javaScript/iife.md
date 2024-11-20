# JavaScript Immediately Invoked Function Expression

## TL;DR

A JavaScript immediately invoked function expression is a function defined as an expression and executed immediately after creation. The following shows the syntax of defining an IIFE

```js
(function () {
  // ...
})();
```

## Why IIFEs

When you define a function, the JavaScript engine adds the function to the global object.

Example:

```js
function add(a, b) {
  return a + b;
}
```

In web browsers, the JS engine adds the `add()` function to the `window` global object:

```js
console.log(window.add);
```

Likewise, if you declare a variable outside of a function using the `var` keyword, the JS engine also adds the variable to the global object:

```js
var counter = 10;
console.log(window.counter); // 10
```

If you have many global variables and functions, the JS engine will only release the memory allocated for them until the global object loses its scopes.

As a result, the script may use the memory inefficiently. On top of that, having global variables and functions will likely cause name collisions.

One way to prevent the functions and variables from polluting the global object is to use IIFEs.

In JavaScript, you can have the following expressions:

```js
"This is a string";
10 + 20;
```

This syntax is correct even though the expressions have no effect. A function can also be declared as an expression which is called a function expression.

```js
let sum = function(a, b) {
  return a + b:
}
```

In this syntax, the part on the right side of the assignment operator (`=`) is a function expression. Since a function is an expression, you can wrap it inside parentheses:

```js
let sum (function(a,b) {
  return a + b;
})
```

In this example, the `sum` variable is referenced as the anonymous function that adds two arguments

In addition, you can execute the function immediately after creating it

```js
let sum = (function (a, b) {
  return a + b;
})(10, 20);

console.log(sum);
```

In this example, the `sum` variable holds the result of the function call.

The following expression is called an IIFE because the function is created as an expression and executed immediately

```js
(function (a, b) {
  return a + b;
})(10, 20);
```

General syntax for defining an IIFE:

```js
(function () {
  //...
})();
```

You can use an arrow function to define an IIFE:

```js
(() => {
  //...
})();
```

By placing functions and variables inside an IIFE, you can avoid polluting them to the global object.

```js
(function () {
  const counter = 0;

  function add(a, b) {
    return a + b;
  }

  console.log(add(10, 20)); // 30
})();
```

## Named IIFE

An IIFE can have a name. However, it cannot be invoked again after execution:

```js
(function namedIIFE() {
  //...
})();
```

## IIFE starting with a semicolon (;)

Sometimes, you may see an IIFE that starts with a semicolon(;);

```js
;(function () {
  /* */
})();
```

In this syntax, the semicolon is used to terminate the statement in case two or more JavaScript files are blindly concatenated into a single file.

Example, you may have two file `lib1.js` and `lib2.js` which use IIFEs:

```js
(function () {
  //...
})()(function () {
  //...
})();
```

IF you use a code bundler tool to concatenate code from both files into a single file, without the semicolon (`;`) the concatenated JS code will cause a syntax error.
