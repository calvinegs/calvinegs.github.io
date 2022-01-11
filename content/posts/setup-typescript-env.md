---
title: "設置 TypeScript 開發環境 (Node.js)"
date: 2021-12-07
draft: false
description: "建置一個學習 Typescript 的開發環境 (Node.js)"
tags: ["typescript", "Node.js"]
categories: ["typescript"]
---


TypeScript 不僅已可使用在主機端來進行 Node.js 程式的開發，也支援前端開發的眾多架構，使用 TypeScript 有許多額外的好處，因此學習 TypeScript 是現在軟體開發工程師不可缺的一項技能。本文是用來記錄如何設置一個簡易的 TypeScript 開發環境，用來學習 TypeScript。

## 開始設置

建立一個目錄

```shell
$ mkdir typescript-starter
$ cd typescript-starter
```

透過 npm 產生 package.json 檔案

```shell
$ npm init -y
```

在 Local 端安裝 TypeScript

```shell
$ npm install typescript --save-dev #or -D
```

為 Node.js 安裝類型檔

```shell
$ npm install @types/node --save-dev
```

透過 tsc 建立 TypeScript 的設定檔 (tsconfig.json)

```shell
$ npx tsc --init
```

P.S. 由於 typescript 是安裝在 local，執行時要透過 npx 指令(由 npm 所提供)

指定額外的參數

```shell
$ npx tsc --init --init --rootDir src --outDir build --esModuleInterop --resolveJsonModule --lib es6 --module commonjs --allowJs true --noImplicitAny true
```

- rootDir: tsc 轉碼器找尋程式的地方。
- outDir: TypeScript 轉譯成 JavaScript 所存放的地方。
- esModuleInterop: 若使用 commonjs 為此專案的 module system，則此設定值必須設定為 true。
- resolveJsonModule: 若此專案會使用 JSON，則此設定值必須設定為 true。
- lib: 指定為 'es6' 指可使用到新版 JS 的一些語言特性，指定 'es5' 則會有較高的相容易。
- module: 指存 module system，如: commonjs。
- allowJs: 此選項將允許您在 .ts 文件中包含 .js 文件。
- noImplicitAny: 值為 true 時，在 TypeScript 文件中，不允許不明確地指定類型。

將多餘不會使用的參數移除

```shell
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "lib": ["es6"],
    "allowJs": true,
    "outDir": "build",
    "rootDir": "src",
    "strict": true,
    "noImplicitAny": true,
    "esModuleInterop": true,
    "resolveJsonModule": true
  }
}
```

建立 src 目錄，並在此目錄下寫程式

```shell
$ mkdir src
$ touch src/index.ts
```

程式如下:

```typescript
import * as cowsay from "cowsay";
console.log(
  cowsay.say({
    text: "I'm a moooodule",
    e: "oO",
    T: "U ",
  })
);
```

因為程式中使用到額外的 library - cowsay，要先安裝

```shell
$ npm install cowsay
```

利用 tsc 來轉譯 TypeScript 成為 JavaScript

```shell
$ npx tsc
```

轉譯成 es5 版本 JavaScript 的結果:

```js
"use strict";
// const aVar = 'abc';
// console.log('Hello world!' + `${aVar}`)
var __createBinding =
  (this && this.__createBinding) ||
  (Object.create
    ? function (o, m, k, k2) {
        if (k2 === undefined) k2 = k;
        Object.defineProperty(o, k2, {
          enumerable: true,
          get: function () {
            return m[k];
          },
        });
      }
    : function (o, m, k, k2) {
        if (k2 === undefined) k2 = k;
        o[k2] = m[k];
      });
var __setModuleDefault =
  (this && this.__setModuleDefault) ||
  (Object.create
    ? function (o, v) {
        Object.defineProperty(o, "default", { enumerable: true, value: v });
      }
    : function (o, v) {
        o["default"] = v;
      });
var __importStar =
  (this && this.__importStar) ||
  function (mod) {
    if (mod && mod.__esModule) return mod;
    var result = {};
    if (mod != null)
      for (var k in mod)
        if (k !== "default" && Object.prototype.hasOwnProperty.call(mod, k))
          __createBinding(result, mod, k);
    __setModuleDefault(result, mod);
    return result;
  };
Object.defineProperty(exports, "__esModule", { value: true });
var cowsay = __importStar(require("cowsay"));
console.log(
  cowsay.say({
    text: "I'm a moooodule",
    e: "Ox",
    T: "V ",
  })
);
```

## 使用一些有用的設定及 Script

安裝 ts-node 和 nodemon

ts-node 是一個 TypeScript 執行引擎和 Node.js 的 REPL。它以JIT方式將TypeScript轉譯為 JavaScript，使你能夠直接在 Node.js上 執行 TypeScript 而不需要預轉譯。通過 node 的模組載入 API 來實現的，使其能夠與其他 Node.js 工具和程式庫一起無縫使用。

nodemon 是一個工具，它通過檢測程式目錄中的文件更改時可以自動重新啟動應用程式(本範例中是啟動 Node.js)，以此協助應用程式開發。

```shell
$ npm install --save-dev ts-node nodemon
```

新增一個 nodemon 的設定檔 nodemon.json

```shell
$ touch nodemon.json
```

內容如下:

```shell
{
  "watch": ["src"],
  "ext": ".ts,.js",
  "ignore": [],
  "exec": "npx ts-node ./src/index.ts"
}
```

在 package.json 中加入啟動的 script

```shell
"start": "nodemon",
```

執行 scripts
```shell
PS F:\test\ts\ts-starter> npm start # npm start 是 npm run start 的 alias

> ts-starter@1.0.0 start
> nodemon                                                                                                                               

[nodemon] 2.0.15
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): src\**\*
[nodemon] watching extensions: ts,js
[nodemon] starting `npx ts-node ./src/index.ts`
 ________________________
< I'm a slaughtered deer >
 ------------------------
        \   ^__^
         \  (Ox)\_______
            (__)\       )\/\
             V  ||----w |
                ||     ||
[nodemon] clean exit - waiting for changes before restart
```

直接修改程式碼，將 index.ts 中的 `slaughtered deer` 改成 `deer`，存檔後，系統將自動轉譯程式碼，並透過 Node.js 顯示出結果:

![image](https://gist.github.com/calvinegs/e6c565f190a36c86513363145aeaddb2/raw/images---Tue_Dec_14_2021_1639449822437.png)