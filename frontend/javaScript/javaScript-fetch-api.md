# Introduction to JavaScript Fetch API

The fetch API is a powerful and modern tool that simplifies making HTTP requests directly from browsers.

- Fetch API leverages [[JavaScript Promise]], providing a cleaner and more flexible way to interact with servers.
- It helps handle asynchronous requests and responses more intuitively

`fetch()` is a method of the [[JavaScript Global Window Object]], which allows you to send an HTTP request to a URL with a single command.

Whether you're retrieving data, submitting a form, or interacting with APIs, Fetch API helps streamline the entire process, making your code more readable.

## Sending a request

`fetch()` requires only one parameter which is the URL of the resource that you want to fetch:

```js
fetch(url);
```

The `fetch()` method returns a `Promise` so you can use the `then()` and `catch()` methods to handle it:

```js
fetch(url)
  .then((response) => {
    // handle the response
  })
  .catch((error) => {
    // handle the error
  });
```

Once the request is completed, the resource becomes available, and the `Promise` resolves into a `Response` object.

The `Response` object serves as an API wrapper around the fetched resource.

## Reading the response

If the response contains JSON data, you can use the `json()` method of the `Response` object to parse it.

The `json()` method returns a `Promise` that resolves with the full contents of the fetched resource, allowing to access the JSON data:

```js
fetch(url)
  .then((response) => response.json())
  .then((data) => console.log(data));
```

In practice, you often use the `async`/`await` with the `fetch()` method to make the code more obvious:

```js
const response = await fetch(url);
const data = await response.json();
console.log(data); // json data
```

Besides the `json()` method, the `Response` object has other methods such as `text()`,`blob()`,`formData()`, and `arrayBuffer()` for handling the respective data types.

## Handling HTTP status code

The `Response` object has a `status` property that gives you the HTTP status code of the response:

```js
response.status;
```

The HTTP status code allows you to determine whether the request was succesful or not:

```js
const response = await fetch(url);
if (response.status === 200) {
  // success
}
```

In practice, you'll use a convenient property `ok` that checks if the status code is in the range of 200-299. If it is `false`, the request was not succesful:

```js
const response = await fetch(url);
if (!response.ok) {
  throw new Error(`HTTP error! Status: ${response.status}`);
}
```

## JavaScript Fetch API example

We'll make a GET request to the following API endpoint that returns a list of users:

```js
https://jsonplaceholder.typicode.com/users
```

1. Create a new directory such as fetch to store the project files.
2. Create an `index.html` file within the projet directory:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Fetch API Demo</title>
    <script src="js/app.js" defer></script>
  </head>

  <body>
    <div id="root">
      <div id="content"></div>
      <p id="message"></p>
      <p id="loading"></p>
    </div>
  </body>
</html>
```

The HTML file has the root element that contains three elements:

- `#content` for rendering the user list.
- `#message` for showing any error message.
- `#loading` for displaying a loading message.

3. Create an `app.js` inside the `js` directory:

```js
const getUsers = async () => {
  const url = "https://jsonplaceholder.typicode.com/users";
  const response = await fetch(url);
  return await response.json();
};

const render = (users) => {
  return users.map(({ name, email }) => `<li>${name} (${email})</li>`).join("");
};

(async () => {
  const users = await getUsers();
  document.querySelector("#content").innerHTML = `<ul>${render(users)}</ul>`;
})();
```

### How it works

1. Define a function `getUsers` that uses the `fetch()` method to fetch data from the API endpoint: <https://jsonplaceholder.typicode.com/users>

```js
const getUsers = async () => {
  const url = "https://jsonplaceholder.typicode.com/users";
  const response = await fetch(url);
  return await response.json();
};
```

2. Create a new function `render` that returns an HTML snippet for showing a list of users on the screen.

```js
const render = (users) => {
  return users.map(({ name, email }) => `<li>${name} (${email})</li>`).join("");
};
```

3. Define an Immediately Invoked Function (IIFE) that calls the `getUsers()` function and displays the user list on the screen:

```js
(async () => {
  const users = await getUsers();
  document.querySelector("#content").innerHTML = `<ul>${render(users)}</ul>`;
})();
```

## Handling errors

When making a web request, various erros can occur:

- network outages
- server downtime
- other connection issues

To handle the errors, you can use the `try...catch` statemenet:

```js
(async () => {
  try {
    // fetch the users
    const users = await getUsers();

    // show the user list
    document.querySelector("#content").innerHTML = `<ul>${render(users)}</ul>`;
  } catch (err) {
    // show the error message
    document.querySelector("#message").textContent = err.message;
  }
})();
```

In this example, if an error occurs, the `catch` block will execute, logging the error message in the console and displaying a user-friendly error message to the user.

## Displaying a loading indicator

If the network is slow, you will see a blank page for a brief period, which leads to a poor user experience.

To enhance this, you can display a loading message while the data is being fetched.

To do so, you can modify the IIFE function as follows:

```js
(async () => {
  // show the loading element
  const loadingElem = document.querySelector("#loading");
  loadingElem.innerHTML = "Loading...";
  try {
    // fetch the users
    const users = await getUsers();

    // show the user list
    document.querySelector("#content").innerHTML = `<ul>${render(users)}</ul>`;
  } catch (err) {
    // show the error message
    document.querySelector("#message").textContent = err.message;
  } finally {
    loadingElem.innerHTML = "";
  }
})();
```

Before making a request, we set the loading message to `'Loading...'`:

```js
const loadingElem = document.querySelector("#loading");
loadingElem.innerHTML = "Loading...";
```

After the request is completed, whether succesful or not, you clear the loading message by setting it to blank in the `finally` block:

```js
// ...
finally {
    loadingElem.innerHTML = '';
}
```

Keep in mind that if the network speed is fast enough, the loading message might not be visible. To test the loading message, you can simulate a network delay as follows:

```js
(async () => {
  // show the loading element
  const loadingElem = document.querySelector("#loading");
  loadingElem.innerHTML = "Loading...";

  // simulate network delay
  const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));
  await delay(2000); // delay 2 seconds

  try {
    // fetch the users
    const users = await getUsers();

    // show the user list
    document.querySelector("#content").innerHTML = `<ul>${render(users)}</ul>`;
  } catch (err) {
    // show the error message
    document.querySelector("#message").textContent = err.message;
  } finally {
    loadingElem.innerHTML = "";
  }
})();
```

In this code, we include a following snippet to introduce a 2-second delay before making the request:

```js
// simulate network delay
const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));
await delay(2000); // delay 2 seconds
```
