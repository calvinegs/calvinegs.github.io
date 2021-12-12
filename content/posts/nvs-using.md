---
title: "使用 nvs 來管理不同版本 Node.js 執行環境"
date: 2021-12-12
draft: false
description: "開發環境為了不同的目的可能會同時使用到不同版本的 Node.js，透過 nvs 工具可以有效、快速切換各個版本的 Node.js 開發環境"
tags: ["nvs", "Node.js", "Angular"]
categories: ["nvs"]
---

Angular 的版本更新迭代的相當快 (所有的前端開於工具都有相同的情形)，安裝及使用 Angular Cli 時必須在有 Node.js 的環境下才能進行。

當你在同一台電腦中使用不同版本的 Angular 時就容易在安裝相關套件時遇到警語，原因常是因為 Angular 與 Node.js 版本搭配的關係。

至於為何有同時使用不同版本的 Angular 呢? 除了開發的專案沒有全部都一起升級至統一版本(這是一件複雜的事)的因素外，個人最常遇到的狀況是: 當在網上(github)找到很棒的 Demo / Sample 程式，在 git clone 回電腦後發現它的版本是舊版本 Angular 所撰寫，使用 npm install 安裝相關套件時，出現警告訊息，甚至無法安裝成功。

為了要管理同一台電腦中存在著有不同版 Node.js，讓你很容易在各版本中自由的進行切換 (甚至在進入不同目錄時自動切換對應的 Node.js 版本)，你需要 Node.js 版本管理工具，如: NVM 或 NVS，在這個筆記中要記錄的是 NVS。

## 安裝 nvs

```shell
F:\> choco list nvs #先查看是否已安裝了 nvs
F:\> choco install nvs #安裝 nvs
F:\> nvs --version #查看是否安裝成功，並顯示 nvs 版本
```

## 安裝不同版本 Node.js

以個人工作環境為例，目前使用的 Angular 是 13.0 搭配的 Node.js 版本是 V16.10.0。

當我需要參考其他範例時，如在 github 找到一個`範例`程式，git clone 後，查看該專案發現它撰寫時使用 Angular 8.2.0 這個版本

`PS F:\test\angular-forms-workshop> cat package.json`

```json
{
  "name": "angular-forms-workshop",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "~8.2.0",
    "@angular/common": "~8.2.0",
    "@angular/compiler": "~8.2.0",
    "@angular/core": "~8.2.0",
    "@angular/forms": "~8.2.0",
    "@angular/platform-browser": "~8.2.0",
    "@angular/platform-browser-dynamic": "~8.2.0",
    "@angular/router": "~8.2.0",
    "@ng-bootstrap/ng-bootstrap": "^5.1.0",
    "@ng-dynamic-forms/core": "^9.0.1",
    "@ng-dynamic-forms/ui-ng-bootstrap": "^9.0.1",
    "angular2-text-mask": "^9.0.0",
    "bootstrap": "^4.3.1",
    "rxjs": "~6.4.0",
    "tslib": "^1.10.0",
    "zone.js": "~0.9.1"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~0.802.0",
    "@angular/cli": "~8.2.0",
    "@angular/compiler-cli": "~8.2.0",
    "@angular/language-service": "~8.2.0",
    "@types/node": "~8.9.4",
    "@types/jasmine": "~3.3.8",
    "@types/jasminewd2": "~2.0.3",
    "codelyzer": "^5.0.0",
    "jasmine-core": "~3.4.0",
    "jasmine-spec-reporter": "~4.2.1",
    "karma": "~4.1.0",
    "karma-chrome-launcher": "~2.2.0",
    "karma-coverage-istanbul-reporter": "~2.0.1",
    "karma-jasmine": "~2.0.1",
    "karma-jasmine-html-reporter": "^1.4.0",
    "protractor": "~5.4.0",
    "ts-node": "~7.0.0",
    "tslint": "~5.15.0",
    "typescript": "~3.5.3"
  }
}
```

若直接使用 `npm install` 來回復此專案的套件，將會出現許多的警告訊息，如下圖:

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Sat_Dec_11_2021_1639234975429.png)

