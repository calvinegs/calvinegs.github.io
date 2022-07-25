---
title: "使用 Snowpack + Typescript 來建置一個 Web App"
date: 2022-05-01
description: "前端技術（架構）的蓬勃發展，使得建構（打包）工具（Bundler）也隨之推陳出新。之前寫過使用 parcel.js 搭配 Typescript 來開發前端應用程式的筆記。今天要來試試更新的打包工具 Snowpack，"
tags: ["Snowpack", "Typescript", "Web App"]
categories: ["Typescript"]
---

Snowpack 號稱是更快速的前端建構工具，在開發 Web 應用程式的工作流程中，它可以替代更重、更複雜的打包工具，如 webpack 或 Parcel。

在此不會討論 Snowpack 的設計細節，只紀錄如可快速的使用 Snowpack 來建置一個由 Typescript 支援網頁程式。由撰寫 Typescript 的過程中了解到強型別（及與搭配 vscode 編輯器）所帶來的好處。

## 使用 create-snowpack-app 建立專案
```bash
$ mkdir doto-list && cd todo-list
$ 
$ npx create-snowpack-app . --template @snowpack/app-template-blank-typescript --force
$ npm install
$ code .
```
産生的程式結構如下：

![程式結構](https://user-images.githubusercontent.com/21993717/166859633-95c09b00-2d36-4912-9d5a-91c5ff33ad32.png)

package.json 的內容：

```json {hl_lines=[3,17,18]}
{
  "scripts": {
    "start": "snowpack dev",
    "build": "snowpack build",
    "test": "echo \"This template does not include a test runner by default.\" && exit 1",
    "format": "prettier --write \"src/**/*.{ts,js}\"",
    "lint": "prettier --check \"src/**/*.{ts,js}\""
  },
  "dependencies": {
    "canvas-confetti": "^1.2.0"
  },
  "devDependencies": {
    "@snowpack/plugin-typescript": "^1.2.1",
    "@types/canvas-confetti": "^1.0.0",
    "@types/snowpack-env": "^2.3.3",
    "prettier": "^2.2.1",
    "snowpack": "^3.3.7",
    "typescript": "^4.2.4"
  }
}
```
執行程式 `$ npm start`

![npm start](https://user-images.githubusercontent.com/21993717/166859960-a835a6e4-e2dd-4f9a-a455-4075206513b1.png)


## 開始建置 Todo List 網頁

### 調整 index.html

首先將 public/index.html 內容修改如下：

```html {hl_lines=[8,"10-15","18-22]}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="description" content="Web site created using create-snowpack-app" />
    <script type="module" src="/dist/index.js"></script>
    <title>Snowpack App</title>
    <style>
      #list {
          list-style: none;
          padding: 0;
      }
    </style>
  </head>
  <body>
    <ul id="list"></ul>
    <form action="" id="new-task-form">
        <input type="text" name="" id="new-task-title">
        <button type="submit">Add</button>
    </form>
  </body>
</html>
```

### 安裝額外的 module
``` bash
$ npm install uuid 
$ npm install types@uuid --save-dev # uuid moduel 不是 Typescript 所撰寫，要將 uuid 使用在 Typescript 中必須搭配額外的宣告檔，透過這個宣告檔，我們就能享受強型別的好處
```
```json {hl_lines=[5,11]}
{
  
  "dependencies": {
    "canvas-confetti": "^1.2.0",
    "uuid": "^8.3.2"
  },
  "devDependencies": {
    "@snowpack/plugin-typescript": "^1.2.1",
    "@types/canvas-confetti": "^1.0.0",
    "@types/snowpack-env": "^2.3.3",
    "@types/uuid": "^8.3.4",
    "prettier": "^2.2.1",
    "snowpack": "^3.3.7",
    "typescript": "^4.2.4"
  }
}
```

### 使用 uuid 

修改 src/index.ts 內容

```js
import {v4 as uuidV4 } from 'uuid';

console.log(uuidV4());
```

存檔後由瀏覽器 Console 視窗中可以看到由 uuid module 提供的功能所産生的 ID： ｀637fd7c4-1bc3-444a-9335-4b355617edbd｀

```js
import {v4 as uuidV4 } from 'uuid';

//console.log(uuidV4());

const list = document.querySelector('#list');
```

### 開始撰寫程式
```js
import {v4 as uuidV4 } from 'uuid';

// console.log(uuidV4());

const list = document.querySelector('#list');
const form = document.getElementById('new-task-form') as HTMLFormElement | null;
const input = document.querySelector<HTMLInputElement>('#new-task-title');
```

將滑鼠移至 list 變數，可以發現目前 list 的型別是 "Element 或 null"。

![image](https://user-images.githubusercontent.com/21993717/166862306-965a5898-5fd7-415a-afcd-b1d8d1fe2cee.png)

透過 index.html 我們可以知道 #list 其實是 ul list，在透過 TypeScript 來撰寫程式時我們可以多利用它的型別檢核，來更方便、更正確的使用程式中的變數。

```js {hl_lines=[5]}
import {v4 as uuidV4 } from 'uuid';

//console.log(uuidV4());

const list = document.querySelector<HTMLUListElement>('#list');
```

透過上述程式，使用 泛型 指定querySelector 回傳的是｀HTMLUListElement｀，再將滑鼠移至 list 變數，可以發現目前 list 的型別已經變成是 "HTMLUListElement 或 null"。這樣的好處是在接下去的程式中可以更精準的使用 list 這個物件。當使用了不是 HTMLUlistElement的屬性時，編輯器就會給你警告，讓程式錯誤在編輯時期就能即時發現。這就是 Typescript 強型別的好處。

![image](https://user-images.githubusercontent.com/21993717/166863064-558d3baf-2413-4316-9541-d97924d24d65.png)

將 index.ts 程式修改如下：
```js {hl_line=[5]}
import {v4 as uuidV4 } from 'uuid';
// console.log(uuidV4());

const list = document.querySelector<HTMLUListElement>('#list');
const form = document.getElementById('new-task-form') as HTMLFormElement | null;
const input = document.querySelector<HTMLInputElement>('#new-task-title');
```
要注意的是 document.getElementById 的語法必不支援泛型，必須改成強制轉型的方式來限制回傳的型別
                
繼續完成 index.ts 程式，當你輸入`form.addEventListener` 按下 tab 時，VSCode 編輯器會自動將你輸入的程式改成 `form?.addEventListener`。原因是編輯器知道 `form` 變數的型別是 `HTMLFormElement | null` 而自動幫你調整合適的語法。 一樣的道理，在你輸入 `input.value` 後編輯器也會幫忙調成 `input?.value`。

```js {hl_line=[5]}
import {v4 as uuidV4 } from 'uuid';
// console.log(uuidV4());

const list = document.querySelector<HTMLUListElement>('#list');
const form = document.getElementById('new-task-form') as HTMLFormElement | null;
const input = document.querySelector<HTMLInputElement>('#new-task-title');

form?.addEventListener("submit", e => {
  e.preventDefault();

  if (input?.value == "" || input?.value == null) return;

  input.value
})
```

![image](https://user-images.githubusercontent.com/21993717/166892639-842153ae-448b-433f-b76c-37736d703d39.png)

在上圖程式第 11 行，可發現此時的 `input` 變數的型別是 `HTMLInputElement | null`，但在本行程式後，由於已排除 null 的可能性，所在在程式第 13 行的 `input` 變數的型別已成為 `HTMLInputElement`(如下圖)

![image](https://user-images.githubusercontent.com/21993717/166892102-36c54c57-d410-48ee-93fc-9549ef08f2ba.png)

繼續完成當按下 "Sumit" 按鈕時要將新的 Task 加入到 Doto list 清單中

```js
import {v4 as uuidV4 } from 'uuid';
// console.log(uuidV4());

const list = document.querySelector<HTMLUListElement>('#list');
const form = document.getElementById('new-task-form') as HTMLFormElement | null;
const input = document.querySelector<HTMLInputElement>('#new-task-title');

form?.addEventListener("submit", e => {
  e.preventDefault();

  if (input?.value == "" || input?.value == null) return;

  // 取得一個新的 Task 
  const newTask = {
    id: uuidV4(),
    title: input.value,
    completed: false,
    createdAt: new Date()
  };

  // 透過 addListItem 這個 Function 來新增一個 Todo List
  addListItem(newTask);
})

function addListItem(task) {

}
```
完成上述程式後，發現在 VSCode 編輯器中給了一個警告，告訴我們參數的型別不符，在 function 宣告中應該為參數設定適當的型別

![image](https://user-images.githubusercontent.com/21993717/166893783-4be0d9f5-fa3b-444d-b407-cc72ac51c91c.png)

透過編輯器我們可以知道 `newTask` 的型別

![image](https://user-images.githubusercontent.com/21993717/166894433-85e47323-3d24-4bb4-a922-e815a6f77adf.png)

將 function 中參數型別設定成如下圖，編輯器不再有警告訊息

```js
function addListItem(task : { 
  id: string, 
  title: string, 
  completed: boolean, 
  createdAt: Date 
}) {

}
```

但這樣的寫法顯然的不是好方式，改寫成如下，將 Task 的型別定義成一個叫 `Task` custom type，並將這個新的型別分別套用在 `NewTask` 宣告的地方以及 `addListItem` function 宣告的地方
``` js {hl_lines=["4-9",21,32]}
import {v4 as uuidV4 } from 'uuid';
// console.log(uuidV4());

type Task = { 
  id: string; 
  title: string;
  completed: boolean;
  createdAt: Date; 
}

const list = document.querySelector<HTMLUListElement>('#list');
const form = document.getElementById('new-task-form') as HTMLFormElement | null;
const input = document.querySelector<HTMLInputElement>('#new-task-title');

form?.addEventListener("submit", e => {
  e.preventDefault();

  if (input?.value == "" || input?.value == null) return;

  // 取得一個新的 Task 
  const newTask: Task = {
    id: uuidV4(),
    title: input.value,
    completed: false,
    createdAt: new Date()
  };
  
  // 透過 addListItem 這個 Function 來新增一個 Todo List
  addListItem(newTask);
})

function addListItem(task : Task) { }
```

經過強型別的設計可以很方便的在程式撰寫時就找出可能有問題的程式碼，如下圖：當傳給 addListItem 的參數型別不正確時，編輯器立即會顯示警告訊息。

![image](https://user-images.githubusercontent.com/21993717/166896731-d652d9b8-1984-44d7-b005-0856a17a0874.png)

繼續完成 `addListItem` function 的處理邏輯：

```js
function addListItem(task : Task) { 
  const item = document.createElement("li");
  const lable = document.createElement("label");
  const checkbox = document.createElement("input");
  checkbox.type="checkbox";
  checkbox.checked = task.completed;
  lable.append(checkbox, task.title);
  item.append(lable);
  list?.append(item);
}
```
執行結果

![image](https://user-images.githubusercontent.com/21993717/166901827-95fdf26f-2d90-4692-855d-2bafd0bd7a40.png)

程式至此已經可以加入新 Task 也可以設定（勾選）是否已完成，但當按下 瀏覧器的 ｀重新載入此頁｀ 功能時會發現所有新增的 Tasks 都會不見，接下來我們將加 Local Storeage 的功能將新增的 Tasks 不僅顯示在畫面上也會寫入到 Local Storeage 中，在重新載入時會將紀錄在 Local Storeage 的 Tasks 再重新顯示在畫面中。

## 完成後程式

```js {hl_lines=[13,14,43,47,"53-55","57-61"]}
import {v4 as uuidV4 } from 'uuid';

type Task = { 
  id: string; 
  title: string;
  completed: boolean;
  createdAt: Date; 
}

const list = document.querySelector<HTMLUListElement>('#list');
const form = document.getElementById('new-task-form') as HTMLFormElement | null;
const input = document.querySelector<HTMLInputElement>('#new-task-title');
const tasks: Task[] = LoadTasks();  // TasK[] 預設由 Local Storeage 中載入所有已新增的 Task
tasks.forEach(addListItem); //將 Task[] 中的 Task 顯示到畫面上

form?.addEventListener("submit", e => {
  e.preventDefault();

  if (input?.value == "" || input?.value == null) return;

  // 取得一個新的 Task 
  const newTask: Task = {
    id: uuidV4(),
    title: input.value,
    completed: false,
    createdAt: new Date()
  };
  tasks.push(newTask);
  
  // 透過 addListItem 這個 Function 來新增一個 Todo List
  addListItem(newTask);
  input.value = ""; // 將 input 清空
  input.focus();    // 將焦點重新定位在 input element 上

})

function addListItem(task : Task) { 
  const item = document.createElement("li");
  const lable = document.createElement("label");
  const checkbox = document.createElement("input");
  checkbox.addEventListener("change", () => {
    task.completed = checkbox.checked;
    saveTasks();  // checkbox 有變化時，將 Tasks 寫入 Local Storeage
  })
  checkbox.type="checkbox";
  checkbox.checked = task.completed;
  saveTasks();  // 新增 Task 時，將 Tasks 寫入 Local Storeage
  lable.append(checkbox, task.title);
  item.append(lable);
  list?.append(item);
}

function saveTasks() {
  localStorage.setItem("TASKS", JSON.stringify(tasks));
}

function LoadTasks(): Task[] {
  const taskJSON = localStorage.getItem("TASKS");
  if (taskJSON == null) return [];
  return JSON.parse(taskJSON);
}
```
在瀏覧器的 “開發人員工具” 的 “Application" 功能中透過 Clear All 可將 Local Storage 資料清除。清除後再按下 “重新載入這個網頁” 畫面會回到初始狀態。

![image](https://user-images.githubusercontent.com/21993717/166930062-45d6f41f-eb9e-443c-967c-c8c9696d27c0.png)

![螢幕擷圖](https://user-images.githubusercontent.com/21993717/166931162-c71b124c-4d18-482c-b8e2-26f18fd51d54.png)


新增 Task、改變 Task 完成否，再 “重新載入這個網頁”，畫面如我們希望的保留了我們已新增/修改的狀態。

![螢幕擷圖](https://user-images.githubusercontent.com/21993717/166931328-ff6ce2e6-72df-4a21-8f9b-0d9427bbf51a.png)