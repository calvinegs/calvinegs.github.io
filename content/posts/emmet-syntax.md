---
title: "透過範例學習 Emmet 語法"
date: 2022-02-21
draft: false
description: "Emmet extension 是一個協助編寫 Html 或 CSS 語法的有力工具，透過範例來學習 Emmet 所支援的語法是最快上手的方式。"
tags: ["emmet", "html", "css"]
categories: ["html"]
---

網頁製作時，手刻 HTML/CSS 即傷神又容易出錯，讓我們來看看 Emmet 如何快速又便捷的協助我們進行 HTML/CSS 的輸入。

## Elements

輸入 div 按下 Enter 或 Tab 鍵，輸入 innerHtml - abc 再按下 Ctrl+Enter 鍵

```html
<div>abc</div>
```

輸入 p 按下 Enter 或 Tab 鍵，輸入 innerHtml -abc 再按下 Ctrl+Enter 鍵

```html
<p>abc</p>
```

## Nesting operators

### Child: >

div>ul>li

```html
<div>
  <ul>
    <li></li>
  </ul>
</div>
```

### Sibling: +

div+p+bq

```html
<div></div>
<p></p>
<blockquote></blockquote>
```

### Climb-up: ^

div+div>p>span+em  
div+div>p>span+em^bq  
div+div>p>span+em^^^bq

```html
<!-- div+div>p>span+em -->
<div></div>
<div>
  <p><span></span><em></em></p>
</div>
<!-- div+div>p>span+em^bq -->
<div></div>
<div>
  <p><span></span><em></em></p>
  <blockquote></blockquote>
</div>
<!-- div+div>p>span+em^^^bq -->
<div></div>
<div>
  <p><span></span><em></em></p>
</div>
<blockquote></blockquote>
```

### Multiplication: \*

ul>li\*5

```html
<ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
</ul>
```

### Grouping: ()

div>(header>ul>li\*2>a)+footer>p

```html
<div>
  <header>
    <ul>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
    </ul>
  </header>
  <footer>
    <p></p>
  </footer>
</div>
```

(div>dl>(dt+dd)\*3)+footer>p

```html
<div>
  <dl>
    <dt></dt>
    <dd></dd>
    <dt></dt>
    <dd></dd>
    <dt></dt>
    <dd></dd>
  </dl>
</div>
<footer>
  <p></p>
</footer>
```

## Attribute operators

abbr#anId.aClass[style="color:blue;" title="Hypertext Markup Language"]{HTML}

```html
<abbr
  title="Hypertext Markup Language"
  id="anId"
  class="aClass"
  style="color:blue;"
  >HTML</abbr
>
```

## ID and CLASS

div#header+div.page+div#footer.class1.class2.class3

```html
<div id="header"></div>
<div class="page"></div>
<div id="footer" class="class1 class2 class3"></div>
```

div.class1

```html
<div class="class1"></div>
```

(header>ul.nav>li\*5)+footer

```html
<header>
  <ul class="nav">
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
  </ul>
</header>
<footer></footer>
```

## Custome attributes

td[title="Hello world!" colspan=3]

```html
<td title="Hello world!" colspan="3"></td>
```

a:blank

```html
<a href="http://" target="_blank" rel="noopener noreferrer"></a>
```

## Item numbering: $

ul>li.item$\*5

```html
<ul>
  <li class="item1"></li>
  <li class="item2"></li>
  <li class="item3"></li>
  <li class="item4"></li>
  <li class="item5"></li>
</ul>
```

ul>li.item$$$\*5

```html
<ul>
  <li class="item001"></li>
  <li class="item002"></li>
  <li class="item003"></li>
  <li class="item004"></li>
  <li class="item005"></li>
</ul>
```

ul>li.item$@-\*5  
註: @-: 表示反向排序

```html
<ul>
  <li class="item5"></li>
  <li class="item4"></li>
  <li class="item3"></li>
  <li class="item2"></li>
  <li class="item1"></li>
</ul>
```

ul>li.item$@3\*5

```html
<ul>
  <li class="item3"></li>
  <li class="item4"></li>
  <li class="item5"></li>
  <li class="item6"></li>
  <li class="item7"></li>
</ul>
```

ul>li.item$@-3\*5

```html<ul>
    <li class="item7"></li>
    <li class="item6"></li>
    <li class="item5"></li>
    <li class="item4"></li>
    <li class="item3"></li>
</ul>
```

ul>li.item${Item $@3}\*5

```html
<ul>
  <li class="item1">Item 3</li>
  <li class="item2">Item 4</li>
  <li class="item3">Item 5</li>
  <li class="item4">Item 6</li>
  <li class="item5">Item 7</li>
</ul>
```