查閱對照表後發現 Angular 8.2 版本應該搭配的是 Node.js 10.9.0 ( [Angular 與 Node.js 對照表](https://calvinegs.github.io/posts/ngcli-nodejs-verion/) )

此時可透過 nvs 來額外再安裝 Node.js 10.9.0 版本到你的系統中，並將 Node.js `使用版本` 切換到 10.9.0

```shell
PS F:\test\angular-forms-workshop> nvs add 10.9.0
PS F:\test\angular-forms-workshop> nvs use 10.9.0
```

完成後，使用這個 `正確` 的版本就可以順利安裝相關的套件了。

同一時間，我又在 github 找到另一個合適的`範例`程式，git clone 後，查看該專案撰寫時是使用那個版本的 Angular，發現是 11.2.13

```shell
PS F:\test\angular-forms-workshop> cd ..\angular.io-example\
PS F:\test\angular.io-example> cat package.json
```

```json
{
  "name": "angular.io-example",
  "version": "0.0.0",
  "private": true,
  "dependencies": {
    "rxjs": "6.6.7",
    "tslib": "2.2.0",
    "zone.js": "0.10.3",
    "jasmine-core": "3.6.0",
    "@angular/core": "11.2.13",
    "@angular/forms": "11.2.13",
    "@angular/common": "11.2.13",
    "@angular/router": "11.2.13",
    "jasmine-marbles": "0.6.0",
    "@angular/compiler": "11.2.13",
    "@angular/animations": "11.2.13",
    "@angular/platform-browser": "11.2.13",
    "angular-in-memory-web-api": "0.11.0",
    "@angular/platform-browser-dynamic": "11.2.13"
  },
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~0.1102.12",
    "@angular/cli": "~11.2.12",
    "@angular/compiler-cli": "~11.2.13",
    "@types/jasmine": "~3.6.0",
    "@types/node": "^12.11.1",
    "codelyzer": "^6.0.0",
    "jasmine-core": "~3.6.0",
    "jasmine-spec-reporter": "~5.0.0",
    "karma": "~6.1.0",
    "karma-chrome-launcher": "~3.1.0",
    "karma-coverage": "~2.0.3",
    "karma-jasmine": "~4.0.0",
    "karma-jasmine-html-reporter": "^1.5.0",
    "protractor": "~7.0.0",
    "ts-node": "~8.3.0",
    "tslint": "~6.1.0",
    "typescript": "~4.1.5"
  }
}
```

一樣的道理，透過 nvs 再多安裝 Node.js 12.11.1 版本

```shell
PS F:\test\angular-forms-workshop> nvs add 12.11.1
```

## nvs 的使用方法

查看目前所有已安裝的 Node.js 版本

PS F:\test\angular-forms-workshop> nvs ls

```shell
node/16.10.0/x64   #Angular 13 所搭配的 Node.js 版本
node/14.15.5/x64 (Fermium)   #Angular 12 所搭配的 Node.js 版本
node/12.11.1/x64   #Angular 11 所搭配的 Node.js 版本
node/10.9.0/x64    #Angular 8 所搭配的 Node.js 版本
node/8.9.4/x64     #Angular 7 所搭配的 Node.js 版本
```

### 手動切換

```shell
PS F:\test\angular-forms-workshop> nvs use 10 #使用(切換到)第十版，在範例中會自動切換到 10.9.0
PS F:\test\angular-forms-workshop> node -v #顯示目前使用中的 Node.js 版本
v10.9.0

PS F:\test\angular-forms-workshop> nvs ls #前導 '>' 符號表示是目前`使用中`的版本
  node/16.10.0/x64
  node/14.15.5/x64 (Fermium)
  node/12.11.1/x64
 >node/10.9.0/x64
  node/8.9.4/x64 (Carbon)
```

### 自動切換

每個目錄若搭配 `.node-version` 檔名的文字檔，且內容是 Node.js 的版本資訊:

```shell
PS F:\test> node -v > .node-version #將版本資訊寫入 .node-version 檔案中
PS F:\test> cat .node-version #查看內容
v10.9.0
```

除有上述檔案及內容外，還必須搭配 `開啓 nvs 自動切換` 功能

`PS F:\test> nvs auto on `

以上兩個條件都成立時，當切換不同`現行目錄`時，系統會自動選擇套用不同的 Node.js 版本

```shell
PS F:\test> cd .\angular.io-example\
PATH += $env:LOCALAPPDATA\nvs\node\12.11.1\x64
PS F:\test\angular.io-example> node -v
v12.11.1
PS F:\test\angular.io-example> cat .\.node-version
v12.11.1
PS F:\test\angular.io-example> cd ..\angular-forms-workshop\
PATH -= $env:LOCALAPPDATA\nvs\node\12.11.1\x64
PATH += $env:LOCALAPPDATA\nvs\node\10.9.0\x64
PS F:\test\angular-forms-workshop> node -v
v10.9.0
```

### 半自動型手動切換

若 `nvs 自動切換` 功能設為 `off` 則可視為所謂的 `半自動` 模式。

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Fri_Dec_10_2021_1639149268309.png)

無.node-version 檔案，也無 nvs 也無設定預設版本的情況:

![image](https://gist.github.com/calvinegs/ff926f09800f808617f84d0a966a8b31/raw/images---Fri_Dec_10_2021_1639149076726.png)

`PS F:\test> nvs use`

上述指令等同 nvs use default (nvs use auto、nvs auto 亦同義) 就是切換所在目錄的 Node.js 版本，系統第一優先查看的現行目錄中的 .node-version 檔案裡所指定的 Node.js 版本，若無 .node-version 檔案，則使用 nvs default (預設)版本，若 nvs 也無設定預設版本，則會將目前的 '使用版本' reset 成 null。
