---
title: "國民健康訪問調整網站維護說明"
date: 2022-06-15
description: "在本說明文件中會介紹如使用 VSCode 來維護網站中的各網頁內容及如何更新至伺服器。"
draft: false
ShowToc: true
---

## 安裝工具
### VSCode
在 windows 10 下安裝 VSCode，首先先下載安裝程式 （https://code.visualstudio.com/Download)，依照你的電腦選擇要安裝的版本，若是比較新的PC大都為 64 bit，可點選 for Windows 8，10，11 的 User Installer 64 位元使用。

![image](https://user-images.githubusercontent.com/21993717/175248304-8c1a6fb0-4ee9-44c1-8972-8ca2d9c349fc.png)

下載完成點選“開啟檔案”後即進行安裝。

![image](https://user-images.githubusercontent.com/21993717/175250466-aeafcf24-8082-49e5-84b6-98ec9b67ff77.png)

點選“我同意”選項後按下“下一步“

![image](https://user-images.githubusercontent.com/21993717/175250601-560c025c-fd08-4ba1-a66b-fcad887afc3f.png)

選擇安裝路徑後按下“下一步”

![image](https://user-images.githubusercontent.com/21993717/175250827-c3e0e7e5-9253-49a7-81c1-588f815e9847.png)

“下一步” => “下一步” => “下一步” => "安裝“

![2022-06-21 11-48-36](https://user-images.githubusercontent.com/21993717/175261732-2ad9e2e0-baea-48bb-a733-d4e8a999f51c.png)

完成

![2022-06-21 11-49-03](https://user-images.githubusercontent.com/21993717/175261847-e4483eb5-018c-47ec-be3e-69016a9037c6.png)

![2022-06-21 11-50-01](https://user-images.githubusercontent.com/21993717/175262000-6f5d086c-ebcc-4202-bb06-19deba70c268.png)

更多資訊可參考 https://code.visualstudiocom/docs/?dv=win64user

![2022-06-21 11-50-41](https://user-images.githubusercontent.com/21993717/175262513-5662737c-1eea-4cb3-841a-2a9cf3c13488.png)

### Live Server Extension for VSCode
在 VSCode 中 Ctrl+Shift+X 打開 Extensions 功能

![image](https://user-images.githubusercontent.com/21993717/175263357-892b5891-2d55-47ea-8202-7ddf463b7799.png)

在搜尋框內輸入 live server，找到 Live Server Extension，按下“Install"

![image](https://user-images.githubusercontent.com/21993717/175264059-5b59cf4e-650c-41fe-a928-7b73ac21dce3.png)

安裝好 Live Server Extension 後在右下角會出現 “Go Live" 的功能按鈕

![image](https://user-images.githubusercontent.com/21993717/175264059-5b59cf4e-650c-41fe-a928-7b73ac21dce3.png)

### git for windows
git 是目前最火紅的 “程式碼版本控制管理系統”，用這個工具可以協助你管理你的網頁程式。

git for windows 安裝程式下載 https://git-scm.com/downloads

![image](https://user-images.githubusercontent.com/21993717/175267461-dfa72690-3638-4c7f-8d88-11942838ba56.png)

依你的作業系統選擇合適版本，下載後“開啟檔案” 進行安裝。

![image](https://user-images.githubusercontent.com/21993717/175267671-77cde4a3-4242-4357-8034-e49e666ad5c0.png)

選擇“是”進行安裝

![2022-06-23 17-35-37](https://user-images.githubusercontent.com/21993717/175268997-7710fe49-0f8c-4850-8441-9f46d9c96149.png)

依序選擇預計值“Next”進行安裝

![2022-06-23 17-35-48](https://user-images.githubusercontent.com/21993717/175269295-f6ebd0a0-a2ae-4216-91d4-27ff0bdfb7f4.png)

安裝完成!

![2022-06-23 17-37-46](https://user-images.githubusercontent.com/21993717/175269633-18951f51-b719-43b5-b7c7-ab904004029b.png)


測試 git 功能，先打開“命令提示字元”輸入`git version`
```bash
C:\git version

git version 2.36.1.windows.1
```

在使用 git 前要先設定`初次設定`，包含 user name 及 email
```bash
c:\> git config --global user.name "Tim Doe"
c:\> git config --global user.email timdoe@example.com
```


測試 git 功能，打開“命令提示字元”，先輸入`E:` + Enter，再輸入 `cd \Sources\Nhis2WebNew` ，這個目錄是網頁程式碼所在的位置。

輸入 git log 指令會顯示目前目錄下的程式碼的版本歴程

![image](https://user-images.githubusercontent.com/21993717/175270144-62a4ca9c-2dcd-4027-9abb-45deffceb30f.png)

至此相關的工具已安裝完成！

## 使用的技術

- VSCode
- HTML Tag 
    - headings (標題)
    - paragraphs (段落)
    - a tag (連結)
    - images (影像檔)
    - buttons (按鈕)
    - lists (清單)
    - div (區塊)
    - id
    - comments (註解)
    - br (換行)
- JavsScript
- CSS
    - Classes
- git


## 網頁程式架構
### 程式檔案結構

```
./NHIS2WebNew
├── bak/
│   └── *.html        # 備份檔
├── css/
│   ├── base.css
│   ├── extend.css
│   └── table.css
├── files/
│   └── *.pdf, *.zip
├── img/
│   └── *.jpg, *.gif  # 網頁使用的影像檔
├── javascript/       # JavaScript 程式碼
│   ├── imgshow.js
│   ├── news_e.js
│   └── news.js
├── templates/
│   └── *.txt
└── HTML files        # 網頁程式檔
```

### 網頁程式碼基本架構

基本上網頁程式碼架構是相似的，以 index.html 和 nhri.html 兩支程式來比較，會發現他們都是由 “head" & "cont" & "功能選單“ & "foot" 以及最下方的“來訪人數和版權宣告”區。

![image](https://user-images.githubusercontent.com/21993717/175281097-c4b118a4-f5be-4454-8ca7-d37b1c337ee0.png)

![image](https://user-images.githubusercontent.com/21993717/175281653-da7eeb90-5a76-4b3a-85f9-81bdf9d1cddb.png)

網頁呈現在瀏覽器上的結果如下圖，上述的五個區塊分別對應到
1. 最上方的紅色區塊 對應的是: "head"
2. 中間左方的紅色區塊 對應的是: "功能選單"
3. 中間右方的紅色區塊 對應的是: "cont" （內文區塊）
4. 下方的倒數第二個紅色區塊 對應的是: "foot"
5. 最下方的紅色區塊 對應的是: "來訪人數和版權宣告"

![image](https://user-images.githubusercontent.com/21993717/175757280-a7917225-df27-43db-ad18-bf563589cc82.png)


由上面的說明可以了解每一個網頁中的五個區塊除了“3.cont"(內文)這個區塊外都是共用的，換句話，內文區塊是每一個網頁的主角，這個區塊內容就是這個網頁的最重要內函，其他四個區塊每個網頁都是一樣的。



## HTML 基本語法的使用
### 使用 VSCode　編輯網頁

啟動 VSCode

![image](https://user-images.githubusercontent.com/21993717/175278234-44966d17-f172-4c9c-9cbc-131ac1ca4bfa.png)

點選 “Open Folder“ 按鈕

![image](https://user-images.githubusercontent.com/21993717/175278492-3d94d6ff-ab5b-4193-a43c-92c9847c53bc.png)

選擇 Source 所在的目錄 `E:\Sources\Learning\` 按下 `選擇資料夾` 按鍵

![2022-06-25 16-56-59](https://user-images.githubusercontent.com/21993717/175768016-7c6d5f30-5cd9-44ec-9f53-5c2ef118af9f.png)

第一次開啟一個目錄時須要使用者的授權，請按“Yes"

![2022-06-25 16-58-31](https://user-images.githubusercontent.com/21993717/175768051-2d234153-6b66-4c46-8943-ce7e1f6c0c21.png)

目錄開啟成功後，使用滑鼠點選 `New File`。

![image](https://user-images.githubusercontent.com/21993717/175768145-3388ba91-1256-4e20-b347-6c2249fd6ce5.png)

輸入新檔檔名: index.html 按下 Enter

![2022-06-25 16-59-59](https://user-images.githubusercontent.com/21993717/175768211-686b402f-9f72-4887-b99f-39e2b16da8a9.png)

在檔案編輯區輸入`!` (驚嘆號)再按下 Enter 鍵，VSCode 自動補齊 Html 的基本內容。
![2022-06-25 17-00-45](https://user-images.githubusercontent.com/21993717/175768234-12d274af-80df-4e9c-a0d9-3cdbf0ac6387.png)

自動補齊的預設內容：
![2022-06-25 17-02-06](https://user-images.githubusercontent.com/21993717/175768266-9bfef29b-8641-47ef-9f0c-34d816e13ac3.png)

### HTML document

![2022-06-25 13-07-00 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/175759112-5a0d2e72-2155-480c-a94d-7f2a75009d49.png)

### HTML heading

![image](https://user-images.githubusercontent.com/21993717/175759249-846288aa-c383-4d1a-ad83-8aa6df24d6a4.png)

### HTML paragraphs

![image](https://user-images.githubusercontent.com/21993717/175768431-fc4d1bcb-9eb1-465f-bc42-4286bce7cf24.png)

### HTML links

![image](https://user-images.githubusercontent.com/21993717/175768535-2bbebb6c-cdfb-4fec-bded-497104ffaee8.png)

### HTML images

![image](https://user-images.githubusercontent.com/21993717/175768591-8891e576-a5ac-44e2-85e7-7d4d2f56c289.png)

### HTML buttons

![image](https://user-images.githubusercontent.com/21993717/175768642-d2aa6fe5-b25a-45c1-8bd2-e0719b55cce4.png)

### HTML lists

![image](https://user-images.githubusercontent.com/21993717/175768701-2849fb01-fead-48d2-b1ba-4e3e1fc7ccf8.png)

### HTML table

一. 簡易表格範例

![image](https://user-images.githubusercontent.com/21993717/175768868-4f0519f3-6443-4d78-ae97-08a4241b31b2.png)

二. 表格範例
```html
<!DOCTYPE html>
<html>
<head>
<style>
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td, th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
</style>
</head>
<body>

<h2>HTML Table</h2>

<table>
  <tr>
    <th>Company</th>
    <th>Contact</th>
    <th>Country</th>
  </tr>
  <tr>
    <td>Alfreds Futterkiste</td>
    <td>Maria Anders</td>
    <td>Germany</td>
  </tr>
  <tr>
    <td>Centro comercial Moctezuma</td>
    <td>Francisco Chang</td>
    <td>Mexico</td>
  </tr>
  <tr>
    <td>Ernst Handel</td>
    <td>Roland Mendel</td>
    <td>Austria</td>
  </tr>
  <tr>
    <td>Island Trading</td>
    <td>Helen Bennett</td>
    <td>UK</td>
  </tr>
  <tr>
    <td>Laughing Bacchus Winecellars</td>
    <td>Yoshi Tannamuri</td>
    <td>Canada</td>
  </tr>
  <tr>
    <td>Magazzini Alimentari Riuniti</td>
    <td>Giovanni Rovelli</td>
    <td>Italy</td>
  </tr>
</table>

</body>
</html>

```

![image](https://user-images.githubusercontent.com/21993717/175768925-fc0bf328-3c03-4a63-8582-af48c94fa91d.png)

三. 表格框線一
```css
table, th, td {
  border: 1px solid black;
}
```
![image](https://user-images.githubusercontent.com/21993717/175769179-8085bfb9-ad94-47fe-ae79-195192c6e028.png)

四. 表格框線二
```css
table, th, td {
  border: 1px solid black;
  border-collapse: collapse;
}
```

![image](https://user-images.githubusercontent.com/21993717/175769214-fa9a8e32-3a81-4277-a0b0-52b9896d47d2.png)

五. 表格框線三
```css
table, th, td {
  border: 1px solid white;
  border-collapse: collapse;
}
th, td {
  background-color: #96D4D4;
}
```

![image](https://user-images.githubusercontent.com/21993717/175769260-b2481212-3dab-441d-b707-a50f195d6cb4.png)

六. 表格框線四
```css
table, th, td {
  border: 1px solid black;
  border-radius: 10px;
}
```

![image](https://user-images.githubusercontent.com/21993717/175769292-1c093f3d-c0fd-4b46-9a62-321cde5bb648.png)


七. 表格大小

![image](https://user-images.githubusercontent.com/21993717/175769993-1cb6debd-cc12-4a35-9d2c-19217241ad5b.png)

![image](https://user-images.githubusercontent.com/21993717/175770054-ed86873a-2914-461f-b7fb-c08b70ac0913.png)

八. 表頭欄位合併

![image](https://user-images.githubusercontent.com/21993717/175770274-26fef119-1f5b-4096-8f1b-d4f400ada8d2.png)

九. 表格列合併
![image](https://user-images.githubusercontent.com/21993717/175770499-1b8de51d-dae4-43c5-873d-db8f6600b5a7.png)

十. 斑馬紋表格

```html
<style>
table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #D6EEEE;
}
</style>
```
![image](https://user-images.githubusercontent.com/21993717/175776767-862f4cb3-e58e-45bf-af50-641d2f740310.png)

## HTML 語法參考網站

- *[W3School](https://www.w3schools.com/html/default.asp)*


- *[MDN web docs](https://developer.mozilla.org/zh-TW/docs/Learn/HTML)*


## 國民健康調查網頁維護

### 使用 VSCode　編輯網頁

啟動 VSCode

![image](https://user-images.githubusercontent.com/21993717/175278234-44966d17-f172-4c9c-9cbc-131ac1ca4bfa.png)

點選 “Open Folder“ 按鈕

![image](https://user-images.githubusercontent.com/21993717/175278492-3d94d6ff-ab5b-4193-a43c-92c9847c53bc.png)

選擇 Source 所在的目錄 E:\Sources\Nhis2WebNew\

![image](https://user-images.githubusercontent.com/21993717/175278821-039bbc50-ab2d-4bb5-935b-389127b6aadd.png)

第一次開啟一個目錄時須要使用者的授權，請按“Yes"

![image](https://user-images.githubusercontent.com/21993717/175279396-5d1f5692-e41d-43b0-a9e4-021c6e8861f4.png)

開啟成功後，VSCode 檢核出你所在的作業系統是中文繁體的環境會顯示你可選擇安裝“語言套件”，若你選擇“安裝並重新啟動”

![image](https://user-images.githubusercontent.com/21993717/175280053-d06d957f-879e-4a25-8ff4-5b003ddc7da3.png)

語言套件安裝成功重新啟動後操作介面變成了中文繁體

![image](https://user-images.githubusercontent.com/21993717/175280387-1d4ef245-67e4-4516-a5d9-44b6de91b579.png)


### index.html

index.html 是網站的預設網頁，來看看它的組成有那些部分，在前面我們提過每一個網頁程式碼皆包含了五個部分：
1. 最上方的紅色區塊 對應的是: "head"
2. 中間左方的紅色區塊 對應的是: "功能選單"
3. 中間右方的紅色區塊 對應的是: "cont" （內文區塊）
4. 下方的倒數第二個紅色區塊 對應的是: "foot"
5. 最下方的紅色區塊 對應的是: "來訪人數和版權宣告"

也提到過其他"3.cont"(內文)區塊是每一個網頁的主角，這個區塊內容就是這個網頁的最重要內函，其他區塊外都是共用的，所以我們把眼光專注在 cont，以 index.html 為例，內文的部分除了最上方的“網頁路徑“（>首頁>最新消息）資訊外，又分成二大塊，位在上方的是"最新消息"，和下方的是“計畫緣起”。“最新消息”是一個比較特殊的內文，我們在下面章節中會獨立出來說明，先來了解一下“計畫緣起”的部分。

![image](https://user-images.githubusercontent.com/21993717/175777757-e8e9c0df-8af2-42a2-810c-af9c27f53a74.png)

配合程式碼的部分，可以發現“計畫緣起”是由一個 `<img>` 和一個 `<h1>` 以及一個 `<table>`等三個元素所組成。
- `<img>` 的部份是顯示一個“地球“的圖檔，在 src 這個屬性可以了解實際對應到的檔案的目錄及檔名。
- `<h1>` 則是使用最大號字型的標題用來顯示“計畫緣起“這個標題。
- 至於 `<table>` 的部分是一個有一列一欄的表格，內容物則由一個`<img>` 和二段文字 以及一個 `<a>`連結按鈕所組成。


![image](https://user-images.githubusercontent.com/21993717/175777123-6b74140c-931b-40c2-9c03-4ec6ba107029.png)

`<table>`中的這個`<img>`只是單純一個占位符，實際上顯示的圖檔是透過 javascript 程式碼來控制的。其中被呼叫的 function 是位於 /javascript/imgshow.js 程式中。在這支程式中可以很清楚的看到用來輸替顯示的圖檔是位於 /img/img01.jpg、img02.jpg、img03.jpg、img04.jpg、img05.jpg 等五個檔。

```js
//基礎參數設置
number_of_sponsors=5;   //設定展示圖片總頁數
var   showTimeOut=2000; //捲動更新時間
var   sctr=0;  
var   halt=0;  
var   isn=new   Array();  
for   (i=0;i<number_of_sponsors;i++){  
  isn[i]=new Image();  
}  

//設定每張展示圖片的圖檔連結   
isn[0].src="/img/img01.jpg";  
isn[1].src="/img/img02.jpg";  
isn[2].src="/img/img03.jpg"; 
isn[3].src="/img/img04.jpg"; 
isn[4].src="/img/img05.jpg";   
  
function   rotateIt(){  
  if   (halt!=1){  
    sctr++;  
    if   (sctr>number_of_sponsors-1){  
      sctr=0;  
      }  
    document.sponsor.src=isn[sctr].src;  
    setTimeout("rotateIt()",showTimeOut);  
    }  
  }  
  
function   doIt(){  
  halt=1;  
  location.href=durl[sctr];  
  }  
   
function   dispIt(){  
  parent.window.status=durl[sctr];  
  }  
```

### 功能表選單

接下來來看看“功能表選單區”。在前面有提過“功能表選單區”的內容在每一個網頁程式檔中都是一樣的。也就是說選單區中有任何的異動，必須同時更新到每一支網頁程式中。這時你就要善用VSCode中的全域置換功能。

![image](https://user-images.githubusercontent.com/21993717/175797945-3c8668d6-43bd-46c3-9be7-1d013127db35.png)

以下模擬一個修改選單文字的案例來說明如何使用全域置換功能。假設我們要把選單中第一行的“計畫內容”這個標題改成“計畫內容說明”

![image](https://user-images.githubusercontent.com/21993717/175799169-2f904a37-b98f-43d0-a61a-45029cd47276.png)

首先，按下 Ctrl+Shift+F 叫出"搜尋/置換“功能（如下圖），輸入要修改的內容，請注意以本例而語應該要輸入`<h5><a href="2001nhis.html">計畫內容</a></h5>`這個“完整”的完串，這樣才能表示是“在選單中的`計畫內容`這個選項的標題“，如此才不會改到其他內容中的`計畫內容`這樣樣字串，此處要特別小心緊慎。

![image](https://user-images.githubusercontent.com/21993717/175799291-b1ece5ff-fff3-4bbc-96f3-f672ad052e67.png)

在SEARCH 功能中輸入`<h5><a href="2001nhis.html">計畫內容</a></h5>`這個“完整”的字串後，在輸入文字框的下方會出現本專案中所有符合條件的清單，也就是說這些地方都是我們必須去更改的地方。

![image](https://user-images.githubusercontent.com/21993717/175799413-52a76c23-195b-4722-ad43-e7a9d8add438.png)

確認完這38個地方是我們要變更後，接著以滑鼠點選“Toggle Replace“按鈕打開”Replace"文字輸入框。

![image](https://user-images.githubusercontent.com/21993717/175799540-13f8e730-4dbd-4876-8cd5-b88fc1a6faa2.png)

在”Replace"文字輸入框中輸入要變更成結果，以本例而言就是`<h5><a href="2001nhis.html">計畫內容說明</a></h5>`這樣的字串。再按下“置換”按鈕

![image](https://user-images.githubusercontent.com/21993717/175799645-125a1fc6-43a5-48cc-8fe6-3cfdc3fe1231.png)

於“再次確認”的視窗中按下 "取代“，VSCode 將會自動修所有38個相符的地方。

![image](https://user-images.githubusercontent.com/21993717/175799762-fd202c2b-9084-456a-9405-4ea29ed815f1.png)


### 新增一個頁面
假設我們要新增一個 “網頁Q&A II“ 的新網頁，並且希望在“網頁Q&A“功能選項下方新增一個“網頁Q&A II“的功能項目。
由於“網頁Q&A II“可能類似“網頁Q&A“，所以可以考慮透過複制的方式來産生這個新頁面。　
首先在瀏覽器上功能選項中點選"網頁Q&A"查看實際檔名，發現它的檔名是“Q&A.html”

![image](https://user-images.githubusercontent.com/21993717/175850263-8b86473b-93cb-495c-afc4-9fedc8f0403c.png)

然後在VSCode找到“Q&A.html”，以滑鼠右鍵點選"Q&A.html"這個檔案，並點選 "copy" 功能。

![image](https://user-images.githubusercontent.com/21993717/175852117-cceae23b-6b75-4061-a56d-48d14ec8772c.png)

再到 VSCode 檔案總管功能中最下方的空白處按下滑鼠右鍵，叫出功能選單，選擇“Paste"(貼上)

![image](https://user-images.githubusercontent.com/21993717/175851620-0378ba4a-6c2e-4c25-8f5d-fa5036a424b3.png)

貼上完成後，會發現在 “Q&A.html”上方已經多了一個名為　“Q&A copy.html”　的新網頁。

![image](https://user-images.githubusercontent.com/21993717/175851899-29f042b0-2e29-47e1-ace4-b0878422d2e2.png)

接下來先把它改名成為 “Q&A II.html”

![image](https://user-images.githubusercontent.com/21993717/175852439-590721fa-efc8-4ce1-bd51-0a1e68b8da60.png)

修改好檔名後，接著就是要週整網頁內容了，從前面章節的說明裡可以知道在這個網頁中我們要節整的地方，基本上有二個地方：一是“cont"（內文）、二是“功能選項”。

![image](https://user-images.githubusercontent.com/21993717/175852667-d7958407-9f47-41c1-be90-7e2e5746c7d8.png)


“cont"（內文）的部份依需求把原有的內容改成你需要的內容，此處說明方便起見，只將"網站Q&A"的字串改成“網站Q&A II"

```html
<div id="cont">
				<font color=#ff6e00>>網站內容>網站Q&A</font>
				<img STYLE="FLOAT: LEFT; MARGIN: 10PX" src="/img/icon1.jpg" alt="" width="50" height="48" class="left" />
				<h1>網站Q&A</h1>
```
改完後
```html
<div id="cont">
				<font color=#ff6e00>>網站內容>網站Q&A II</font>
				<img STYLE="FLOAT: LEFT; MARGIN: 10PX" src="/img/icon1.jpg" alt="" width="50" height="48" class="left" />
				<h1>網站Q&A II</h1>
```

內文內容完成後，接下來是要在功能選項中新加入一個“網頁Q&A II“的功能項目在“網頁Q&A“功能選項下方。這裡值得注意的是這個新增功能選項的動作除了在“Q&A II.html"檔案內必須新加入外，在其他的網頁程式(.html)中也要加入，所以這裡新加入的動作可以利用前面章節介紹的“全域修改“的方法來完成。

首先找到 "Q&A.html" 這個選項的程式碼，即“<a href="Q&A.html">網站Q&A</a>”，利用滑鼠將這整個字串“選取”起來，再按下 Ctrl+Shift+H 叫出搜尋/置換功能視窗，在“置換”文字框輸入要置換的字串。這裡要特別注意的是我們使用了“置換”功能來完成“新增功能選項”的需求，而不是真的要把“<a href="Q&A.html">網站Q&A</a>”這個字串置換掉，所以在取代的文字框內除了保留原來的“<a href="Q&A.html">網站Q&A</a>”字串外，將游標停在在這個字中最後的位置，並按下 Ctrl+Enter 按鈕來新增一個“換行”，然後再將“<a href="Q&A.html">網站Q&A II</a>” 這個新的功能選項程式碼繼續加在取代的文字框內，如下圖

![image](https://user-images.githubusercontent.com/21993717/175855885-e21bd426-99be-4129-a34b-b58321c77031.png)

輸入就續後按下“置換”按鈕

![image](https://user-images.githubusercontent.com/21993717/175854827-e28f343a-6d0e-498f-ae50-d8b0281df4d8.png)

確認要進行所有檔案的置換則按下 replace 

![image](https://user-images.githubusercontent.com/21993717/175854938-29155881-d4c7-49a1-a3da-77b913d72f0d.png)

完成置換

![image](https://user-images.githubusercontent.com/21993717/175855183-0b3bcb92-3331-4c9e-8b04-7a5963e8265f.png)

完後瀏覽結果

![image](https://user-images.githubusercontent.com/21993717/175856132-86308603-3743-4752-ae43-870111c81b85.png)


### 修改頁面

接下來我們以 nhri.html 來介紹修改的功能，在這個網頁的內容中主角是 `<table>`。

```html
  <div id="cont">		
		<font color=#ff6e00>>工作團隊>財團法人國家衛生研究院</font>
		<img STYLE="FLOAT: LEFT; MARGIN: 10PX" src="img/icon1.jpg" alt="" width="50" height="48" class="left"/>
		<h1>工作團隊</h1>				

		<table summary="Submitted 財團法人國家衛生研究院">
			<caption>財團法人國家衛生研究院</caption>
			<caption>電話:(037)246-166　　　地址:350 苗栗縣竹南鎮科研路35號</caption>
			<thead>
				<tr>
					<th width="30%" scope="col">服務單位</th>
					<th width="25%" scope="col">人員</th>
					<th width="15%" scope="col">電話分機</th>
					<th width="30%" scope="col">電子郵件信箱</th>
				</tr>
			</thead>	
			<tfoot>
				<tr>				</tr>
			</tfoot>
			<tbody>
				<tr>
					<th>群體健康科學研究所</th>
					<td>熊昭 所長兼生統生資組組主任</td>
					<td>36100</td>
					<td><a href="mailto:hsiung@nhri.org.tw" class="style1">hsiung@nhri.org.tw</a></td>
			  </tr>
	
			  			  
				<tr class="odd">
					<th>群健所醫療保健研究組</th>
					<td>張新儀 研究員</td>
					<td>36333</td>
					<td><a href="mailto:hsingyi@nhri.org.tw" class="style1">hsingyi@nhri.org.tw</a></td>
			  </tr>
				<tr  >
					<th>群健所醫療保健研究組</th>
					<td>于勝宗 研究助理</td>
					<td>36343</td>
					<td><a href="mailto:yu650911@nhri.org.tw" class="style1">yu650911@nhri.org.tw</a></td>
			  </tr>
				<tr class="odd">
					<th>群健所醫療保健研究組</th>
					<td>江啟永 研究助理</td>
					<td>36329</td>
					<td><a href="mailto:ccy@nhri.org.tw" class="style1">ccy@nhri.org.tw</a></td>
			  </tr>
				<tr  >
					<th>群健所醫療保健研究組</th>
					<td>方心伶 研究助理</td>
					<td>36373</td>
					<td><a href="mailto:sling0329@nhri.org.tw" class="style1">sling0329@nhri.org.tw</a></td>
			  </tr>

				<tr class="odd">
					<th>群健所生物統計與生物資訊研究組</th>
					<td>陳主智 研究員</td>
					<td>36186</td>
					<td><a href="mailto:ccchen@nhri.org.tw" class="style1">ccchen@nhri.org.tw</a></td>
			  </tr>

				<tr >
					<th>群健所生物統計與生物資訊研究組</th>
					<td>翁文舜 研究助理</td>
					<td>36129</td>
					<td><a href="mailto:wengws@nhri.org.tw" class="style1">wengws@nhri.org.tw</a></td>
			  </tr>
				<tr class="odd">
					<th>群健所生物統計與生物資訊研究組</th>
					<td>劉季鑫 研究助理</td>
					<td>36144</td>
					<td><a href="mailto:jihshin@nhri.org.tw" class="style1">jihshin@nhri.org.tw</a></td>
			  </tr>
				<tr >
					<th>群健所生物統計與生物資訊研究組</th>
					<td>鍾曉君 研究助理</td>
					<td>36131</td>
					<td><a href="mailto:941014@nhri.org.tw" class="style1">941014@nhri.org.tw</a></td>
			  </tr>
				<tr class="odd">
				  <th>群健所老年醫學研究組</th>
				  <td>許志成 主任</td>
				  <td>36336</td>
				  <td><a href="mailto:cch@nhri.org.tw" class="style1">cch@nhri.org.tw</a></td>
			  </tr>
				<tr >
				  <th>群健所老年醫學研究組</th>
				  <td>吳名祥 研究助理</td>
				  <td>37319</td>
				  <td><a href="mailto:start082105@nhri.org.tw" class="style1">start082105@nhri.org.tw</a></td>
			  </tr>
					
	       <tr class="odd" >
				  <th>群健所精神與成癮醫學研究組</th>
				  <td>陳娟瑜 副研究員</td>
				  <td>36705</td>
				  <td><a href="mailto:cychen@nhri.org.tw" class="style1">cychen@nhri.org.tw</a></td>
			  </tr>		  
			  <tr >
				  <th>群健所精神與成癮醫學研究組</th>
				  <td>陳光宏 博士生</td>
				  <td> </td>
				  <td><a href="mailto: d49907004@ym.edu.tw" class="style1"> d49907004@ym.edu.tw</a></td>
			  </tr>	  
			  
			  
			  
			  		
			</tbody>	
		</table>
	  <a STYLE="FLOAT: RIGHT; MARGIN: 5PX" href="#" class="top">【回到頁首】</a>
	</div> 
  ```

![image](https://user-images.githubusercontent.com/21993717/175860528-1be89375-34cd-424f-99be-8f3ee8d2f8cf.png)

修改後
```html
			<div id="cont">
				<font color=#ff6e00>>工作團隊>財團法人國家衛生研究院</font>
				<img STYLE="FLOAT: LEFT; MARGIN: 10PX" src="/img/icon1.jpg" alt="" width="50" height="48" class="left" />
				<h1>工作團隊</h1>

				<table summary="Submitted 財團法人國家衛生研究院">
					<caption>財團法人國家衛生研究院</caption>
					<caption>電話:(037)206-166　　　地址:350 苗栗縣竹南鎮科研路35號</caption>
					<thead>
						<tr>
							<th width="30%" scope="col">服務單位</th>
							<th width="25%" scope="col">人員</th>
							<th width="15%" scope="col">電話分機</th>
							<th width="30%" scope="col">電子郵件信箱</th>
						</tr>
					</thead>
					<tfoot>
						<tr> </tr>
					</tfoot>
					<tbody>
						<tr>
							<th>群體健康科學研究所</th>
							<td>邱弘毅特聘研究員兼所長</td>
							<td>36100</td>
							<td><a href="mailto:hychiou@nhri.edu.tw" class="style1">hychiou@nhri.edu.tw</a></td>
						</tr>
						<tr class="odd">
							<th></th>
							<td>許志成研究員<br>兼副所長</td>
							<td>36336</td>
							<td><a href="mailto:cch@nhri.edu.tw" class="style1">cch@nhri.edu.tw</a></td>
						</tr>

						<tr>
							<th></th>
							<td>張新儀研究員</td>
							<td>36333</td>
							<td><a href="mailto:hsingyi@nhri.edu.tw" class="style1">hsingyi@nhri.edu.tw</a></td>
						</tr>
						<tr class="odd">
							<th></th>
							<td align="right">于勝宗研究助理</td>
							<td>36343</td>
							<td><a href="mailto:yu650911@nhri.edu.tw" class="style1">yu650911@nhri.edu.tw</a></td>
						</tr>
						<tr>
							<th></th>
							<td align="right">江啟永研究助理</td>
							<td>36329</td>
							<td><a href="mailto:ccy@nhri.edu.tw" class="style1">ccy@nhri.edu.tw</a></td>
						</tr>
						<tr class="odd">
							<th></th>
							<td align="right">方心伶研究助理</td>
							<td>36373</td>
							<td><a href="mailto:sling0329@nhri.edu.tw" class="style1">sling0329@nhri.edu.tw</a></td>
						</tr>

						<tr>
							<th></th>
							<td>陳主智研究員</td>
							<td>36186</td>
							<td><a href="mailto:ccchen@nhri.edu.tw" class="style1">ccchen@nhri.edu.tw</a></td>
						</tr>
						<tr class="odd">
							<th></th>
							<td align="right">翁文舜研究助理</td>
							<td>36129</td>
							<td><a href="mailto:wengws@nhri.edu.tw" class="style1">wengws@nhri.edu.tw</a></td>
						</tr>

						<tr>
							<th>神經及精神醫學研究中心</th>
							<td>陳娟瑜合聘研究員</td>
							<td>(02)2826-7000 #65034</td>
							<td><a href="mailto:cychen@nhri.edu.tw" class="style1">chuanychen@gmail.com</a></td>
						</tr>

					</tbody>
				</table>
				<a STYLE="FLOAT: RIGHT; MARGIN: 5PX" href="#" class="top">【回到頁首】</a>
			</div>

```

![image](https://user-images.githubusercontent.com/21993717/175860631-b6708e6e-63ae-4c58-b196-1eaaf2bde612.png)


### 刪除一個頁面

假設我們要移除“工作團隊”中的“衛生福利部食品藥物管理署“這個網頁，主要有二個步驟：一是把選項中的“衛生福利部食品藥物管理署“移除，第二步驟是：nbcd.html 這個網頁檔案刪除。

![image](https://user-images.githubusercontent.com/21993717/175860952-64e2c0fb-5ffc-4380-8f7f-01318d6a8330.png)

先找到 "nhri.html"中“衛生福利部食品藥物管理署“這個選項的程式碼，即“<a href="nbcd.html">衛生福利部食品藥物管理署</a>”，利用滑鼠將這整個字串“選取”起來，再按下 Ctrl+Shift+H 叫出搜尋/置換功能視窗，在“置換”文字框中直接留著空白，再按下“置換”按鈕進行“全域置換“的動作即可。

![image](https://user-images.githubusercontent.com/21993717/175861525-15946cf5-7bc4-4495-922b-7e75263db4fb.png)

另外也可以把　“<a href="nbcd.html">衛生福利部食品藥物管理署</a>”　置換成 "`<!-- <a href="nbcd.html">衛生福利部食品藥物管理署</a> -->`" 來達成同樣的效果，不同處是：這個作法是透過“註解“的方式來“隱蔵這個選項而不是這個段網頁程式碼”刪除“了。

![image](https://user-images.githubusercontent.com/21993717/175862217-b3b7a197-a81c-4cc2-8546-442a42a2385f.png)

### 最新消息清單維護

接下來“最新消息”的網頁修改是屬比較特殊的部分。先來介紹“清單”的部分。在 VSCode 找到 /javascript/news.js 這支 JavaScript 程式檔。在這程式中只有“設定新聞訊息與連結”這個部分是需要被維護的，其餘皆是固定的 JS 程式碼，不要作任何變更。

![image](https://user-images.githubusercontent.com/21993717/175892155-21f0a14e-8d50-4b69-ab16-8f8326b73a86.png)

以上圖為例，可以看到二個陣列，newText[] 這個陣列是用來將 "最新消息“ 的標題顯在網站的“預設網頁”(index.html)中，當使用者透過滑鼠點選在這四個清單中任一項時，系統會自動開啟對應各最新消息的詳細說明網頁。而這些“最新消息的詳細說明網頁”則是透過第二個陣列 newsURL 來設定的。以第一則最新消息來看，標題是“「國民健康訪問調查」資料管理中心暫停受理各項資料使用申請的業務。 因應目前正在進行「國民健康訪問調查」問卷資料檔的去識別化驗證，自2021年2月3日起「國民健康訪問調查」資料管理中心暫停受理各項資料使用申請的業務。(2021/02/03公告)”，而更詳細的說明則是在 "20210203.html" 檔案中。

![image](https://user-images.githubusercontent.com/21993717/175894130-e35f420e-0c66-4296-b45f-95a877963c55.png)

滑鼠點選在這四個清單中第一則消息，系統會自動打開"20210203.html"最新消息的詳細說明。

![image](https://user-images.githubusercontent.com/21993717/175894783-77748c8f-7ac0-41f4-9aea-de8d2829bd84.png)

按下 "close" 按鈕時，系統會自動關閉這個詳細說明頁面。

![image](https://user-images.githubusercontent.com/21993717/175895002-865f1031-df96-4a50-b0ba-e3adf18c88e4.png)


### 最新消息明細網頁維護
至於“最新消息明細網頁維護”則如同一般 HTML 網頁程式一樣。

![image](https://user-images.githubusercontent.com/21993717/175895789-0e987a71-e31f-4e3c-9f34-5c3d4ff8e053.png)

其中需要被維護的部分就是紅框反白處（也就是內容區塊的部分）。這區塊內可以支支所有 HTML 格式的設定。以下圖為例，先有個“標題”（h2)，再放了一個段落。你也依需求放入一個`<table>`或是`<div>`區塊...等。

![image](https://user-images.githubusercontent.com/21993717/175896569-48505dbe-0f86-479f-a480-5adca3314eee.png)


## 在開發環境查看網站成果

在開發維護中如何查看網頁的成果呢？一般而言，瀏覽器是連結到伺服器上去要求回應網頁內容，再由瀏覽器呈現網頁的最終成果。透過 VSCode 在網頁開發階段時不需這麼複雜，只要透過前面“開發環境安裝”章節介紹的 Live Server 套件就可以看到與伺服器支援相同效果的成果。

### 啟動網站

以 "20210203.html" 為例，要查看它的實際成果，先在 VSCode 找到這檔案並打開它。

![image](https://user-images.githubusercontent.com/21993717/175903059-6100657c-772b-4990-9f96-a1a2a6d42446.png)

只須按下視窗右下方的"Go Live"按鈕，VSCode即自動打開瀏覽器並將成果顯示在瀏覽器中。

![image](https://user-images.githubusercontent.com/21993717/175903817-474e63d7-3f82-4983-be6c-16f0ddb8b540.png)

註："Go Live"按鈕功能是由"Live Server 套件"　所提供，要依前面章節所介紹的先進行安裝。

## 更新到主機
當已完成所有新增／修改作業後，必須把相關的收到更新到伺服器上(10.64.18.223)。

### 主機上網頁程式目錄

在伺服器上打開桌面上的“Internet Information Services (IIS) 管理員”

![image](https://user-images.githubusercontent.com/21993717/175909731-a1fc017a-8153-484f-bc3f-b19ac545d8e3.png)

其他站台 - NHIS2Web 就是 "NHIS 國民健康訪問調整網站"，要查看這個網站的相關程式碼檔案所存放的位置，將滑鼠點選“IIS管理員“中“動作”中的“瀏覽”，系統會自動打開檔案管理員，並顯示出程式碼檔案所存放的路徑及檔案。

![image](https://user-images.githubusercontent.com/21993717/175910597-82dec717-40d0-4809-8801-e989c261b7da.png)

將你在local 已修改的所有檔案傳送到這個目錄來，覆蓋掉舊檔即可。

### IIS 操作

在更新好有修改／變更的檔案後，你可以操作 “IIS管理員“ 的相關功能先來確認網頁是否有正常作業。

為確保 IIS Server 有讀到最新更新的檔案，你可以執行 “IIS管理員“ 中的 "重新啟動“ 功能。

![image](https://user-images.githubusercontent.com/21993717/175914054-7bed70e2-2ca6-4329-b8a7-1ff31dfdf2de.png)