ul.my-list>(li.my-list-item#id-$$)\*6

```html
<ul class="my-list">
  <li class="my-list-item" id="id-01"></li>
  <li class="my-list-item" id="id-02"></li>
  <li class="my-list-item" id="id-03"></li>
  <li class="my-list-item" id="id-04"></li>
  <li class="my-list-item" id="id-05"></li>
  <li class="my-list-item" id="id-06"></li>
</ul>
```

## Text: {}

a{Click me}

```html
<!-- a{click}+b{here} -->
<a href="">click</a><b>here</b>

<!-- a>{click}+b{here} -->
<a href="">click<b>here</b></a>
```

```html
<!-- p>{Click }+a{here}+{ to continue} -->

<p>Click <a href="">here</a> to continue</p>
```

```html
<!-- p{Click }+a{here}+{ to continue} -->

<p>Click</p>
<a href="">here</a> to continue
```

## Implicit

.class1

```html
<div class="class1"></div>
```

em>.class1

```html
<em><span class="class1"></span></em>
```

ul>.class1

```html
<ul>
  <li class="class1"></li>
</ul>
```

table>.row>.col

```html
<table>
  <tr class="row">
    <td class="col"></td>
  </tr>
</table>
```

## Forms & Inputs

form:get

```html
<form action="" method="get"></form>
```

form:post

```html
<form action="" method="post"></form>
```

## Lorem lpsum

lorem  
lorem20  
ul.mylist>lorem4.item\*5

```html
<ul class="mylist">
  <li>Lorem ipsum dolor sit.</li>
  <li>Repellendus iusto debitis vel.</li>
  <li>Aperiam similique architecto odio!</li>
  <li>Exercitationem iusto nesciunt maxime.</li>
  <li>Minus reiciendis eaque nemo?</li>
</ul>
```

p\*4>lorem4

```html
<p>Lorem ipsum dolor sit.</p>
<p>Accusantium odit nostrum modi.</p>
<p>Consequatur soluta voluptatibus amet.</p>
<p>Molestiae quo a asperiores!</p>
```

## Advanced Structures

p>{Click }+a{here}+{ to continue}

```html
<p>Click <a href="">here</a> to continue</p>
```

## CSS Abbreviations

```css
body {
  /* c#000 */
  color: #000;
  /* c#f */
  color: #fff;
  /* c#f4 */
  color: #f4f4f4;

  /* p10 */
  padding: 10px; /* default pixel */
  /* p10% or p10p */
  padding: 10%;
  /* p10r */
  padding: 10rem;

  /* pr10 */
  padding-right: 10px;
  /* pl10 pt10 pb10 */
  padding-left: 10px;
  padding-top: 10px;
  padding-bottom: 10px;

  /* m20 ml20 mt20 mb20*/
  margin: 20px;
  margin-left: 20px;
  margin-top: 20px;
  margin-bottom: 2%;

  /* t10 b10 db di dib */
  top: 10px;
  bottom: 10px;
  display: block;
  display: inline;
  display: inline-block;

  /* w300 taj dn df*/
  width: 300px;
  text-align: justify;
  display: none;
  display: flex;

  /* ff ffa fft ffv*/
  font-family: serif; /* default font: serif */
  font-family: Arial, "Helvetica Neue", Helvetica, sans-serif;
  font-family: "Times New Roman", Times, Baskerville, Georgia, serif;
  font-family: Verdana, Geneva, sans-serif;

  /* fwb fw700  fsi fsn fso */
  font-weight: bold;
  font-weight: 700;
  font-style: italic;
  font-style: normal;
  font-style: oblique;

  /* fz2e */
  font-size: 2em;

  /* pr15+ml10 */
  padding-right: 15px;
  margin-left: 10px;
}
```

## Custom Snippets

先設定 Emmet Snippets 要存放的目錄 (如: f:\emmet )

![EmmetSetting](https://user-images.githubusercontent.com/21993717/129663354-be7f2d35-3c7a-42f3-a1b4-fbee8b53afe0.png)

在該目錄下建立一個名為 snippets.json 檔案，內容如下
```json
{
  "html": {
    "snippets": {
      "nl": "nav.main-nav>ul>li.item$*4",
      "ull": "ul>li[id=${1} class=${2}]*2"
    }
  },
  "css": {
    "snippets": {
      "cb": "color: black",
      "bsd": "border: 1px solid ${1:red}"
    }
  }
}
```

註:

1. ${1} 表示為第一個游標停駐的地方，可直接進行 id 輸入, 當按下 Tab 鍵後，游標會自動停駐在 ${2} 的地方，方便輸入 class name。
2. ${1:red} 其中的 red 表示是預設值。

存檔後重新啟動 VS Code

```html
<!-- 在 html 樘中輪入 nl 按下 Tab 鍵 -->
<nav class="main-nav">
  <ul>
    <li class="item1"></li>
    <li class="item2"></li>
    <li class="item3"></li>
    <li class="item4"></li>
  </ul>
</nav>

<!-- ull -->
<ul>
  <li id="" class=""></li>
  <li id="" class=""></li>
</ul>
```

```css
/* cb */
color: black;
/* bsd */
border: 1px solid red;
```
