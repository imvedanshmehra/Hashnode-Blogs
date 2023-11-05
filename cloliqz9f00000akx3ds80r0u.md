---
title: "Virtual Scrolling in React: Implementation from scratch and using react-window"
seoTitle: "Virtual Scrolling In React"
seoDescription: "How to implement virtual scrolling in React from scratch or using react-window?"
datePublished: Sun Nov 05 2023 13:38:23 GMT+0000 (Coordinated Universal Time)
cuid: cloliqz9f00000akx3ds80r0u
slug: virtual-scrolling-in-react-implementation-from-scratch-and-using-react-window
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1699107072007/4451b4d0-8304-412d-b5c8-485c60953ca7.png
tags: javascript, performance, reactjs, frontend-development, virtualization

---

How many times you've been in a situation where you have to render a list of items in your web application? It is a very common thing when building modern web apps these days. Well, the problem comes when you have to render very huge datasets let's say 100,000 or more data all at once, and that too without any pagination. This will pollute the DOM and occupy so much of the browser's memory leading to a lot of performance issues and degraded user experience.

**Virtual scrolling** is one of the techniques that can save your back in this situation by only rendering the items that the user actually wants to see while the rest of the items will be loaded virtually as the user scrolls towards them instead of rendering everything at once.

In this article, we'll see what virtual scrolling is and how can we implement virtual scroll in a React application to render a list of 100,000 items with no pagination.

## What is Virtual Scrolling? 🧐

As we have already discussed, rendering a huge list of data on the DOM with no pagination can make the application slower by polluting the DOM, consuming too much of the browser's memory, and on top of that the end user is not going to consume all the data at once, they'll only see a subset of that data and then scroll through the list to see more data.

Virtual scroll or **Windowing** is a technique in web app development that will only render items that are visible in the viewport while the rest of the items will be virtualized by top and bottom padding. As we scroll through the list, the older items will be destroyed and new items will be displayed based on the position of the scrollbar. This way we won't put too much load on the DOM by rendering everything at once. Using this technique in the correct scenario can greatly improve the performance of the application.

## Do we need Virtualization? 💭

Now that we know what virtual scrolling is, the question comes - **"Do we always need virtual scroll?"**

The answer to this question should be quite obvious, If you are building a web app where you have to render a huge list of items without any pagination and you truly care about the performance then definitely virtual scrolling is what you can use.

While it is a good way to improve performance there are a few scenarios in which you might not need to implement virtual scrolling:

* **If you are rendering a list of less than 50 or 100 data**\- If you are rendering a list with a very items say 50 or 100 items, virtual scrolling might just be an overhead and would not make any huge difference on the performance of the app.
    
* **When rendering a paginated list**\- When rendering a list with some sort of [pagination](https://en.wikipedia.org/wiki/Pagination), then obviously virtual scrolling might not be of any use because the whole point of virtual scrolling is to render list items efficiently when there is no pagination implemented.
    

## Virtualization in React app from scratch

Now when we have some idea about virtual scrolling and when to use it, we'll see how can we implement virtual scrolling in a react application without using any third-party tools.

To get started with this approach, we'll make a few assumptions:

* the height of each row in the list will be fixed.
    
* total number of items to be rendered in the list is known upfront.
    

If we meet the above condition, let's create a new React app and build a `VirtualScroll` component.

```javascript
import { useState } from "react";

const VirtualScroll = ({
  rowHeight,
  totalItems,
  items,
  visibleItemsLength,
  containerHeight,
}) => {
  // Calculate the total height of the container
  const totalHeight = rowHeight * totalItems;
  //   Current scroll position of the container
  const [scrollTop, setScrollTop] = useState(0);
  // Get the first element to be displayed
  const startNodeElem = Math.ceil(scrollTop / rowHeight);
  // Get the items to be displayed
  const visibleItems = items?.slice(
    startNodeElem,
    startNodeElem + visibleItemsLength
  );
  //  Add padding to the empty space
  const offsetY = startNodeElem * rowHeight;

  const handleScroll = (e) => {
    // set scrollTop to the current scroll position of the container.
    setScrollTop(e?.currentTarget?.scrollTop);
  };

  return (
    <div
      style={{
        height: containerHeight,
        overflow: "auto",
        border: "5px solid black",
      }}
      onScroll={handleScroll}
    >
      <div style={{ height: totalHeight }}>
        <div style={{ transform: `translateY(${offsetY}px)` }}>
          {visibleItems}
        </div>
      </div>
    </div>
  );
};

export default VirtualScroll;
```

In the code above, we are doing very basic maths, we first calculate the total height of the container `totalHeight` by multiplying a single row's height `rowHeight` with total items to be rendered `totalItems`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1699174051660/e76f5890-3fdc-496c-a076-6a8fd307856e.png align="center")

Then we divide the container's scroll position from the top `scrollTop` with a single row's height `rowHeight` to get the starting node element `startNodeElem` which will be rendered on the list, we are also using `Math.ceil` to round up the value.

Then we slice `totalItems` to only show items starting from the `startNodeElem` till `startNodeElem + visibleItemsLength`. So, let's say our `startNodeElem = 5`, then we'll show items from the 5th position till `5 + visibleItemsLength` considering `visibleItemsLength` to be **20** in this case. So our component will render items starting from the 5th position till `5 + 20 = 25` position.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1699174868848/c631e10a-699f-4c91-9642-3a08eca77e15.png align="center")

