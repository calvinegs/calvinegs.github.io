---
title: "透過範例了解 Javascript Destructuring (解構指定值)"
date: 2022-04-11
draft: false
description: "透過 解構 語法可以方便的用來擷取陣列或物件中的資料"
tags: ["destructuring"]
categories: ["JavaScript"]
---

## 物件解構
### 指定值給已存在的變數
``` js
const superHero = {
	name : "Spiderman",
	power : "Spider Sense",
	color : "red"
};

const { name, power, color } = superHero;
console.log("name:", name);     // Spiderman
console.log("color:", color);   // red
console.log("power:", power);   // Spider Sense
```

### 重新指定值的用法
```js
const superMan = {
	name : "Superman",
	power : "Super Sense",
	color : "blue"
};

({ name, power, color } = superMan);
console.log("name:", name);
console.log("color:", color);
console.log("power:", power);
```

### 指定值給新的變數
``` js {hl_lines=[7],linenostart=1}
const superHero = {
	name : "Spiderman",
	power : "Spider Sense",
	color : "red"
};

const { name : n, power : p, color : c } = superHero;
console.log("color:", c);   // red
console.log("power:", p);   // Spider Sense
console.log("name:", n);    // Spiderman
```
### 預設值
```js
const person = {
  name: 'Skay',
  age: 38
};

// Assign default value of Canada to country if undefined
const { name, age, country = 'Canada' } = person;
console.log(`I am ${name} from ${country} and I am ${age} years old.`);
// Output -> I am Skay from Canada and I am 38 years old.'
```

### 解構回傳值
```js
const data = () => ({ name: 'George', role: 'SA' });
const { name, role } = data();
```

### 槽狀的解構
``` js {hl_lines=[13],linenostart=1}
const superHero = {
	name : "Spiderman",
	power : "Spider Sense",
	"character" : {
		"realName" : "Tom Holland",
		"citizenship" : "British",
		"appearances" : {
			"movie" : "captain america civil war",
			"releaseDate" : "6 May 2016"
		}
	}
};
const { character } = superHero;
console.log(character);
```
```bash
{
  realName: 'Tom Holland',
  citizenship: 'British',
  appearances: { movie: 'captain america civil war', releaseDate: '6 May 2016' }
}
```
``` js {hl_lines=[13],linenostart=1}
const superHero = {
	name : "Spiderman",
	power : "Spider Sense",
	"character" : {
		"realName" : "Tom Holland",
		"citizenship" : "British",
		"appearances" : {
			"movie" : "captain america civil war",
			"releaseDate" : "6 May 2016"
		}
	}
};
const { character : {appearances : { movie }} } = superHero;
console.log(movie);
```
```bash
captain america civil war
```

## 陣列解構
``` js {hl_lines=["4-7"],linenostart=1}
// we have an array with the name and surname
let arr = ["John", "Smith"]

// destructuring assignment
// sets firstName = arr[0]
// and surname = arr[1]
let [firstName, surname] = arr;

console.log(firstName); // John
console.log(surname);   // Smith
```

``` js {hl_lines=[1],linenostart=1}
let [firstName, surname] = "John Smith".split(' ');
alert(firstName);   // John
alert(surname);     // Smith
```

```js
// 變數值互換
const a = 1, b = 2;
[b, a] = [a, b]     //a=2, b=1
```

```js
const [a, [b, [c, d]]] = [1, [2, [[[3, 4], 5], 6]]];
console.log(c);     //[ [ 3, 4 ], 5 ]
```
### 乎略某些值
```js
// second element is not needed
let [firstName, , title] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
console.log(title);     // Consul
```

### 支援任何可疊代的值
```js
let [a, b, c] = "abc"; // ["a", "b", "c"]
let [one, two, three] = new Set([1, 2, 3]);
```

### 預設值
```js
let [a, b, c] = "abc"; // ["a", "b", "c"]
let [one, two, three] = new Set([1, 2, 3]);
```