---
title: "Hugo 中使用的 Markdown 語法介紹"
date: "2021-12-06"
description: "以範例來說明 Hugo 所支援的 Markdown 語法 "
tags: ["markdown", "hugo"]
categories: ["hugo"]
series: ["Hugo 功能介紹"]
aliases: ["migrate-from-jekyl"]
draft: false
ShowToc: true
TocOpen: false
---

Hugo 內容文件中使用基本 Markdown 語法，還額外支援由 Hugo 所提供的加強語法(也支援基本 HTML 元素)。 

## 標題 (Headings)

要建立標題，請在文字前添加井字符號 `#`(請注意井字符號後要加入一個空格)。使用的井字符號的數量應與標題級別相對應。例如，要建立三級標題 (`<h3>`)，請使用三個井字符號（例如，### My Header）。

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638799287333.png)

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638799328708.png)

## 段落 (Paragraphs)

一個 Markdown 段落是由一個或多個連續的文字行組成，它的前後要有一個以上的空行。

## 文字區塊引用 (Blockquotes)

在段落的第一行最前面加">"

#### 未註明出處的文字區塊引用 (Blockquote without attribution)

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638781433614.png)
> Tiam, ad mint andaepu dandae nostion secatur sequo quae.
> **Note** that you can use _Markdown syntax_ within a blockquote.

#### 註明出處的文字區塊引用 (Blockquote with attribution)

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638781500603.png)
> Don't communicate by sharing memory, share memory by communicating.
>
> — <cite>Rob Pike[^1]</cite>

[^1]: The above quote is excerpted from Rob Pike's [talk](https://www.youtube.com/watch?v=PAAkCSZUG1c) during Gopherfest, November 18, 2015.

## 斷行 (Line Breaks)

要建立換行符號 (`<br>`)，請以兩個或多個空格結束一行，然後鍵入 return鍵。

## 表格 (Tables)

表格功能並不是 Markdown 規格, 不過 Hugo 支援這個好用的功能.

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638782877970.png)

| Name  | Age |
| ----- | --- |
| Bob   | 27  |
| Alice | 23  |

#### 表格內支援內嵌 Markdown 語法

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638782923449.png)

| Italics   | Bold     | Code   |
| --------- | -------- | ------ |
| _italics_ | **bold** | `code` |


#### 表格內的對齊功能

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638782962956.png)

| Number | Next number | Previous number |
| :------ |:---: | ---: |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |

## 程式碼區塊 (Code Blocks)

#### 使用 '反引號' 的程式碼區塊

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638783017129.png)

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8" />
        <title>Example HTML5 Document</title>
    </head>
    <body>
        <p>Test</p>
    </body>
</html>
```

#### 使用 '四個空格' 的程式碼區塊

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638783058278.png)

    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>Example HTML5 Document</title>
    </head>
    <body>
      <p>Test</p>
    </body>
    </html>

#### 使用 Hugo 內部支援 highlight shortcode 的程式碼區塊

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638783269185.png)

{{< highlight html >}}

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Example HTML5 Document</title>
</head>
<body>
  <p>Test</p>
</body>
</html>
{{< /highlight >}}

#### 使用 Gist 的程式區塊

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638783951002.png)

`<script src="https://gist.github.com/spf13/7896402.js"></script>`

{{< gist spf13 7896402 >}}

## 清單

#### 有序清單

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638784545161.png)

1. First item
2. Second item
3. Third item

#### 無序清單

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638784580362.png)

-   List item
-   Another item
-   And another item

#### 巢狀清單

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638784616109.png)

-   Fruit
    -   Apple
    -   Orange
    -   Banana
-   Dairy
    -   Milk
    -   Cheese

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638784791650.png)

1. First item
2. Second item
3. Third item
    - Indented item
    - Indented item
4. Fourth item
#### 以數字開頭的無序清單

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638784480784.png)


#### 將'段落'加入清單中

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638785228842.png)

* This is the first list item.
* Here's the second list item.

    I need to add another paragraph below the second list item.

* And here's the third list item.

#### 將'文字區塊引用'加入清單中

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638785268821.png)

* This is the first list item.
* Here's the second list item.

    > A blockquote would look great below the second list item.

* And here's the third list item.

#### 將'程式區塊'加入清單中

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638785296437.png)

1. Open the file.
2. Find the following code block on line 21:

        <html>
          <head>
            <title>Test</title>
          </head>

3. Update the title to match the name of your website.

#### 將'圖檔'加入清單中

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638785331162.png)

1. Open the file containing the Linux mascot.
2. Marvel at its beauty.

    ![Tux, the Linux mascot](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638785106824.png)

3. Close the file.

## Task List

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638799958147.png)

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638799849495.png)

## 反引號的使用 

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638786654747.png)

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638786709378.png)


## 水平線 (Horizontal Rules)

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638787170081.png)

Try to put a blank line before...

---

...and after a horizontal rule.

## 連結 (Links)

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638787310010.png)

My favorite search engine is [Duck Duck Go](https://duckduckgo.com).


![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638787425525.png)

I love supporting the **[EFF](https://eff.org)**.  
This is the *[Markdown Guide](https://www.markdownguide.org)*.  
See the section on [`code`](#code).

## 斜體 & 強調
使用 * 符號套用在文字的前後方，即可將文字改為斜體字；而使用連續兩個*加在文字的前後方則會是強調文字。

有時候，你得*停一下*腳步，等一等 **心靈**，讓心情平和，想一想自己生活中擁有的所有 **美好** 的東西。

## 使用跳脫字元

要顯示原本用於格式化 Markdown 文件中的文字字元，請在該字符前添加反斜線 (\)。

## 其他元素的使用  — abbr, sub, sup, kbd, mark

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Mon_Dec_06_2021_1638786778889.png)


<abbr title="Graphics Interchange Format">GIF</abbr> is a bitmap image format.

H<sub>2</sub>O

X<sup>n</sup> + Y<sup>n</sup> = Z<sup>n</sup>

Press <kbd><kbd>CTRL</kbd>+<kbd>ALT</kbd>+<kbd>Delete</kbd></kbd> to end the session.

Most <mark>salamanders</mark> are nocturnal, and hunt for insects, worms, and other small creatures.