Now every time the user scrolls the container, the value of `scrollTop` will be re-calculated and `startNodeElem` will be updated based on the scroll position and we'll shift the nodes down using `transform: translateY` property.

We can now use our `VirtualScroll` component to render a list of items.

```javascript
import React from "react";
import VirtualScroll from "./virtual-scroll";
// Total items to be rendered
const totalItems = 100000;

const items = new Array(totalItems).fill(null).map((_, index) => {
  return (
    // Height of the div should be the same as what we are sending in rowHeight
    <div style={{ height: "70px" }} key={index}>
      <p>Row Number - {index}</p>
    </div>
  );
});

function App() {
  return (
    <>
      <h1>Virtual Scroll From Scratch</h1>
      <VirtualScroll
        rowHeight={70}
        totalItems={totalItems}
        containerHeight="500px"
        items={items}
        visibleItemsLength={20}
      />
    </>
  );
}
export default App;
```

Here, we are rendering a list of 100000 items and we have set `containerHeight` to 500px and the height of each row or the `rowHeight` to 70px.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1699176236306/1e3cd813-00d1-4a16-96cc-25fe34ac42b2.gif align="center")

Now start your application to see virtual scrolling in action.

As you can see, when we are scrolling through the list we are not piling up extra nodes but instead, the previous nodes are destroyed and we are rendering new nodes based on the container's scroll position.

Here's the sandbox output with the above implementation:

%[https://codesandbox.io/s/virtualisation-in-react-pvg2cx?file=/src/virtual-scroll.js] 

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">You might be noticing some white space while scrolling and that is because every time the container is scrolled the entire DOM is manipulated and we are re-calculating everything.</div>
</div>

So this was a very naive implementation of virtual scrolling from scratch in a react application that can of course be more optimized for better results.

## Virtual Scrolling in React using react-window

As we have seen in the previous section we can implement virtual scrolling from scratch. In this section, we'll discuss a third-party tool called [react-window](https://github.com/bvaughn/react-window) that allows us to implement virtual scrolling in a much easier way.

`react-window` is a very lightweight React package that lets you render a virtualized list while removing all the extra work that you might have to put in when implementing a solution from scratch.

To get started with react-window, we first need to install it into our react application.

```bash
# Yarn
yarn add react-window

# NPM
npm install --save react-window
```

With react-window, we can basically render lists with fixed row height and also the lists with dynamic row height, we'll go off by each of them one at a time.

### react-window with fixed size list

If you have to render list items with fixed row height you can use the `FixedSizeList` component from react-window:

```javascript
import { FixedSizeList } from "react-window";

const Row = ({ index, style }) => (
  /* Adding style attribute is very important here
    it supplies the row height to the elements. */
  <div style={style}>Row {index}</div>
);

// Create a component with Fixed Size example
const ReactWindowFixedSizeEx = () => (
  <FixedSizeList height={150} itemCount={100000} itemSize={35} width={300}>
    {Row}
  </FixedSizeList>
);

export { ReactWindowFixedSizeEx };
```

In the above example, we have set the container's height to **150px** and each of the row's height will be **35px**.

%[https://codesandbox.io/s/react-window-example-m5tkt9?file=/src/App.js] 

### react-window with variable size list

If you have to render list items with dynamic row height, you can use the `VariableSizeList` component from react-window:

```javascript
import { VariableSizeList } from "react-window";
import "./App.css";
const totalItemsCount = 100000;

// Set random row heights
const rowHeights = new Array(totalItemsCount)
  .fill(true)
  .map(() => 12 + Math.round(Math.random() * 50));

const getItemSize = (index) => rowHeights[index];

const Row = ({ index, style }) => (
  <div style={style} className={index % 2 ? "odd" : "even"}>
    Row {index}
  </div>
);

// Create a component with Fixed Size example
const ReactWindowVariableSizeEx = () => (
  <VariableSizeList
    height={500}
    itemCount={totalItemsCount}
    itemSize={getItemSize} 
    width={300}
  >
    {Row}
  </VariableSizeList>
);

export default ReactWindowVariableSizeEx;
```

In the above example, the container's height will be **500px** while the row's height will be arbitrary and we are making use of the `itemSize` prop to provide the height of a particular row.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">In the above code, we are setting arbitrary row height for each of the rows but in real application there should be some actual logic to calculate the heights of the rows.</div>
</div>

%[https://codesandbox.io/s/react-window-variable-size-list-example-64ynfm] 

Awesome! So we now have seen different ways to implement virtual scrolling in a react application, here is the GitHub repo link with each of these implementations if you want to check - [https://github.com/imvedanshmehra/virtual-scroll-react](https://github.com/imvedanshmehra/virtual-scroll-react).

## Final Words 🗣️

Virtualization is a very good technique to improve the performance of the application when rendering a large set of data without pagination, you can either implement it from scratch or use packages like `react-window`.

You can take a step further and make it more optimized by lazy loading the data to be rendered by using `infinite scroll` which is yet another very good technique to render a large set of data.

With this, I hope you enjoyed reading this article as much as I enjoyed writing it and I am hoping that you could get to learn something from it.

If you like the blog, please show your love by clicking on the ❤️ icon below and leave your thoughts in the comments.

If you'd like to read more such articles you can give me a follow. Also connect with me on [Twitter](https://twitter.com/imvedanshmehra) or [LinkedIn](https://www.linkedin.com/in/imvedanshmehra/) where I regularly share content related to web development, Javascript, & software engineering best practices.