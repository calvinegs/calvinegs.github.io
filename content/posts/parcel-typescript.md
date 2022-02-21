---
title: "使用 Parcel.js + Typescript 來建置一個 Web App"
date: 2022-02-20
draft: false
description: "使用 Typescript 來開發前端應用程式已是一個趨勢，透過 Parcel.js 的幫助，可以快速地建置一個簡潔又有彈性的開發環境，來學習 Typescript"
tags: ["Parcel.js", "Typescript", "Web App"]
categories: ["Typescript"]
---

使用 Typescript 來開發前端應用程式已是一個趨勢，這篇筆記是用來紀錄如何用 Parcel.js 來建置一個簡潔又有彈性的開發環境用來學習 Typescript。

## 使用的工具 & 作業系統

- git
- vscode
- Mint 20.2 (作業系統)

## 專案初始化

```bash
$ mkdir parcel_ts && cd parcel_ts # 建立專案目錄
$ npm init -y   # 初始化專案目錄
$ touch Startup.ts Employee.ts index.html # 新增三個空白程式檔
$ npm install --save-dev parcel parcel-bundler # 安裝 parcel & parcel-bundler
$ code . # 開啟 vs code
```

## 在 package.json 中建立執行程式的 script

```json
"script": {
    "dev": "parcel index.html"
}
```

## 使用 Typescript 撰寫程式

> Employee.ts

```ts
export class Employee {
  constructor(
    private name: string = "no name",
    private department: string = "no department"
  ) {}
  displayInfo(): string {
    return `${this.name} department is ${this.department}`;
  }
}
```

> Startup.ts

```ts
import { Employee } from "./Employee";

window.onload = () => {
  let information = document.querySelector("#info");
  const employee = new Employee("Dany Green", "Market");
  if (employee) information.innerHTML = employee.displayInfo();
};
```

> index.html

```html
<!-- ! : 輸入 "!" 按下 Tab 或 Enter鍵，Emmet 將自動建立 html5 樣版檔案-->
<!-- 透過 Tab 鍵可快速跳至 <title></title> -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>What's up</title>
  </head>
  <body>
    <!-- 直接輸入以下 Emmet 語法: div[name="container"]>h2#info, 即可産生以下 Html div 內容-->
    <div name="container">
      <h2 id="info"></h2>
    </div>

    <!-- 直接輸入以下 Emmet 語法: script[type="module" src="Startup.ts"] , 即可産生以下 Html script 內容-->
    <script type="module" src="Startup.ts"></script>
  </body>
</html>
```

## 執行程式

```bash
$ npm run dev

> parcel_ts@1.0.0 dev
> parcel index.html

Server running at http://localhost:1234
✨ Built in 22ms
```

![image](https://user-images.githubusercontent.com/21993717/154879712-d9317cc6-8b92-407d-8337-cd458b091e25.png)

## Typescript 轉譯成 Javascript

> 程式執行後，可發現在 dist 目錄下，Parcel.js 已經自動的 Typescript 程式轉譯成 Javascript 了。
> ![image](https://user-images.githubusercontent.com/21993717/154881115-a298bcf0-39f9-4393-8a85-787f2712212c.png)

同時也把 HTML 檔案中的 .ts 自動的轉成 .js
![image](https://user-images.githubusercontent.com/21993717/154881354-6214d7d0-be8d-4176-ab73-d4286eb0b03f.png)

## 結論

> 你會發現透過 Parcel 的幫助，當使用 Typescript 來撰寫 網站程式即方便又快速。