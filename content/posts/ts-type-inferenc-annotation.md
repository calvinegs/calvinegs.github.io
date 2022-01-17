---
title: "型別推論(type inference) 與 型別註記(type annotation)"
date: 2022-01-17
draft: false
description: "筆記 TypeScript 中所謂的 '型別推論'(type inference) 與 '型別註記'(type annotation)"
tags: ["type inference", "type annotation"]
categories: ["typescript"]
---

在此要討論的是有關 TypeScript 的 型別推論(type inference) 與 型別註記(type annotation)

## 關於 Javascript 的型別

Javascript 是個動態型別的語言，也就是說擁有 **'型別'** 的是 **'值'** 而不是變數。

```javascript
let myVar;
console.log(`${myVar}: ${typeof myVar}`); // undefined : undefined
myVar = 20;
console.log(`${myVar}: ${typeof myVar}`); // 20 : number
myVar = "Hi";
console.log(`${myVar}: ${typeof myVar}`); // Hi : string
myVar = true;
console.log(`${myVar}: ${typeof myVar}`); // true : boolean
```

## Type Annotation (型別註記)

TypeScript 提供靜型別功能，讓我們可以明確指定變數的型別。編譯器在轉譯過程即可偵測到使用不同型別時主動抛出錯誤訊息。下面的例子就是使用型別註記來定義靜態型別:

```typescript
function calculateTax(amount: number): number {
  return amount * 1.2;
}

console.log(`${20}: ${calculateTax(20)}`); //20: 24
console.log(`${"Hello"}: ${calculateTax("Hello")}`); //error
console.log(`${true}: ${calculateTax(true)}`); //error
```

## Type inference (型別推論)

TypeScript 編譯器可以根據變數宣告時給定的值來推論它的型別。

```typescript
function calculateTax(amount: number): number {
  return amount * 1.2;
}

let price = 100; //未給定資料型別，由給定的值推論此時的 price 變數型別為 number
let taxAmount = calculateTax(price); //未給定資料型別，由回傳值推論此時的變數型別為 number，因為 calculateTax 型別註記為 number
let halfShare = taxAmount / 2; //未給定資料型別，由計算結果值推論此時的變數型別為 number
console.log(`Full amount in tax: ${taxAmount}`); //Full amount in tax: 120
console.log(`Half share: ${halfShare}`); //Half share: 60
```

## 調整 tsc 編輯參數來查看編譯器所採用的型別

tsc 編譯器推論的型別若與預期的有出入，可以在 tscconfig.json "compilerOptions"中加入一條"declaration": true 的設定值

```jason
{
    "compilerOptions": {
        "target": "es2020",
        "outDir": "./dist",
        "rootDir": "./src",
        "declaration": true
    }
}
```

上述範例改成:

```typescript
function calculateTax(amount: number) {
  return (amount * 1.2).toFixed(2);
}

let price = 100; //未給定資料型別，由給定的值推論此時的 price 變數型別為 number
let taxAmount = calculateTax(price); //未給定資料型別，由回傳值推論此時的變數型別為 string，因為 calculateTax function toFixed(2) 結果是 string
let halfShare = taxAmount / 2; // 產生錯誤
console.log(`Full amount in tax: ${taxAmount}`); //Full amount in tax: 120
console.log(`Half share: ${halfShare}`); //Half share: 60
```

由於 toFixed(2) 回傳的是 string，導致 taxAmount 變數型別被推論成 string 而產生錯誤警告。
![image](https://user-images.githubusercontent.com/21993717/149703622-fc701a45-5a1e-40c9-b59e-4661be593abb.png)

tsc 因為在 tscconfig.json 多加入了編輯參數 "declaration": true，這個參數告訴編譯器，除了輸出程式碼轉譯外，還要輸芔包含型別宣告資訊的 .d.ts 檔，所以會在 build 目錄中產生一個 index.d.js，內容如下圖，可以看到 taxAmount 被推論成 string

```json
declare function calculateTax(amount: number): string;
declare let price: number;
declare let taxAmount: string;
declare let halfShare: number;
```

## 關於 **Any** 的型別推論

將範例改成如下圖，calculateTax 回傳值及傳入參數皆註記成為 any

```typescript
function calculateTax(amount: any): any {
  return (amount * 1.2).toFixed(2);
}

let price = 100; //未給定資料型別，由給定的值推論此時的 price 變數型別為 number
let taxAmount = calculateTax(price); //由於 calcaulateTax 型別註記為 any，故被推論成 any
let halfShare = taxAmount / 2; // 未給定資料型別，(any / 2) 計算結果會被推論成 number
console.log(`Full amount in tax: ${taxAmount}`); //Full amount in tax: 120.00
console.log(`Half share: ${halfShare}`); //Half share: 60
```

結果發現在 index.d.js 檔中 taxAmount 也被推論成 any，且編譯結果正常。

```json
declare function calculateTax(amount: any): any;
declare let price: number;
declare let taxAmount: any;
declare let halfShare: number;
```

但這樣的使用 any 型別，你會發現這和寫 javascript 程式碼無任何差別，也就是說你讓程式自行承擔結東，而沒有用到 TypeScript 靜態型別的好處(編譯時期即可檢查出程式可能出問題的地方)。

我們來測試一下，將 calculateTax function 改成如下:

```typescript
function calculateTax(amount: any): any {
  return `$${(amount * 1.2).toFixed(2)}`;
}
```

再執行看看，發現編譯程式時正常，確在執行時期產生非預期結果

```json
Full amount in tax: $120.00
Half share: NaN
```

結論就是你應該儘可能的不要有 any 這樣的型別註記。

## Contextual Typing (依照背景來進行的型態推論)

TypeScript 使用變量的位置來推斷它們的類型，這就是所謂的 contextual typing

```typescript
document.addEventListener("click", function (event) {
  console.log(event.button); //
});
```

在這個例子中，TypeScript 因為 click 事件而知道 event 參數是 MouseEvent 的一個實例。

```typescript
document.addEventListener("scroll", function (event) {
  console.log(event.button); // compiler error
});
```

而在這個例子中，因為是 'scroll' 事件，所以參數中不能是 button，因此轉譯時會產生錯誤。

```script
window.onmousedown = function(mouseEvent) {
   console.log(mouseEvent.button);   //<- OK
   console.log(mouseEvent.kangaroo); //<- Error!
};
```

Typescript 型別檢查器使用 Window.onmousedown 函數的型別來推斷賦值右邊的函數運算式的型別。因此，它能夠推斷出 mouseEvent 參數的型別，它確實包含一個**按鈕**屬性，但不包含**袋鼠**屬性。
