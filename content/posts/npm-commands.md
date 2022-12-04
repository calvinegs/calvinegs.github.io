---
title: "好用的 npm 指令"
date: 2022-12-11
draft: false
description: "除了常用 npm 指令, 你一定要懂的好用指令"
tags: ["npm"]
categories: ["Node.js"]
---
本篇筆記是除了以下常用的 npm 指令外，要介紹幾個好用的其他 npm 指令
- npm --version (檢查在本端主機上安裝的 npm 版本)
- npm init (在現行目錄中初始化專案)
- npm list (顯示所有已安裝在專案中的所有套件)
- npm install (在專案中安裝套件)
- npm uninstall (移除已安裝在專案中的套件)


## 1.開啟套件的說明文件 - npm docs
```bash
$ npm docs [package-name]

#例如以下指令將會自動開啟 lodash 套件的說明網頁
$ npm docs lodash
```

![image](https://user-images.githubusercontent.com/21993717/205472805-efcbce43-39b1-4952-a02f-c3c5aecc9024.png)


## 2.開啟套件的原始碼儲存庫網頁 - npm repo
```bash
$ npm repo [package-name]

#例如以下指令將會自動開啟 lodash 套件的原始碼儲存庫網頁
$ npm repo lodash
```

![image](https://user-images.githubusercontent.com/21993717/205472928-8a19bf2c-afec-4a9e-80ae-78b6155d7202.png)

## 3.查看套件的版本資訊
```bash
$ npm v [package-name] versions

#以下指令將直接在 終端機 上顯示所有 lodash 套件的版本資訊
$ npm v lodash versions
[
  '0.1.0',      '0.2.0',      '0.2.1',   '0.2.2',   '0.3.0',
  '0.3.1',      '0.3.2',      '0.4.0',   '0.4.1',   '0.4.2',
  '0.5.0-rc.1', '0.5.0',      '0.5.1',   '0.5.2',   '0.6.0',
  '0.6.1',      '0.7.0',      '0.8.0',   '0.8.1',   '0.8.2',
  '0.9.0',      '0.9.1',      '0.9.2',   '0.10.0',  '1.0.0-rc.1',
  '1.0.0-rc.2', '1.0.0-rc.3', '1.0.0',   '1.0.1',   '1.0.2',
  '1.1.0',      '1.1.1',      '1.2.0',   '1.2.1',   '1.3.0',
  '1.3.1',      '2.0.0',      '2.1.0',   '2.2.0',   '2.2.1',
  '2.3.0',      '2.4.0',      '2.4.1',   '2.4.2',   '3.0.0',
  '3.0.1',      '3.1.0',      '3.2.0',   '3.3.0',   '3.3.1',
  '3.4.0',      '3.5.0',      '3.6.0',   '3.7.0',   '3.8.0',
  '3.9.0',      '3.9.1',      '3.9.2',   '3.9.3',   '3.10.0',
  '3.10.1',     '4.0.0',      '4.0.1',   '4.1.0',   '4.2.0',
  '4.2.1',      '4.3.0',      '4.4.0',   '4.5.0',   '4.5.1',
  '4.6.0',      '4.6.1',      '4.7.0',   '4.8.0',   '4.8.1',
  '4.8.2',      '4.9.0',      '4.10.0',  '4.11.0',  '4.11.1',
  '4.11.2',     '4.12.0',     '4.13.0',  '4.13.1',  '4.14.0',
  '4.14.1',     '4.14.2',     '4.15.0',  '4.16.0',  '4.16.1',
  '4.16.2',     '4.16.3',     '4.16.4',  '4.16.5',  '4.16.6',
  '4.17.0',     '4.17.1',     '4.17.2',  '4.17.3',  '4.17.4',
  '4.17.5',     '4.17.9',     '4.17.10', '4.17.11', '4.17.12',
  '4.17.13',    '4.17.14',    '4.17.15', '4.17.16', '4.17.17',
  '4.17.18',    '4.17.19',    '4.17.20', '4.17.21'
]
```

若不使用這個方便的指令，那你就比須要原始碼儲存庫網站上去查看：
![image](https://user-images.githubusercontent.com/21993717/205473063-289ebe48-3f28-4c52-87b0-cfabe67b77e6.png)


## 4.查看套件的相關資訊 - npm v
```bash
$ npm v [package-name] 

#以下指令將直接在 終端機 上顯示所有 lodash 套件的相關資訊
$ npm v lodash

Lodash modular utilities.
https://lodash.com/

keywords: modules, stdlib, util

dist
.tarball: https://registry.npmjs.org/lodash/-/lodash-4.17.21.tgz
.shasum: 679591c564c3bffaae8454cf0b3df370c3d6911c
.integrity: sha512-v2kDEe57lecTulaDIuNTPy3Ry4gLGJ6Z1O3vE1krgXZNrsQ+LFTGHVxVjcXPs17LhbZVGedAJv8XZ1tvj5FvSg==
.unpackedSize: 1.4 MB

maintainers:
- mathias <mathias@qiwi.be>
- jdalton <john.david.dalton@gmail.com>
- bnjmnt4n <benjamin@dev.ofcr.se>

dist-tags:
latest: 4.17.21  

published a year ago by bnjmnt4n <benjamin@dev.ofcr.se>
```

## 5.查看專案中是否有已過時的依賴套件 - npm outdated
```bash
# 在終端機中先切換到專案的目錄中 - $ cd project-name
# 以下指令是在 "form-validation" 專案目錄中執行的結果
$ npm outdated
```

![2022-12-04 11-46-45 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/205473306-61bbad4e-b651-4128-9674-08e1d7a06b44.png)


## 6.查看是否 node_modules 有相關資安漏洞 - npm audit
```bash
# 以下指令是在 "form-validation" 專案目錄中執行的結果, 可發現此專案中有6個相依套件有資案漏洞
$ npm audit

# npm audit report

decode-uri-component  <0.2.1
decode-uri-component vulnerable to Denial of Service (DoS) - https://github.com/advisories/GHSA-w573-4hg7-7wgq
fix available via `npm audit fix`
node_modules/decode-uri-component

engine.io  4.0.0 - 6.2.0
Severity: moderate
Uncaught exception in engine.io  - https://github.com/advisories/GHSA-r7qp-cfhv-p84w
fix available via `npm audit fix`
node_modules/engine.io

loader-utils  2.0.0 - 2.0.3 || 3.0.0 - 3.2.0
Severity: critical
Prototype pollution in webpack loader-utils - https://github.com/advisories/GHSA-76p3-8jx3-jpfq
loader-utils is vulnerable to Regular Expression Denial of Service (ReDoS) via url variable - https://github.com/advisories/GHSA-3rfm-jhwj-7488
loader-utils is vulnerable to Regular Expression Denial of Service (ReDoS) via url variable - https://github.com/advisories/GHSA-3rfm-jhwj-7488
fix available via `npm audit fix`
node_modules/adjust-sourcemap-loader/node_modules/loader-utils
node_modules/babel-loader/node_modules/loader-utils
node_modules/loader-utils
node_modules/resolve-url-loader/node_modules/loader-utils
  @angular-devkit/build-angular  0.1001.0-next.0 - 12.2.17 || 13.0.0-next.0 - 13.3.9 || 14.0.0-next.0 - 14.2.9 || 15.0.0-next.0 - 15.0.0-rc.5
  Depends on vulnerable versions of loader-utils
  Depends on vulnerable versions of terser
  node_modules/@angular-devkit/build-angular

socket.io-parser  4.0.0 - 4.0.4
Severity: critical
Insufficient validation when decoding a Socket.IO packet - https://github.com/advisories/GHSA-qm95-pgcg-qqfq
fix available via `npm audit fix`
node_modules/socket.io-parser

terser  5.0.0 - 5.14.1
Severity: high
Terser insecure use of regular expressions before v4.8.1 and v5.14.2 leads to ReDoS - https://github.com/advisories/GHSA-4wf5-vphf-c2xc
fix available via `npm audit fix`
node_modules/terser
  @angular-devkit/build-angular  0.1001.0-next.0 - 12.2.17 || 13.0.0-next.0 - 13.3.9 || 14.0.0-next.0 - 14.2.9 || 15.0.0-next.0 - 15.0.0-rc.5
  Depends on vulnerable versions of loader-utils
  Depends on vulnerable versions of terser
  node_modules/@angular-devkit/build-angular

6 vulnerabilities (1 low, 1 moderate, 2 high, 2 critical)

To address all issues, run:
  npm audit fix

```

## 自動修正相關相依套件的漏洞
```bash
$ npm audit fix 
```