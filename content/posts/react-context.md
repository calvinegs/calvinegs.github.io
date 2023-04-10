---
title: "Context API in ReactJS"
date: 2023-04-10
draft: false
description: "使用 context api 來傳遞資料給子元件"
tags: ["reactjs", "context api"]
categories: ["reactjs"]
---

*[github Source code](https://github.com/calvinegs/react-contextapi.git)*


# Context API in ReactJS

Context API 是 ReactJS 中的一項功能，它允許跨組件樹共享資料，而不必通過樹的每一層向下傳遞 props。它提供了一種通過組件樹傳遞資料而無需使用 props 的方法，使用 props 這對於具有多層組件的大型應用程序來說可能會變得很麻煩。

## 使用 vite 建立一支 react web app 樣版

使用 yarn 語法

```bash
$ yarn create vite react-context -- --template react && cd react-context
$ yarn
$ yarn dev
```

使用 npm 語法
```bash
$ npm create vite react-context -- --template react && cd react-context
$ npm i
$ npm run dev
```


## 使用 props 來傳遞資料

先來看看原來使用 props 來傳遞資料給子元件的方式。

將樣版中的 App.jsx 內容先全部移除，改以下列的程式：

```js {linenos=table,hl_lines=[3,5,9,13,17]}
import './App.css'

const App = () => {
  return(
    <ParentClass color= "blue"/>
  );
}

const ParentClass = (props) => (
  <ChildClass color= {props.color} />
)

const ChildClass = (props) => (
  <GrandChildClass color= {props.color} />
)

const GrandChildClass = (props) => (
  <p>Color: {props.color}</p>
)
```

執行程式

```bash
$ npm run dev
```

結果：

![image](https://user-images.githubusercontent.com/21993717/230811476-1e0b3a7f-c274-47bb-8c71-9865a7481060.png)


在上述範例中，可以很清楚的看到缺點，由 App 傳入的 Color 資料值，實際上的使用者是 GrandChildClass，中間的 ParentClass 及 ChildClass 都只是過水，為了讓 GrandChildClass 可以正確的接收到 App 傳遞出的 color props，中間的這二層物件不得不重覆性的接收／傳遞出相同的 color props，顯然地不是一個最佳方式。

## 使用 Context api

來看看使用 Context api 是如何更簡潔地達到相同的目的。

### 使用 createContext 方法建立一個 Context 物件。

為了使用 Context API，首先需要使用 createContext() 方法創建一個 Context 物件。該物件將提供一種在組件樹中未直接相連的組件之間共享資料的方法。

新增一支 ColorContext.js 檔案，內容如下：

```js {linenos=table,hl_lines=[]}
import { createContext } from "react";

export const ColorContext = createContext("yellow");
```

註：context 可以傳遞的不僅限於'值`，也可以是


### 定義一個可提供資料給子元件的 provider component 函式、物件及陣列等等

新增一支 ColorProvider.js 程式，內容如下;

```js {linenos=table,hl_lines=[]}
function ColorProvider(props) {
  const [color, setColor] = useState("blue");

  return (
    <ColorContext.Provider value={{ color, setColor }}>
      {props.children}
    </ColorContext.Provider>
  );
}
```

### 建立子元件

新增最底層子元件 - GrandChild Component。

在這個元件中使用 useContext hook 來取得最上層程式由 context 傳來的 State 資料 (color)。

```js {linenos=table,hl_lines=[2,5]}
import { useContext } from "react";
import { ColorContext } from "./ColorContext";

const GrandChildComponent = () => {
    const { color, setColor} = useContext(ColorContext);

    return (
        <p>Color: {color}</p>
    )
}

export default GrandChildComponent;
```

新增次底層子元件 - Child Component，在這個‘過渡’層中不須任何傳遞資料的程式邏輯。

```js {linenos=table,hl_lines=[]}
import GrandChildComponent from "./GrandChildComponent";

const ChildComponent = (props) => {
    return (
        <GrandChildComponent></GrandChildComponent>
    )
}
export default ChildComponent;
```

新增第一層子元件 - Parent Component, 這也是一個‘過渡’層中也不須任何傳遞資料的程式邏輯。

```js {linenos=table,hl_lines=[]}
import ChildComponent from "./ChildComponent";

function ParentComponent() {
    return (
        <ChildComponent></ChildComponent>
    )
}

export default ParentComponent;
```

### 將子元件包裹在 ColorContext Provider 中

在 App 程式中將 State 資料透過 Context Api (Context Provider) 傳給最底層的子元件。

```js {linenos=table,hl_lines=[]}
import './App.css'
import { ColorContext } from './ColorContext';

import { useState } from 'react';
import ParentComponent from './ParentComponent';

const App = (props) => {
  const [color, setColor] = useState("pink");

  return (
    <ColorContext.Provider value={{ color, setColor }}>
      <ParentComponent/>
    </ColorContext.Provider>
  )
}

export default App
```


![image](https://user-images.githubusercontent.com/21993717/230811476-1e0b3a7f-c274-47bb-8c71-9865a7481060.png)


整個程式，由 App function 來設定 color 值，而由最底層的子元件來顯示 color 值，使用 Context Api 來傳值是不是顯得比使用 props 來得更為簡潔許多！