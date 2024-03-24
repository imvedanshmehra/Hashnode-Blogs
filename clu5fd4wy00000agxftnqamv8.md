---
title: "React 19"
datePublished: Sun Mar 24 2024 11:17:34 GMT+0000 (Coordinated Universal Time)
cuid: clu5fd4wy00000agxftnqamv8
slug: react-19
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1711273246493/40547cc8-0a3c-478e-81b0-0d951f6c93d3.png
tags: javascript, web-development, react-js, reactjs, react-19

---

So, you must have already heard that React is about to roll out their new version - React 19 🎉

Everyone is talking about how this upgrade can be a game changer for React's ecosystem, how React's own compiler will take over other Javascript UI libraries and frameworks, in this article we'll talk about all of these new features which is coming along with the latest update.

### What's coming in with React 19?

The most important thing that we'll get to see in React 19 is of course the *compiler* but there are few others improvements and additions which has been added to the latest version that includes an all new `use()` hook, *actions, use client & use server directives, document metadata* etc.

## The Compiler 💪

> *"*React can sometimes be *too* reactive: it can re-render too much.*"*

React team has been talking about this for a long time and finally they have announced that they'll be launching it soon in their new version upgrade.  
React compiler is probably going to change how we work with React forever, it solves one of the most critical and annoying problem that exists with React - which is manual memoization.

React compiler is now going to compile the React code into native Javascript code before executing which is going to make it 2X more faster.

Currently Instagram is powered by this compiler in production to test the ability and drawbacks of the compiler, they are gradually rolling it out to the other Meta projects and few open source project before releasing it for production use.

### Automatic memoization using the compiler

Manually memoizing react code using `useMemo`, `useCallback` and `memo` API can sometimes be very complicated to wrap one's head around, sometime's you don't know which code should be memoized and which one should not and it's very easy to go wrong with the memoization. Additionally, if you have worked with other frameworks like *Svelte* or *Vue* you might have noticed that these frameworks provides an out of the box memoization to the components which was one reason why react was fall behind despite being the most popular frontend framework out there.  
  
The React team was very well aware of this issue and this is what they aimed to fix using the all new compiler.

## use() hook

use() hook is currently available in the [React's Canary](https://react.dev/blog/2023/05/03/react-canaries) version, this new hook is going to break all the rules of React.

This hook will be used to read data from resources like `promises` and `contexts`.

```javascript
const value = use(resource);
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">This <code>resource</code> is the source of the data where you want to read the values from, it could be a Promise or a Context.</div>
</div>

One great thing about this hook is that it can be called inside of the functions and conditionals unlike the other traditional hooks in React which can only be called at the top level of the component.

In the below sandbox, I have shown how we can read values from a promise using this hook.

[https://codesandbox.io/p/devbox/new-wildflower-gj3dqy?file=%2Fpackage.json&embed=1](https://codesandbox.io/p/devbox/new-wildflower-gj3dqy?file=%2Fpackage.json&embed=1)

## use server & use client directives

Client and Server directives were already a thing in `NextJS` which is now natively supported by React itself.

Directives are a way to build fullstack react application by separating client and server code into their boundaries.

### use client

To mark a module as the client code, add `"use client"` at the top of the file. When a component marked with `"use client"` is imported by the server component, the compatible bundlers will treat the module import as a boundary between client and the server code.

```javascript
'use client';

const ClientComponent = () => {
  return (
    <>
      {/* Other component JSX... */}
    </>
  );
}
```

### use server

Add `"use server"` at the top of the async functions to mark the function callable by the client, such functions are called *Server Actions*.

Additionally, we can also add `"use server"` to the top of the file to mark the entire module as *Server Actions* which can then be imported in the client code.

```javascript
const someAsyncFn = async(data) => {
  'use server';
  // Other logic will go here...
}
```

## Actions

Actions can now be used in both server and client components, it allows us to pass functions to DOM elements like `</form>`.

```javascript
<form action={onSubmit}>
  <input name="email" />
  <button type="submit">Submit</button>
</form>
```

This feature was already available in `NextJS` but now it will be a stable feature of the React package itself.  
Actions can be operated synchronously or asynchronously and it'll make working with forms much more intuitive.

Additionally, there are two new hooks -`useFormStatus` and `useFormState` which are introduced to manage the lifecycle of the form submission. These hooks will allow us to access the form status and the current form state respectively.

## useOptimistic hook

The `useOptimistic` hook is yet another hook added to React which works well together with React actions. This hook allows us to optimistically update the UI while a background operation like network request is being performed.

```javascript
 const [optimisticState, addOptimistic] = useOptimistic(state, updateFn);
```

Using this hook can make the app feel more responsive, especially when working with forms. For example, when a users types a message into a input box and click on the submit button, instead of waiting for server to respond, the `useOptimistic` hook will directly show the user message along with a loading indicator, meanwhile the form will attempt to send the message to the actual server in the background and once the server confirms that the message is received, the loading indicator will be removed.

## Metadata

Another cool thing coming along the way are built-in support for the document metadata within the component tree which can help streamline the SEO and the other metadata concerns of the application.

With this cool feature we can now use `<title>`, `<link>`, `<meta>` and other meta elements within the component tree without the need of any third party packages like React Helmet.

```javascript
const App = () => {
  return (
    <>
      <title>React 19</title>
      <meta name="description" content="Welcome React 19" />
      // Other page content...
    </>
  );
}
```

## Conclusion

So these we some new features which are about to release in the latest production version of React.  
In conclusion, React 19 looks promising and is a significant step forward in making React more efficient and intuitive for the developer community.

With this, I hope you enjoyed reading this article as much as I enjoyed writing it and I hope that you get to learn something from it.

If you like the blog, please show your love by clicking on the ❤️ icon below and leave your thoughts in the comments.

If you'd like to read more such articles you can give me a follow. Also connect with me on [**Twitter**](https://twitter.com/imvedanshmehra) or [**LinkedIn**](https://www.linkedin.com/in/imvedanshmehra/) where I regularly share content related to web development, Javascript, & software engineering best practices.