---
title: "Angular 元件樣式 及一些特殊的選擇器"
date: 2023-10-12
description: "本篇筆記是來記錄 Angular 元件樣式及其原理和 探討　Angular 提供的一些特殊選擇器的用法"
tags: ["angular", "component styles", "selector"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular-component-styles.git)*

希望透過了解預設的 Angular 樣式機制（模擬封裝）在底層的工作原理來幫助我們更清楚的如何使用　Angular 所提供的一些特殊選擇器(如： :host、:host-context、::ng-deep等)的用法。

在下面的實作過程將會使用到下列技術:

- Angular cli 16
- Angular 16
    - Router
    - Standalone component
- CSS & SCSS
- 瀏覧器的開發者工具



# CSS 的樣式污染

css 沒有作用域的概念，這就導致 css 在書寫的時候都是全局作用域。造成開發者在撰寫過程中會造成因 classname 相同而產生樣式污染及　css　規則層疊(CSS Cascade)。 隨著 SPA 應用的興起，全部的 css 會在同一個環境中同時進行加載，相互影響的問題更加嚴峻。


# Angular 元件樣式原理

Angular 以元件組合為基本原理來建構一支 web application，在 Angular 元件中預設採用的“檢視封裝”(View encapsulation)是“ViewEncapsulation.Emulated” (模擬封裝)。在這個設定值下 Angular 會修改元件的 CSS 選擇器，使它們只應用於元件的檢視，不影響應用程式中的其他元素(模擬 Shadow DOM 行為)。

## 建立 Angular Web Application

使用 npm init 指令建立一支 web application:

```bash
$ npm init @angular styles -- --routing --style scss
```

修改 app.component.html，將預設內容全數刪除，加入以下 button tag:

```html
<button class="red-button">按鈕</button>
```

在 app.compnent.scss 中加入以下內容：

```scss
.red-button{
  background-color: red;
}
```

在終端機中使用 `npm start` 啟動程式，打啟 chrome，並在 url 輸入 http://localhost:4200 開啟網頁。

