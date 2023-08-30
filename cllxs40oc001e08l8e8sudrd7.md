---
title: "Auto-batching In React 18"
seoTitle: "What is Auto Batching in React?"
seoDescription: "Learn about auto batching in React 18 and how React uses it to provide improved performance."
datePublished: Wed Aug 30 2023 13:34:35 GMT+0000 (Coordinated Universal Time)
cuid: cllxs40oc001e08l8e8sudrd7
slug: auto-batching-in-react-18
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1693380724995/2f7eba55-eeb9-4575-aa94-bc59deb725ec.png
tags: javascript, performance, dom, reactjs, jsx

---

React 17 and prior versions of React use a technique called "[Batching](https://github.com/reactwg/react-18/discussions/21#:~:text=and%20library%20developers.-,What%20is%20batching%3F,-Batching%20is%20when)" to group multiple state updates into one to prevent the components from re-rendering multiple times on every state change. This is used for optimization purposes and especially for large-scale applications where unnecessary re-renders can be quite expensive sometimes.

The problem is that the older versions of React(17 and prior) support batching only for state updates that happen inside of browser events (like click of a button).

To improve this, React 18 and the newer versions provide an out-of-the-box optimization using Auto-batching which batches all the state updates together no matter if they are coming from a browser event, asynchronous function, timeouts, or interval.

### What is Batching?

To learn about batching in-depth we'll create a React application using React v17 or some older versions.

%[https://codesandbox.io/s/quiet-silence-c2rr47?file=/src/index.js] 

In the above sandbox, you might notice that even though the `handleClick` function updates two states namely `count1` and `count2` but still, every time we click on the button we see our console message only once which indicates that our component is only re-rendering once.

This is because as we have discussed previously in React 17 and prior, it automatically batches all the state updates into one as far as the state updates are happening inside of a browser event, and as you might already know `onClick` is a browser event and therefore our component is only re-rendering once even though two states are getting updated every time the button is clicked.

Well, that's not the case always, this time we'll consider a slightly more complex example where the state update will happen on the callback of a AJAX call using the `fetch` API.

%[https://codesandbox.io/s/xenodochial-villani-zr6t3d?file=/src/App.js] 

This time around, you'll notice that whenever the button is clicked the state update is happening but then we see that the console message is printed twice which indicates that our component is re-rendering two times whenever the button is clicked.

This is happening because AJAX calls are not part of the browser events and because of that React is not batching the state updates together.

### The Problem

If you are a seasoned React developer you might have already noticed the problem with this approach.

Multiple re-renders in a complex and large application can be quite expensive. Let's take an example where we have a nested components hierarchy in that case whenever the parent component re-renders all of its child components will be updated too and if we keep re-rendering the component on every state change we'll end up having too many unnecessary re-renders which can significantly affect the overall performance of the application.

### Automatic batching in React 18

To solve the above problem React auto batches all the state updates together starting from React 18, It provides out-of-box performance improvement by making sure that all the state updates are batched together no matter if they are coming from asynchronous calls, setTimeout, native event handlers, or any other event.

To understand this in more detail, let's take our previous example where we were updating the state within the fetch callback but this time we'll use React v18 or the newer versions to see the difference.

> We won't be using Codesandbox this time as due to some reason it doesn't batches the state properly in React 18.

Let's create a new React application using NextJs (or any other framework of choice).

```bash
npx create-next-app new-react-app
```

It should by default create a new React app with the latest version of React, and add the following code to it:

```javascript
// Only required for NextJs
"use client";

import { useState } from "react";

function App() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);

  const handleClick = () => {
    fetch("https://jsonplaceholder.typicode.com/todos/1").then(() => {
      setCount1((count) => count + 1);
      setCount2((count) => count - 1);
    });
  };

  console.log("Component rendered!");

  return (
    <>
      <button onClick={handleClick}>Click Me</button>
      <p>Count1: {count1}</p>
      <p>Count2: {count2}</p>
    </>
  );
}

export default App;
```

This time you'll notice that whenever we click the button a single console message is printed out which indicates that our component is re-rendering only once and both our states are batched together even when the state update is happening inside of a `fetch` callback.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1693390217132/eb8f021d-9a2b-47bf-a59f-7409640b475f.gif align="center")

You'll see a similar output if the state updates happen inside a `setTimeout` or any other event of that sort. Replace the `handleClick` as following and you'll see that the component is still re-rendering only once for multiple state updates.

```javascript
 const handleClick = () => {
    setTimeout(() => {
      setCount1((count) => count + 1);
      setCount2((count) => count - 1);
      // React will only re-render once.
    }, 1000);
  };
```

This is how auto-batching works in React, it will group multiple state updates into one without caring where the state update is happening to prevent multiple re-renders of the component.

### How to prevent Auto batching in React 18?

Starting from React 18 all the state updates will be batched together by default and if you want to prevent this for whatever reasons you can use `ReactDOM.flushSync` method which will stop auto batching in React 18.

Considering our previous example, if we want our component to re-render two times for both the state updates, we can do that as follows:

```javascript
"use client";

import { useState } from "react";
import { flushSync } from "react-dom";

function App() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);

  const handleClick = () => {
    fetch("https://jsonplaceholder.typicode.com/todos/1").then(() => {
      flushSync(() => {
        // Component will re-render
        setCount1((count) => count + 1);
      });
      flushSync(() => {
        // Component will re-render
        setCount2((count) => count - 1);
      });
    });
  };

  console.log("Component rendered!");

  return (
    <>
      <button onClick={handleClick}>Click Me</button>
      <p>Count1: {count1}</p>
      <p>Count2: {count2}</p>
    </>
  );
}

export default App;
```

As you can see below this time whenever we click the button our component is re-rendering two times for both the state updates.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1693346884958/782835ac-058b-40b8-a796-ae44c03a3d07.gif align="center")

### Final Notes 🎬

In conclusion, you might not always have to worry about the multiple re-renders in React, especially for smaller applications but as your application grows and has a complex nested component hierarchy unnecessary re-renders can significantly slow down your app and affect the user experience.

By intelligently grouping multiple state updates into a single render cycle React minimizes the unnecessary re-renders of your application to provide a smoother, improved experience to the users.

Finally, if you've liked this article please leave a ❤️ below and consider following me on [Twitter](https://twitter.com/imvedanshmehra) I regularly share content related to web development, Javascript, software engineering best practices, and a little bit of humor too.

See you in the next article 👋