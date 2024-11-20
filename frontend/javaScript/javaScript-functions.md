# JavaScript Functions

## Summary

- Use the function keyword to declare a function.
- Use the functionName() to call a function.
- All functions implicitly return undefined if they don’t explicitly return a value.
- Use the return statement to return a value from a function explicitly.
- The arguments variable is an array-like object inside a function, representing function arguments.
- The function hoisting allows you to call a function before declaring it.

## Introduction to JavaScript functions

When developing an application, you often need to perform the same action in many places.
Example: show a message whenever an error occurs

To avoid repeating the same code all over placese, you can use a function to wrap that code and reuse it.

## Declaring a function

To declare a function, you use the `function` keyword, followed by the function name, list of parameters, and the function body:

```js
function functionName(parameters) {
  // function body
  // ...
}
```

function name must be a valid JavaScript identifier.

- by convention, the function names are in `camelCase` and start with verbs like `getData()`, `fetchContents()`, and `isValid()`.

- a function can accept zero, one, or multiple parameters.
- in the case of multiple parameters, you need to use a comma to seperate two parameters.

## Calling a function

To use a function, you need to call it.
Calling a function is AKA invoking a function

To call a function, you use its name followed by arguments enclosed in parentheses like this:

```js
functionName(arguments);
```

### Parameters vs Arguments

The terms are often used interchangeably. However, they are essentially different.

- When declaring a function, you specify the parameters
- when calling a function, you pass the arguments corresponding to the parameters

## Returning a value

Every function in JS implicitly returns `undefined` unless you explicitly specify a return value.
Example:

```js
function say(message) {
  console.log(message);
}

let result = say("Hello");
console.log("Result:", result);
```

Output:

```console
Hello
Result: undefined
```

To specify a return value for a function, you use the `return` statement followed by an expression or a value

```js
return expression;
```

## The arguments object

Inside a function, you can access an object called `arguments` that represents the named arguments of the function

The `arguments` object behaves like an array though it is not an instance of the Array type.

Example, you can use the square bracket `[]` to access the arguments: `arguments[0]` returns the first argument, `arguments[1]` returns the second one and so on.

You can also use the `length` property of the `arguments` object to determine the number of arguments.

Example implementing a generic `add()` function that calculates the sum of any number of arguments.

```js
function add() {
  let sum = 0;
  for (let i = 0; i < arguments.length; i++) {
    sum += arguments[i];
  }
  return sum;
}
```

## Function hoisting

In JS, you can use a function before declaring it.

Example:

```js
showMe(); // a hoisting example

function showMe() {
  console.log("an hoisting example");
}
```

This feature is called hoisting

Function hoisting is a mechanism in which the JS engine physically moves function declarations to the top of the code before executing them

example the version of the code before the JS engine executes it:

```js
function showMe() {
  console.log("a hoisting example");
}

showMe(); // a hoisting example
```

Function hoisting allows you to call a function before declaring it, making the development workflow more smoother.