![image](https://user-images.githubusercontent.com/21993717/274478987-0413ae18-1dce-478e-bda8-fce431c1de03.png)


## 了解 Angular 如何實作視圖封裝(view encapsulation)

在瀏覽器中按下 F12 快速鍵開啟 chrome 開發者工具

![image](https://user-images.githubusercontent.com/21993717/274479799-27fade0d-8db7-4edf-88d0-58480dacca80.png)

在開發工具的 "Elements" tab 中可以查看到整個網頁的內容，包含 HTML & CSS 。

在 HTML source code 中，可發現經由 Angular “處理後”的內容有不同。
在對應的 element 上都額外産生了一個“屬性“，在這個屬性名稱上是一個以自動産生的唯一值(數字)為結尾。

如：
- _nghost-ng-c1763460945 (app-root element)
- _ngcontent-ng-c1763460945 (button element)

```html
<app-root _nghost-ng-c1763460945="" ng-version="16.2.9">
    <button _ngcontent-ng-c1763460945="" class="red-button">按鈕</button>
</app-root>
```

比對原始的 index.html 內容：

![image](https://user-images.githubusercontent.com/21993717/274480846-e902e7c0-75e8-4834-8152-3acfc1a66f4d.png)

原始的 app.component.html 內容：

![image](https://user-images.githubusercontent.com/21993717/274481272-458323df-15fc-4f3e-8feb-dc22646520e7.png)

相同的原理也出現在 css 檔案中。

在原始的 app.component.scss 中，css 的內容是：
```
.red-button{
  background-color: red;
}
```
但在瀏覽器中的 css:
```css
.red-button[_ngcontent-ng-c1763460945] {
    background-color: red;
}
```

這兩個特殊屬性實現某種樣式的隔離作法。

這個_ngcontent-ng-c1763460945屬性對於red-button模板的元素是唯一的，因此樣式的範圍僅限於這些元素。

這就是 Angular 預設視圖封裝機制的工作原理！

這種機制非常有用，因為它使我們能夠編寫不易破壞的簡單樣式，但我們可能有時會想選擇性地打破這種隔離。

## 視圖封裝的設定

在 Angular 元件中可透過 ＠Component 指令中的 metadata 值設定來改變支援不同元件樣式封裝方式。encapsulation 這個 metadata有三個選項值，分別是：
- ViewEncapsulation.Emulated: 這是預設值，採用的封裝策略是 Angular 所提供的方式(即前面章節所描述的方式)
- ViewEncapsulation.None: 沒有提供樣式封裝。
- ViewEncapsulation.ShadowDom: 使用瀏覽器原生的 Shadow DOM API 來封裝樣式。

在 app.components.ts 的 @Component matadata 中加入 encapsulation 的設定，值為”ViewEncapsulation.None“

```ts {linenos=table,hl_lines=[7]}
import { Component, ViewEncapsulation } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss'],
  encapsulation: ViewEncapsulation.None
})
export class AppComponent {
  title = 'styles';
}
```

重新執行程式後，再來查視産生的 html 及 css 代碼：

![image](https://user-images.githubusercontent.com/21993717/274788426-68f4b0d5-1cba-40bd-8d88-a07b2be36c20.png)

可以看到上述章節中提到的由 Angular 所提供的 CSS 隔離的方案都不見了。在這個策略中所有的 CSS 樣式設定值都是“全局有效”(global available)的。

# 特殊的選擇器及其功能

## 建立另一支 Angular Web application

在探討 Angular 特殊的選擇器功能之前，先再建立一支新 Application。

```bash
$ npm init @angular component-styles -- --style scss --routing=no

$ npx ng g c pages/child-first
$ npx ng g c pages/child-second
```

將 app.component.html 內容修改成：

```html
<p>我是應用程式根元件中的 P 標籤。</p>
<app-child-first></app-child-first>
```

將 child-first.component.html 內容修改成：

```html
<div class="childFirstContainer">
  <span>我是第一子元件中的 Span 標籤。</span>
  <app-child-second></app-child-second>
</div>
```

child-first.component.scss 內容如下：

```scss
.childFirstContainer{
  border: 2px solid blue;
  padding: 10px;
}
:host {
  border: 2px solid green;
  color:chocolate;
  display: block;
  padding: 10px;
}
:host h2 {
  font-style: italic;
}
:host .iAmH4 {
  border: 2px dotted blue;
}
:host [id='1stH6'] {
  border: 2px dotted violet;
}

```

將 child-second.component.html 內容修改成：
```html
<div class="childSecondContainer">
  <span>我是第二子元件中的 Span 標籤。</span>
  <h2>第二子元件中的 H2 標籤。</h2>
  <h4 class="iAmH4">第二子元件中帶有 class 的 H4 標籤。</h4>
  <h6 id="1stH6">第二子元件中帶有 id 的 H6 標籤。</h6>
</div>
```

暫時假設 child-second.component.scss 沒有內容。

執行的結果：
![image](https://user-images.githubusercontent.com/21993717/274757995-88513348-ceae-4e03-8dcc-7deaa2ea78fb.png)


### 實際執行的程式碼

開啟瀏覽器的開發者工具

![image](https://user-images.githubusercontent.com/21993717/275296776-b2b9f940-89e3-4193-8b4c-9772d104e19d.png)


- 正如您所看到的，每個元件都有一個以 _nghost 開頭的唯一屬性。
- 每個元件中的每個元素都套用了一個以 _ngcontent 開頭的屬性。
- 同一個元件下的所有元素都有相同的 _ngcontent 屬性，因此因此元件的 css 檔案中定義的所有樣式將僅限於該元件的元素或範圍。

這就是 Angular 如何使用這兩個屬性來實作元件中的樣式封裝。

## 宿主元素

對 child first component 而言， " app-child-first" 這個 tag 就是宿主元素。

![image](https://user-images.githubusercontent.com/21993717/274756384-11f76c2e-3ff8-4905-8c60-1bf5ca5bc707.png)


## :host 選擇器

在 Angular 中 :host 這個特殊的選擇器就是用來選定“宿主元素”的，搭配在 child-first.component.scss 中的樣式設定：

![image](https://user-images.githubusercontent.com/21993717/274758656-16c26da3-6d5f-48ff-b671-2bbac9565a16.png)

呈現出來的效果如下：

![image](https://user-images.githubusercontent.com/21993717/274756712-e5025ed2-1307-45a5-8bbd-420fb064cda7.png)

透過“_nghost-ng-c2623725029”這個特別的屬性，我們在 scss 中設定的 :host{...} 樣式設定，就能精準的套用在宿主元素(即 app-child-first 這個 selector 上)及其後代元素上，這樣可避免大部份的樣式污染問題。

### 番外篇 - css 的繼承問題探討

css 樣式的繼承基本原理，套用在父元素上的樣式會被繼承其後代元素上。

app-child-first 套用的樣式如下：

![image](https://user-images.githubusercontent.com/21993717/274814764-f9652525-5654-40d8-8e9b-d8ffb0a73e5c.png)


查看它的子元套發現也因繼承關係也套用了相同的樣式：

![image](https://user-images.githubusercontent.com/21993717/274815270-94584791-91f3-4aa2-92b5-673de8f74ed4.png)

不過，有個值得注意的地方，繼承下來的樣式，確只有 color 這有樣式是有作用的，其因原是某些樣式屬性是可繼承的，而有些則不可繼承。即使元件被封裝，可繼承屬性也可以從父元件傳遞到子元件。

父元件下的後代元素也都是相同的情況：

![image](https://user-images.githubusercontent.com/21993717/274837022-2f8a98ff-b5f5-4e59-a7e5-9422459a3db1.png)


要了解可繼承和不可繼承的屬性清單，可檢視以下鏈接，。*[Full Property table](https://www.w3.org/TR/CSS21/propidx.html?source=post_page-----c829098cf194--------------------------------)*


### :host h2 的作用對象

在　child-first.component.scss 內容如下，先把關注點放在 :host 還額外搭配其他選擇器的這些設定值上。首先來看看 :host h2 這個組合。在這個範例中，我們在 :host 之後加入了標籤名稱 h2 以選擇宿主元素 app-child-first 下的所有 h2 元素。

```scss  {linenos=table,hl_lines=[11-13]}
.childFirstContainer{
  border: 2px solid blue;
  padding: 10px;
}
:host {
  border: 2px solid green;
  color:chocolate;
  display: block;
  padding: 10px;
}
:host h2 {
  font-style: italic;
}
:host .iAmH4 {
  border: 2px dotted blue;
}
:host [id='1stH6'] {
  border: 2px dotted violet;
}
```

在執行的結果中並沒有發現有任何斜體字出現，儘管在第二子元件中有一個 h2 的標籤存在，但它依舊沒有套用到該樣式。

![image](https://user-images.githubusercontent.com/21993717/274757995-88513348-ceae-4e03-8dcc-7deaa2ea78fb.png)

修改一下第一子元件的 html，加入一個 h2 標籤:

```html {linenos=table,hl_lines=[2]}
<div class="childFirstContainer">
  <h2>我是第一子元件中的 H2 標籤。</h2>
  <span>我是第一子元件中的 Span 標籤。</span>
  <app-child-second></app-child-second>
</div>
```

再看看執行結果：

![image](https://user-images.githubusercontent.com/21993717/274859170-a611bfb5-cc54-4e9c-bd95-b4e053222f97.png)

我們看到新加到第一子元件的 H2 Tag 已呈現出套用斜體字樣式的內容了。

在 :host 之後新增了標籤名稱 h2 以選擇宿主元素 第一子元件 下的所有 h2 元素。

但只有 “第一子元件“ 的 h2 標記使用 font-style:italic 進行樣式設定。這是由於 Angular 為元件提供了預設的模擬封裝，所以”第二子元件中的 h2 標記不會受到影響。

一樣的道理，第二子元件 中有一個類別為 iAmH4 的 h4 標籤和 ID 為 1stH6 的 h6 標籤也因為相同原因:host .iAmH4 與 :host [id='1stH6'] 兩種樣子的設定根本沒有任何影響，這都是由於上述已討論過的預設“模擬封裝”造成的。

```css
:host .iAmH4 {
  border: 2px dotted blue;
}
:host [id='1stH6'] {
  border: 2px dotted violet;
}
```

## ::ng-deep 選擇器

如果你因為某些因素要繞過上述這種封裝， 那麼 ::ng-deep 選擇器就很有用。假設將上面的兩種樣式修改如下: (在 :host 選擇器後加入了 ::ng-deep 選擇器)

```css
:host ::ng-deep .iAmH4 {
  border: 2px dotted blue;
}
:host ::ng-deep [id=’1stH6'] {
  border: 2px dotted violet;
}
```

你會發現現在這二個樣子設定可以穿透“模擬封裝”的保護了。::ng-deep 非常強大，能夠將其下定義的樣式渲染為全域樣式。這裡要注意的是　::ng-deep 必須在 :host 選擇器之後使用，以確保定義的樣式僅適用於第一子元件及其後代元件。

![image](https://user-images.githubusercontent.com/21993717/274881888-4ddb705f-8a1b-42c7-a879-5753ee6301b7.png)


## :host-context 選擇器

修改一下“第一子元件”的範本(child-first.component.html)，在範本中新增另一個帶有邊框類別(即 .box)的第二子元件。

```html {linenos=table,hl_lines=[5]}
<div class="childFirstContainer">
  <h2>我是第一子元件中的 H2 標籤。</h2>
  <span>我是第一子元件中的 Span 標籤。</span>
  <app-child-second></app-child-second>
  <app-child-second class="box"></app-child-second>
</div>
```

這麼做的目的是為了示範如何對多次使用的相同宿主元素卻可以設定不同的樣式。除了上述修改“第一子元件”的範本外，同時在第二子元件的 scss 檔案添加一些樣式。為“第二子元件”的宿主元素添加紫色背景色，並為第二子元件的類別添加橙色背景色。

```css {linenos=table,hl_lines=[2,8]}
:host {
  border: 2px solid violet;
  display: block;
  padding: 10px;
  margin: 10px;
}
:host(.box) {
  border: 2px solid orange;
}
.childSecondContainer {
  border: 2px solid tomato;
}
```

在:host() 選擇器中，括號內給出的條件決定要設定哪個宿主元素的樣式。如您所見，:host(.box) 幫助我向app-child-second class=”box” 添加不同的背景顏色。同時，我們還添加了帶有 childSecondContainer 類別的 div 標籤， div標籤包著了 app-child-second 的所有內容，我們為它的邊框加入番茄色顏色。 目的是區分套用於 app-child-second 宿主元素的邊框和應用於 app-child-second 元件內容的邊框。

結果如下：

![image](https://user-images.githubusercontent.com/21993717/275297708-3e681ecf-2108-47be-b103-234cd4893fa1.png)

最後讓我們來看看 :host-context() 選擇器。宿主上下文(背景)的概念是根據 CSS 類別是否應用於宿主元素的任何祖先來設定宿主元素的樣式。

簡單的說這個選擇器：用於設置元件內部元素的樣式，但卻具體取決於元件外部設置的某些條件。

這有點不太好理解，讓我們來看例子吧。

使用 Angular cli 新加入一個元件：

```bash
$ npx ng g c pages/child-third
```

將這個"第三子元件“ ChildComponentThird 加入到 ChildComponentSecond 裡。

在 ChildComponentSecond 的範本 (child-second.component.html) 中加入 ChildComponentThird：

```html {linenos=table,hl_lines=[6]}
<div class="childSecondContainer">
  <p>我是第二子元件中的 P 標籤。</p>
  <h2>第二子元件中的 H2 標籤。</h2>
  <h4 class="iAmH4">第二子元件中帶有 class 的 H4 標籤。</h4>
  <h6 id="1stH6">第二子元件中帶有 id 的 H6 標籤。</h6>
  <app-child-third></app-child-third>
</div>
```

ChildComponentThird的範本 (child-third.component.html) 內容：

```html
<p>我是第三子元件中的 p 標籤。</p>
```

ChildComponentThird CSS (child-third.component.scss) 內容：

```scss
:host-context(app-child-second.box){
  color: red;
  border: 2px solid green;
  display: block;
  margin: 10px;
}
```

這表示如果 app-child-second 元素具有邊框類別(.box)，則 color:red 等樣式將套用至宿主元素 app-child-third 上。

如預期的那樣，邊框、邊距和顯示屬性僅套用在 app-child-third 宿主元素上。

![image](https://user-images.githubusercontent.com/21993717/275299101-fc30635d-49d7-4fbe-af5b-bf84d6fbed37.png)


沒有帶有"邊框類別(.box)" 的 app-child-second 元素:

![image](https://user-images.githubusercontent.com/21993717/275299205-780fd0b9-1f4f-4e68-b59d-ccd9c3a2b77f.png)

![image](https://user-images.githubusercontent.com/21993717/275299494-9dc0b154-386e-4448-872e-38b3cbd4659d.png)

帶有"邊框類別(.box)" 的 app-child-second 元素:

![image](https://user-images.githubusercontent.com/21993717/275299312-6ee9cedf-6c34-480e-bc70-9eab63dafa8e.png)

![image](https://user-images.githubusercontent.com/21993717/275299399-9f960504-ed41-4d51-8d1b-3ea505c9f7cd.png)


如前所示，由於繼承關係，只有 color 屬性套用於 app-child-third 下的 p 標記，因為 color 屬性是可繼承的。其他屬性均不可繼承。