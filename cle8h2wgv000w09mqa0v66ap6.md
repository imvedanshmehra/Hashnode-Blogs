---
title: "Difference between useMemo and useCallback"
seoTitle: "What is the difference between useMemo and useCallback hook in React?"
seoDescription: "In this blog, I have talked about what is the difference between useMemo and useCallback and when to use one over another."
datePublished: Fri Feb 17 2023 11:53:36 GMT+0000 (Coordinated Universal Time)
cuid: cle8h2wgv000w09mqa0v66ap6
slug: difference-between-usememo-and-usecallback
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1681822368533/22801d24-9f3b-4fe4-b9bf-026169bbf39d.png
tags: reactjs, hashnode, hooks

---

Re-render is at the heart of React applications, Re-render happens when React components need to update the UI with new data. But there are times when some unnecessary re-renders can decrease the overall performance of the application. React has some in-built tools to prevent these unnecessary rerenders - `useMemo` and `useCallback`. Both of these hooks are used to reduce the re-render of a react app.

In this article, we are going to talk about what is the actual difference between these two hooks and when to use one over another.

## When does react component re-renders?

In general, React components re-render due to one of the following reasons:

* When `state` of the component changes.
    
* When the parent component re-renders.
    
* When the value of `context` provider changes, all the components using the `context` would re-render.
    

## When is re-rendering a problem?

Re-rendering itself is not a problem. React is so fast in performing these re-renders that most of the time it can handle them without users noticing anything. However, there are cases when these re-renders can slow down the application, especially when unnecessary re-render happens on heavy components that compute complex values.

## An example showing unnecessary re-render of a component

To illustrate this with an example, we will create a `Parent` and a `Child` component.

```javascript
const Parent = () => {
  const [num, setNum] = useState(0);

  return (
    <>
      <p>{num}</p>
      <button onClick={() => setNum(num + 1)}>+</button>
      <Child />
    </>
  )
}

const Child = () => {
  console.log("Child component re-rendered...");

  return (
    <h1>I am Child Component</h1>
  )
}
```

We created a `Parent` component which has a button element and a state `num` and every time we click on the button element the state updates and as a result, we see the updated value of `num` on the UI.

However, you might notice that every time we click on the button element the `Child` component also re-renders, we know that because every time we click on the button we see **Child component re-rendered...** printed on the console.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1676627604623/6dc0307d-0654-4c0e-b770-4d96b1d7e912.gif align="center")

## How to prevent unnecessary re-render using useMemo?

React has a built-in hook that can help us solve this problem - `useMemo`.

> useMemo(() =&gt; function, \[\])

`useMemo` takes a function and dependency array and it returns a memoized value.

In the above example, we have seen how `Child` component is re-rendering unnecessarily whenever we click on the button element in the `Parent` component. Let's see how we can prevent this re-render using `useMemo`.

```javascript
const Parent = () => {
  const [num, setNum] = useState(0);
  const getChild = useMemo(() => <Child />, []) // Wrap the Child     component inside useMemo hook.

  return (
    <>
      <p>{num}</p>
      <button onClick={() => setNum(num + 1)}>+</button>
      {getChild}
    </>
  )
}
```

This should now prevent `Child` component from re-rendering whenever the state updates in the `Parent` component.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1676627857830/8b589cd3-9b76-429d-b2e8-75d2797f2de6.gif align="center")

## A tricky catch with useMemo

We have seen how `useMemo` can solve the problem of unnecessary re-render but there is a catch. Let's find it out with the example below:

```javascript
const Parent = () => {
  const [num, setNum] = useState(0);

  const clickHandler = () => {
    // Do something...
  }

  const getChild = useMemo(() => <Child handleClick={clickHandler}/>, [clickHandler])

  return (
    <>
      <p>{num}</p>
      <button onClick={() => setNum(num + 1)}>+</button>
      {getChild}
    </>
  )
}

const Child = ({handleClick}) => {
  console.log("Child component re-rendered...");

  return (
    <>
      <h1>I am Child Component</h1>
      <button onClick={handleClick}>Button inside child</button> 
    </>
  )
}
```

We added a button inside the `Child` component which will call a callback function whenever it is clicked. It will get that callback function from its props.

Then, we create a `clickHandler` function inside the `Parent` component and passed that function as a dependency to `useMemo` and then we passed that function as a prop to the `Child` component.

This will again cause the same problem that we faced earlier. Every time the state changes in the `Parent` component our Child component will re-render.

This is happening because whenever a component re-renders in react, the functions inside that component are also created again. So in this case, whenever the `Parent` component is re-rendering due to a change in its state the `clickHandler` function is created again which rerenders the `Child` component.

## useCallback to Rescue

React has another built-in hook that can help us solve this problem - `useCallback`.

> useCallback(() =&gt; function, \[\])

`useCallback` is very similar to `useMemo`, the main difference is that the `useMemo` returns a memoized value whereas `useCallback` returns a memoized function.

Let's see how we can solve this problem using `useCallback`.

```javascript
const Parent = () => {
  const [num, setNum] = useState(0);
  
  // Wrap the clickHandler function with useCallback
  const clickHandler = useCallback(() => {
    // Do something...
  }, []);

  const getChild = useMemo(() => <Child handleClick={clickHandler} />, [
    clickHandler
  ]);

  return (
    <>
      <p>{num}</p>
      <button onClick={() => setNum(num + 1)}>+</button>
      {getChild}
    </>
  );
};
```

We just need to wrap the `clickHandler` function with `useCallback` hook to solve this problem. Thereafter, whenver the `Parent` component re-renders `clickHandler` will return the memoized function until one or more of its dependencies has not changed and due to this the `Child` component will not re-render unnecessarily.

Here is the complete code:

```javascript
const Parent = () => {
  const [num, setNum] = useState(0);

  const clickHandler = useCallback(() => {
    // Do something...
  }, []);

  const getChild = useMemo(() => <Child handleClick={clickHandler} />, [
    clickHandler
  ]);

  return (
    <>
      <p>{num}</p>
      <button onClick={() => setNum(num + 1)}>+</button>
      {getChild}
    </>
  );
};

const Child = ({ handleClick }) => {
  console.log("Child component re-rendered...");

  return (
    <>
      <h1>I am Child Component</h1>
      <button onClick={handleClick}>Button inside child</button>
    </>
  );
};
```

## Summary

Both `useMemo` and `useCallback` can be used to improve the performance of the React app by reducing unnecessary re-renders. At this point, most of us might be thinking that we should use these hooks for every function and component to improve performance but that is far from the truth. It's important to remember that `useMemo` and `useCallback` comes with a cost of creating a memoized value or function and if the values that we are computing are not very expensive and don't change very often they cannot outweigh that cost.

If the app is not performant, we should first try to find the underlying problem and fix it and then only use these hooks to improve the performance.

If you liked this article, you might like my tweets too - [Follow me on Twitter](https://twitter.com/imvedanshmehra).

Show your support by giving a thumbs up to this article and share your thoughts in the comments.