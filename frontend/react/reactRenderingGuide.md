# What is "Rendering"?

**Rendering** is the process of React asking your components to describe what they want their section of the UI to look like, now, based on the current combination of props and state.

## Rendering Process Overview

During the rendering process, React will start at the root of the component tree and loop downwards to find all components that have been flagged as needing updates.
For each flagged component, React will call either `FunctionComponent(props)` (for function components), or `classComponentInstance.render()` (for class components), and save the render output for the next steps of the render pass.

A component's render output is normally written in JSX syntax, which is then converted to `React.createElement()` calls as the JS is complied and prepared for deployment.

`createElement` returns React _elements_, which are plain JS objects that describe the intended structure of the UI.

Example:

```jsx
// This JSX syntax:
return <MyComponent a={42} b="testing">Text here</MyComponent>

// is converted to this call:
return React.createElement(MyComponent, {a: 42, b: "testing"}, "Text Here")

// and that becomes this element object:
{type: MyComponent, props: {a: 42, b: "testing"}, children: ["Text Here"]}

// And internally, React calls the actual function to render it:
let elements = MyComponent({...props, children})

// For "host components" like HTML:
return <button onClick={() => {}}>Click Me</button>
// becomes
React.createElement("button", {onClick}, "Click Me")
// and finally:
{type: "button", props: {onClick}, children: ["Click me"]}
```

After it has collected the render output from the entire component tree, React will diff the new tree of objects (frequently referred to as the "virtual DOM"), and collects a list of all the changes that need to be applied to make the real DOM look like the current desired output.

The diffing and calculation process is known ans "reconcilliation"

React then applies all the calculated changes to the DOM in one synchronous sequence.

## Render and Commit Phases

The React team divides this work into two phases, conceptually:

- the "Render phase" contains all the work of rendering components and calculating changes
- the "Commit phase" is the process of applying those changes to the DOM

After React has updated the DOM in the commit phase, it updates all refs accordingly to point to the requested DOM nodes and component instances. It then synchronously runs the `componentDidMount` and `componentDidUpdate` class lifecycle methods, and the `useLayoutEffect` hooks.

React then sets a short timeout, and when it expires, runs all the `useEffect` hooks.
This step is also known as the "Passive Effects" phase.

React 18 added "Concurrent Rendering" features like `useTransition`.
This gives React the ability to pause the work in the rendering phase to allow the browser to process events.
React will either resume, throw away, or recalculate that work later as appropriate.
Once the render pass has been completed, React will still run the commit phase synchronously in one step.

A key part of this is to understand that _"rendering" is not the same thing as "updating the DOM"_, and a component may be rendered without any visible changes happening as a result.

When React renders a component:

- the component might return the same render output as last time, so no changes are needed
- in Concurrent Rendering, React might end up rendering a component multiple times, but throw away the render output each time if other updates invalidate the current work being done

React hooks timeline diagram below helps illustrate the sequencing of rendering, commiting, and executing hooks:

![React hooks timeline diagram](https://blog.isquaredsoftware.com/images/2020-05-react-rendering-behavior/react-hook-timeline-diagram.png)

## How Does React Handle Renders?

### Queuing Renders

After the initial render has completed, there are a few different ways to tell React to queue a re-render:

1. Function components:

-`useState` setters

- `useReducer` dispatches

2. Class components:

- `this.setState()`
- `this.forceUpdate()`

3. Other:

- Calling the ReactDOM top-level `render(<App>)` method again (which is equivalent to calling `forceUpdate()` on the root component)
- Updates triggered from the new `useSyncExternalStore` hook

> note that function components don't have a `forceUpdate` method, but you can get the same behavior by using a `useReducer` hook that always incremenets a counter:

```jsx
const [, forceRender] = useReducer((c) => c + 1, 0);
```

### Standard Render Behavior

It's very important to remember that:
React's default behavior is that when a parent component renders, React will **recursively render _all_ child components inside of it!**

As an example, say we have a component tree of `A > B > C > D`, and we've already shown them on the page.

The user clicks a button in `B` that increments a counter:

- we call `setState()` in `B`, which queues a re-render of B
- React starts the render pass from the top of the tree
- React sees that `A` is not marked as needing an update, and moves past it
- React sees that `B` is marked as needing an update, and renders it. `B` returns `<C/>` as it did last time
- `C` was _not_ originally marked as needing an update. However, because its parent `B` rendered, React now moves downwards and renders `C` as well. C returns `<D/>` again
- `D` was also not marked for rendering, but since its parent `C` rendered, REact moves downward and renders `D` too

To repeat this another way:

Rendering a component will, by default, cause _all_ components inside of it to be rendered too!

Also, another key point:

In normal rendering, React does _not_ care whether "props changed" - it will render child components unconditionally just because the parent rendered!

This means that calling `setState()` in your root `<App>` component, with no other changes altering the behavior, _will_ cause React to re-render every single component in the component tree.

Now, it's very likely that most of the components in the tree will return the exact same render output as last time, and therefore React won't need to make any changes to the DOM. _But_, React will still have to do the work of asking components to render themselves and diffing the render output. Both of those take time and effort.

Remember, rendering is not a _bad_ thing - it's how React knows whether it needs to actually make any changes to the DOM!

## Rules of React Rendering

One of the primary rules of React rendering is that rendering must be "pure" and not have any side effects!

This can be tricky and confusing, because many side effects are not obvious, and don't result in anything breaking.

For example, strictly speaking a `console.log()` statement is a side effect, but it won't actually break anything.

Mutating a prop is defintely a side effet, and it _might_ not break anything.

Making an AJAX call in the middle of rendering is also defintely a side effect, and can definitely cause unexpected app behavior depending on the type of request.

Key points from Sebastian Markbage's _The Rules of React_

He defines the expected behaviors for different React lifecycle methods, including `render`, and what kinds of operations would be considered safely "pure" and which would be unsafe.

Key points:

1. Render logic _must not_:

- can't mutate existing variables and objects
- can't create random values like `Math.random()` or `Date.now()`
- can't make network requests
- can't queue state updates

2. Render logic _may_:

- mutate objects that were newly created while rendering
- throw errors
- "lazy initialize" data that hasn't been created yet, such as a cached value

## Component Metadata and Fibers

React stores an internal data structure that tracks all the current component instances that exist in the application.
The core piece of this data structure is an object called a "fiber", which contains metadata that describe:

- what component type is supposed to be rendered at this point in the component tree
- the current props and state associated with this component
- pointers to parent, sibling, and child components
- other internal metadata that React uses to track the rendering process

the phrase "React Fiber" used to describe a React version or feature, is really referring to the rewrite of React's internals that switched the rendering logic to rely on these "Fiber" objects as the key data structure. That was released as React 16.0, so every version of React since then has used this approach.
