# Making an HTTP POST request

## Summary

- Fetch API provides a simpler and more flexible way to make HTTP requests compared to `XMLHttpRequest` object.
- Use `fetch()` method to make an asynchronous web request to a URL.
- The `fetch()` returns a `Promise` that resolves into a `Response` object.
- Use the `status` or `ok` property of the `Response` object to check whether the request was succesful.
- Use the `json()` method of the `Response` object to parse the contents into JSON data.
- Use the `try...catch` statement to handle errors when making HTTP requests.

How to make an HTTP POST request to the following API endpoint that creates a new blog post with the `title`, `body`, and `userId`:

```js
<code>https://jsonplaceholder.typicode.com/posts</code>
```

1. create a new directory to store project files
2. create a new `index.html` file in the project directory

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Fetch API Demo - HTTP POST</title>
    <meta name="robots" content="noindex" />
    <script src="js/app.js" defer></script>
  </head>
  <body></body>
</html>
```

The `index.html` file includes the `js/app.js` file in its header.

3. create a new `js/app.js` file with the following code:

```js
async function create(blogPost) {
  try {
    // Create the URL
    const url = "https://jsonplaceholder.typicode.com/posts";

    // Create the headers
    const headers = {
      "Content-Type": "application/json",
    };

    // Create the POST body
    const body = JSON.stringify({
      title: blogPost.title,
      body: blogPost.body,
      userId: blogPost.userId,
    });

    // Send the POST request
    const response = await fetch(url, { method: "POST", headers, body });

    // Check the response status
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    // Parse the JSON response
    const data = await response.json();
    console.log("Success:", data);
  } catch (error) {
    // Handle any errors
    console.error("Error:", error);
  }
}

create({
  title: "Test Post",
  body: "This is a test post",
  userId: 1,
});
```

### How it works

1. define a function that creates a new blog post that accepts a post object:

```js
async function create(post)
```

2. construct a URL endpoint where the POST request will be sent:

```js
const url = "https://jsonplaceholder.typicode.com/posts";
```

3. create an HTTP header to be sent with the request:

```js
const headers = {
  "Content-Type": "application/json",
};
```

Set the `"Content-Type"` header to `"application/json"` to instruct that the request's body is in JSON format.

4. create a body of the POST request by serializing the post object into a JSON string using `JSON.stringify()` method:

```js
const body = JSON.stringify({
  title: post.title,
  body: post.body,
  userId: post.userId,
});
```

this assumes that the `blogPost` object has a `title`,`body`, and `userId` properties.

5. send the POST request using the `fetch()` method:

```js
const response = await fetch(url, {
  method: "POST",
  headers,
  body,
});
```

In this syntax:

- `url`: the API endpoint where you send the POST request
- `method: 'POST'`: specifies that this is an HTTP POST request
- `headers`: includes the header in the request
- `body`: contains the JSON string included in the body of the HTTP POST request

The `fetch()` method returns a `Response` object.

6. check the response status and throw an error if the request was not succesful:

```js
if (!response.ok) {
  throw new Error(`HTTP error! Status: ${response.status}`);
}
```

7. parse the JSON response and log the data into the console window:

```js
const data = await response.json();
console.log("Success:", data);
```

8. catch any error that occurs during the request and log it to the console:

```js
catch (error) {
  console.log('Error:', error);
}
```

9. call the `create()` function with a blog post object containing the `title`, `body`, and `userId` properties:

```js
create({
  title: "Test Post",
  body: "This is a test post",
  userId: 1,
});
```

Step 4. Open the index.html in the web browser. It'll execute the app.js file that makes an HTTP POST request.

Step 5. Open the console window, you'll see the following message if the request was succesful:
![console log window](https://www.javascripttutorial.net/wp-content/uploads/2024/08/javascript-fetch-api-post-method.png)
