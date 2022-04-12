---
title: "透過範例了解 Javascript Rest ＆ Spread 運算符有何不同"
date: 2022-04-12
draft: false
description: "JavaScript 使用三個點 (...) 來表示rest(其餘)運算符和Spread(擴展)運算符。但這兩個運算符並不相同。"
tags: ["rest", "Spread"]
categories: ["JavaScript"]
---

rest 和 spread 之間的主要區別在於，rest 運算符將使用者提供的特定的值的“其餘”部分放入 JavaScript 陣列中。但是擴展語法將可疊代物件擴展為各別的元素。

## rest 的範例
```js {hl_lines=[2],linenostart=1}
// 將使用者提供的某些值放入陣列中
function myBio(firstName, lastName, ...otherInfo) { 
  return otherInfo;
}

// 呼叫 myBio 功能時傳入五個參數值:
myBio("Oluwatobi", "Sofela", "CodeSweetly", "Web Developer", "Male");

// 回傳以下陣列:
["CodeSweetly", "Web Developer", "Male"]
```

```js {hl_lines=[2]}
// 宣告二個一般變數及一個 rest 變數，再使用 destructruing 方式給值:
const [firstName, lastName, ...otherInfo] = [
  "Oluwatobi", "Sofela", "CodeSweetly", "Web Developer", "Male"
];

console.log(otherInfo); 

// 結果:
["CodeSweetly", "Web Developer", "Male"]
```

```js
// ES7+的物件屬性其餘運算符
const {a, b, ...rest} = {a:1, b:2, c:3, d:4} //a=1, b=2, rest={c:3, d:4}

let options = {
  title: "Menu",
  height: 200,
  width: 100
};

// title = property named title
// rest = object with the rest of properties
let {title, ...rest} = options;

// now title="Menu", rest={height: 200, width: 100}
alert(rest.height);  // 200
alert(rest.width);   // 100
```

```js
function sum(...numbers) {
    let result = 0
    result = numbers.reduce((orgValue, value) => orgValue += value)
    return result
}
  
console.log(sum(1)); // 1
console.log(sum(1, 2, 3, 4, 5)); // 15
```

## spread 的範例
```js{hl_lines=[7],linenostart=1}
// 定義一個有三個參數傳入值的功能
function myBio(firstName, lastName, company) { 
  return `${firstName} ${lastName} runs ${company}`;
}

// 使用擴展運算符將陣列中的值展開至各別的參數中:
myBio(...["Oluwatobi", "Sofela", "CodeSweetly"]);

// 上述程式的結果:
“Oluwatobi Sofela runs CodeSweetly”
```

```js{hl_lines=[4],linenostart=1}
let first = [1,3,5];
let second = [2,4,6];

second = [ ...first, ...second];

console.log("second : ", second);   // second [ 1, 3, 5, 2, 4, 6 ]
```

```js
const myName = "Oluwatobi Sofela";

console.log([...myName]);

// The invocation above will return:
[ "O", "l", "u", "w", "a", "t", "o", "b", "i", " ", "S", "o", "f", "e", "l", "a" ]
```

```js
const numbers = [1, 3, 5, 7, 10, 200, 90, 59];

function addNumbers(a, b, c, d) {
  return a + b + c + d;
}

console.log(addNumbers(...numbers));
// The invocation above will return: 16
```

```js
// 將擴展運算符運用在物件上
const myNames = ["Oluwatobi", "Sofela"];
const bio = { ...myNames, runs: "codesweetly.com" };

console.log(bio);
// The invocation above will return: { 0: "Oluwatobi", 1: "Sofela", runs: "codesweetly.com" }
```

```js {hl_lines=[8]}
// 相同屬性會被覆蓋
const myName = { firstName: "Tobi", lastName: "Sofela" };
const bio = { ...myName, firstName: "Oluwatobi", website: "codesweetly.com" };

console.log(bio);

// The invocation above will return:
{ firstName: "Oluwatobi", lastName: "Sofela", website: "codesweetly.com" };
```

```js {hl_lines=[8,10]}
// myName 陣列的值都是 Primitive value, 所以 myName 陣列的變化不會反應到 aboutMe 陣列中
const myName = ["Sofela", "is", "my"];
const aboutMe = ["Oluwatobi", ...myName, "name."];

console.log(aboutMe);
// The invocation above will return: ["Oluwatobi", "Sofela", "is", "my", "name."]

myName.push("real");

console.log(myName); // ["Sofela", "is", "my", "real"]
console.log(aboutMe); // ["Oluwatobi", "Sofela", "is", "my", "name."]
```

```js {hl_lines=[8]}
// myName 陣列的值包含了 non-primitive value, 所以 myName 陣列的變化會影響 aboutMe 陣列
const myName = [["Sofela", "is", "my"]];
const aboutMe = ["Oluwatobi", ...myName, "name."];

console.log(aboutMe);
// The invocation above will return: [ "Oluwatobi", ["Sofela", "is", "my"], "name." ]

myName[0].push("real");

console.log(myName); // [["Sofela", "is", "my", "real"]]
console.log(aboutMe); // ["Oluwatobi", ["Sofela", "is", "my", "real"], "name."]
```