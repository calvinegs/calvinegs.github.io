---
title: "TypeScript 編譯器的選項"
date: 2022-09-24
draft: false
description: "常用 TypeScript 編譯器的選項 說明"
tags: ["typescript"]
categories: ["typescript"]
---

TypeScript 的許多功能是透過 TypeScript 編譯器 (tsc) 來實現的。tsc 有許多選項可以用來調整編譯過程的行為。

## 設置專案

建立一個目錄

```shell
$ mkdir tsc-options
$ cd tsc-options
```

### 透過 npm 產生 package.json 檔案

```shell
$ npm init -y
```

### 在 Local 端安裝 TypeScript

```shell
$ npm install --save-dev typescript@4.8.3 #or -D
```

## TypeScript 選項值設定

### 透過 tsc 建立 TypeScript 的設定檔 (tsconfig.json)

```shell
$ npx tsc --init
```

P.S. 由於 typescript 是安裝在 local，執行時要透過 npx 指令(由 npm 所提供)

### TypeScript 選項值影響

先在專案目錄下建立 src 子目錄，並在此目錄下撰寫程式

```shell
$ mkdir src
$ touch src/index.ts
```

程式如下:

```typescript
function add(a, b) {
    return a + b;
}
add(25, null);
```

上述的程式可否通過 tsc 型態檢核呢？ 答案很難回答，因為取決於“tsc 選項”的設定。

若直接以預設的情況執行編譯，則會有以下的錯誤訊息。

```shell
$ npx tsc
src/index.ts:1:14 - error TS7006: Parameter 'a' implicitly has an 'any' type.

1 function add(a, b) {
               ~

src/index.ts:1:17 - error TS7006: Parameter 'b' implicitly has an 'any' type.

1 function add(a, b) {
                  ~


Found 2 errors in the same file, starting at: src/index.ts:1
```

等你調整了 tsconfig.json 中的 "noImplicitAny" 選項（= false）設定後，再執行一次編譯處理，竟然就成功了！

```json {linenos=table,hl_lines=[7]}
//tsconfig.json
{
  "compilerOptions": {
    //...
    /* Type Checking */
    "strict": true,                                      /* Enable all strict type-checking options. */
    "noImplicitAny": false,                              /* Enable error reporting for expressions and declarations with an implied 'any' type. */
    // "strictNullChecks": true,                         /* When type checking, take into account 'null' and 'undefined'. */
    //...
  }
}
```

我們來看看到底是怎麼一回事
首先，先打開 tsconfig.json 選項設定檔中的 "declaration" 選項 （"declaration": true,）

```json {linenos=table,hl_lines=[6]}
//tsconfig.json
{
  "compilerOptions": {
  //...
  /* Emit */
      "declaration": true,           
  //...
  }
}
```

再執行一次編譯處理，隨著已開啟 "declaration": true 選項值，tsc 編譯器會在 index.ts 原始碼的相同目錄中産生了 index.js & index.d.ts 二個編譯結果檔案。

