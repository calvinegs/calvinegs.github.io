---
title: "Setup Typescript Environment"
date: 2021-12-07
draft: false
description: "設定 Typescript 的開發環境 (nodejs)"
tags: ["typescript"]
categories: ["typescript"]
---

# 設置 TypeScript 開發環境

TypeScript 已經支援前端開發的許多架構

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

為 nodejs 安裝類型檔

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

- rootDir: TypeScript 找尋程式的地方。
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
  "exec": "ts-node ./src/index.ts"
}
```

在 package.json 中加入啟動的 script

```shell
"start": "nodemon",
```
