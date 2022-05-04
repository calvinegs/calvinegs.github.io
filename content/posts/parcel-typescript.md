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
註: 可參考另一編筆記是紀錄如何使用 Emmet 語法
*[透過範例學習 Emmet 語法](https://calvinegs.github.io/posts/emmet-syntax/)*



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

## 除錯

> 按下快速鍵 Ctrl+Shift+D 叫出 "Run and Debug" 功能，點選 create a launch.sjon file
![image](https://user-images.githubusercontent.com/21993717/166696988-e9e13d36-7b94-448c-bec7-5076c1321452.png)

> 點選 "Edge: Launch" 
![image](https://user-images.githubusercontent.com/21993717/166697548-86ab5d94-f72e-462b-abd2-2033e49072c3.png)

> 自動産生 launch.sjon
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        
        {
            "type": "pwa-msedge",
            "request": "launch",
            "name": "Launch Edge against localhost",
            "url": "http://localhost:8080", // 8080 改成 1234
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```
> 先將 "url": "http://localhost:8080" Port 改成1234,，並在檔案尾部加入以下設定，並存檔。
```json
            "breakOnLoad": true,
            "sourceMapPathOverrides": {
                "../*": "${webRoot}/*"
            }
```

> 再次按下快速鍵 Ctrl+Shift+D 叫出 "Run and Debug" 功能， 下拉選單中選擇 “Launch Edge against localhost"。在 Source code 中設定中斷點，再以滑鼠點選右邊的綠色三角型按鍵，開始進行除錯。VSCode 會自動啟動 edge 瀏覽器，同時程式會暫停在中斷點處。
![2022-05-04 22-10-29 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/166699593-1f896fd7-f4eb-4d9a-a0b5-2e3f7111497f.png)

![image](https://user-images.githubusercontent.com/21993717/166701338-a61e30f6-bdcf-4d85-a41e-b015bbb5ce1d.png)


## 結論

> 你會發現透過 Parcel 的幫助，當使用 Typescript 來撰寫 網站程式即方便又快速。