![image](https://user-images.githubusercontent.com/21993717/192183047-5dd79163-37ae-4b2a-8d7b-c434b44aa268.png)


index.js 是 index.ts 編譯完成的 js 程式結果檔。
index.d.ts 則是編譯過程式預設會産生的 TypeScript 型態定義檔。開啟 index.d.ts，內容如下：

```typescript
declare function add(a: any, b: any): any;
```

原來 tsc 把你在 index.ts 所撰寫沒有型態註記的程式碼，自行推論為型態都是使用 any。搭配著前述 “noImplicitAny"選項值是 true 或 false (預設值為 true) 才會産生編譯成功或失敗的結果呀。（“noImplicitAny”選項的用途是：若資料沒有型別註記，禁止 TypeScript 隱性推論其型別為 any 型別）

解決了一個疑問，心中是否又産生另一個疑問呢： 不是編譯失敗(型態有誤）嗎？怎麼還會産生 js 檔案呢？原來輸出程式碼與型態檢查的結果完全無關，所以型能有誤的程式是可以産生輸出的。你可以把 TypeScript 的錯誤視為是警告訊息，雖然可能是一個人值得追查的問題，但不會中斷組建的程序。那可否要求若有錯誤發生時不要産生結果檔案呢？可以的，透過“noEmitOnError”選項來控制，它的預設值是false，若你開啟 tsconfig.json “noEmitOnError”選項，並把它的值設成 true，則當編譯過程有錯誤時就不會還産生 js 檔案了。

## tsc 編譯選項

|編譯設定選項|說明|
|-----------------------------------|-----------------------------------------------------------------------------|
|allowJs| 是否允許原始碼中包括 JavaScript 檔案 (.js) |
|alwaysStrict| 在 JavaScript 啟用嚴格模式，並在每個文件中加入‘use strict’ |
|allowSyntheticDefaultImports| 模組沒有宣告預設匯出的功能，或遵循 commonJs 規範時，允許使用 import ... from ... 語法 (用來提高對舊版模組的相容性) |
|baseUrl| 設定根目錄位置，以便用相對路徑尋找模組|
|checkJs| 需搭配 allowJs 選項，在編譯 JavaScript 原始檔案時檢查其中的錯誤 |
|declaration| 對專案中的所有 TypeScript 或 JavaScript 原始檔産生 .d.ts 型別宣告檔 |
|downlevelIteration| 程式中若使用到　Symbol 完訪器，啟用此選項可更精確地對 ES5 環境模擬出適當的 ES2015 完訪器 |
|emitDecoratorMetadata| 需搭配 experimentalDecorators 選項，在輸出的 JavaScript 中包含裝飾器元數據 (decorator metadata) |
|esModuleInterop| 需搭配 allowSyntheticDefaultImports 選項，若 CommonJS/AMD/UMD 模組沒有宣告預設匯出的功能,就加入額外的輔助程式碼來使之符合 ES2015 模組。若使用 commonjs 為此專案的 module system，則此設定值必須設定為 true |
|experimentalDecorators| 允許使用函式裝飾器 (decorator) |
|forceConsitentCasingInFileNames| 要求匯入的模組名稱必須和實際檔案名稱有一致的大小寫 |
|importHelpers| 在針對舊版 JavaScript 編譯時，TypeScript 會插入輔助程式好模擬 ES2015 的功能。啟用此選項可減少輔助程式碼的重複量 |
|isolatedModules| 將每個檔案視為單獨模組，以利 Babel 工具一次編譯一個檔案時不會發生問題 |
|jsx| 決定 JSX/TSX 檔案中對 HTML 元素的轉譯方式|
|jsxFactory| 指定工廠函式(factory function)，用它來轉譯 JSX/TSX 檔案中的 HTML 元素 |
|lib| 指定編譯器要選使用的定義檔 |
|module| 指定用那種模組規範來進行編譯  |
|moduleResoultion| 指定模組的解析方式(可設為相容於 TypeScript 舊版的 "classic" 或新版的 "node")|
|noEmit| 要求編輯器不要輸出 JavaScript 程式碼，等同於只檢查程式碼正確與否 |
|noImplicitany| 若資料沒有型別註記，禁止 TypeScript 隱性推論其型別為 any 型別 |
|noImplicitReturns| 要求函式中的任何執行途徑都必須用 return 傳回結果 |
|noImplicitThis| this 表逹式的值不能為 any 型態 |
|noUnusedParameters| 要求函式中所有參數都必須有用到 |
|outDir| 指定編譯生成的 JavaScript 檔案的輸出目錄 |
|paths| 若模組位於 baseUrl 以外的位置，可用 path 定義其路徑 |
|outFile| 將輸出文件合併成一個檔案　|
|resolveJsonModule| 若專案會使用 JSON，則此設定值必須設定為 true |
|rootDir| tsc 編譯器找尋程式的目錄。若要編譯 TypeScript 檔案位於多個目錄內，可用 rootDir 將它們組織成單一一個虛擬目錄|
|skipLibCheck| 跳過宣告定義檔的檢查，以加快編譯速度 |
|sourceMap| 決定編譯器是否要生成除錯用的 source map 檔案 |
|strict| 啟用 TypeScript 的所有相關嚴格模式 |
|strictNullChecks| 把 null 和 undefined 視為獨立型別， 並禁止你將它們賦值給其他型別的變數 |
|suppressExcessPropertyErrors| 在試圖使用不存在的物件屬性時不回報錯誤。用來與舊版 TypeScript 相容 |
|target| 指定要以那個 JavaScript 版本為編譯目標 |
|traceResolution| 印出編譯器解析模組的詳細過程 |
|typeRoots| 限制編譯器尋找宣告檔案的位置 |
|types| 指定編譯時要加入的宣告檔 |
|removeComments| 編譯後刪除所有註解 |

## module 參數可指定的值

|名稱|說明|
|-----------|-----------------------------------------------------------------------------|
| none | 關閉模組功能 |
| commonjs | 指定使用 CommonJS 模組格式，在設為 es3 或 es5 時會預設啟用。 也是 Node.js 環境預設害援的規範 |
| amd | 指定使用 AMD (Asynchronous Module Definition, 非同步模組)，是 RequireJS模組載入工具支援的規範 |
| system | 指定使用 SystemJS 模組載入工具支援的模組規範  |
| umd | 指定使用 UMD (Universal Module Definition, 通用模組定義) 規範   |
| es2015/es6 | 指定使用 ES2015 的 ECMAScript 模組規範 |
| es2020 | 指定使用 ES2020 的 ECMAScript 模組規範 |
| esnext | 指定使用下一版 JavaScript 的模組規範  |


## compilerOption中 lib 屬性選項（設定編譯時要加入的函式庫）

|名稱|說明|
|-----------|-----------------------------------------------------------------------------|
| es5, es2015, es2016, 2017... | 引入這幾個值所對應之版本的定義。舊的命名法同樣可用，所以 es6 亦可寫成 es2015 |
| esnext | 引入 JavaScript 預定新增、但尚未正式採用的新功能，其實際內容將隨時間而改變 |
| dom | 引入 DOM 文件物件模型的相關定義， console 物件也是定義在這裡。網頁應用程式需要依賴它們來操偏瀏覧器內的 HTML 元素內容。|
| dom.iterable | 引入 DOM API 的額外相關定義，讓應用程式能走訪 HTML 元素 |
| scriptHost | 引入 Windowss Script Host 的相關定義，以便在 Windows 系統自動執行程式 |
| webworker | 引入 web worker 的相關定義，讓網頁應用程式得以執行背景工作 |


## 檢查編譯器能存取的檔案

`$ npx tsc --listFiles`

## NPM 版本格式
|名稱|說明|
|-----------|-----------------------------------------------------------------------------|
| 3.8.1 | 只接受這個特定版本 |
| * | 星號表示接受任何版本 |
| >3.8.1 >=3.8.1 | 任何大於、或大於等於此版本的套件均可使用 |
| <3.8.1 <=3.8.1 | 任何小於、或小於等於此版本的套件均可使用 |
| ~3.8.1 | 加入 ~ 表示修訂版本號以外的版本編號相同即可 |
| ^3.8.1 | 加入 ^ 表示主版本號相同即可，次版本號或修訂版本號不同也可以接受 |

版本編號 3.8.1
- major version number: 主版本編號 3
- minor version number: 次版本編號 8
- revision version number: 修訂版本編號 1