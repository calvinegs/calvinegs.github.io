---
title: "在 ubuntu 22.04 中建立一支 Angular 15 Web App"
date: 2023-01-05
description: "本篇筆記二個重點：一是在 ubuntu 22.04 中安裝及使用 nvm，二是如何使用最簡易的方法來産生一個 Angular 15 Web App 程式架構"
tags: ["angular", "node.js", "nvm", "ubuntu 22.04"]
categories: ["nvm"]
---

# 以最簡潔的方式在 ubuntu 22.04 中建立一支 Angular 15 Web App

先講結論：使用 "npm init @angular " 就可以在不先安裝 Angular Cli 的前題下，來建立一支 Web App 範本程式。

一般要新增一支 Angular Web App 都會使用 Angular Cli，而使用 Angular Cli 必須搭配上 Node.js & npm

## 一般的方式
### 在 ubuntu 22.04 上安裝 Node.js

```bash 
$ node -v # 先檢視是否有安裝 Node.js，若沒有則需安裝
$ 
$ sudo apt update
$ sudo apt install nodejs
$
$ node -v
v12.22.9
```

### 在 ubuntu 22.04 上安裝 npm 

```bash 
$ npm -v	# 先檢視是否有安裝 npm，若沒有則需安裝
$ 
$ sudo apt update
$ sudo apt install npm
$
$ npm -v
8.5.1
```

### 安裝 Angular Cli
```bash
$ mkdir ./test && cd ./test
$ npm install @angular/cli  # 在 local test 目錄下安裝最新版 Angular Cli

npm WARN deprecated @npmcli/move-file@2.0.1: This functionality has been moved to @npmcli/fs
npm WARN saveError ENOENT: no such file or directory, open '/home/egs/temp/v12/package.json'
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN notsup Unsupported engine for @angular/cli@15.0.4: wanted: {"node":"^14.20.0 || ^16.13.0 || >=18.10.0","npm":"^6.11.0 || ^7.5.6 || >=8.0.0","yarn":">= 1.13.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @angular/cli@15.0.4
npm WARN notsup Unsupported engine for npm-pick-manifest@8.0.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-pick-manifest@8.0.1
npm WARN notsup Unsupported engine for pacote@15.0.6: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: pacote@15.0.6
npm WARN notsup Unsupported engine for @angular-devkit/architect@0.1500.4: wanted: {"node":"^14.20.0 || ^16.13.0 || >=18.10.0","npm":"^6.11.0 || ^7.5.6 || >=8.0.0","yarn":">= 1.13.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @angular-devkit/architect@0.1500.4
npm WARN notsup Unsupported engine for @angular-devkit/schematics@15.0.4: wanted: {"node":"^14.20.0 || ^16.13.0 || >=18.10.0","npm":"^6.11.0 || ^7.5.6 || >=8.0.0","yarn":">= 1.13.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @angular-devkit/schematics@15.0.4
npm WARN notsup Unsupported engine for @schematics/angular@15.0.4: wanted: {"node":"^14.20.0 || ^16.13.0 || >=18.10.0","npm":"^6.11.0 || ^7.5.6 || >=8.0.0","yarn":">= 1.13.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @schematics/angular@15.0.4
npm WARN notsup Unsupported engine for @angular-devkit/core@15.0.4: wanted: {"node":"^14.20.0 || ^16.13.0 || >=18.10.0","npm":"^6.11.0 || ^7.5.6 || >=8.0.0","yarn":">= 1.13.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @angular-devkit/core@15.0.4
npm WARN notsup Unsupported engine for npm-package-arg@10.1.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-package-arg@10.1.0
npm WARN notsup Unsupported engine for npm-install-checks@6.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-install-checks@6.0.0
npm WARN notsup Unsupported engine for npm-normalize-package-bin@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-normalize-package-bin@3.0.0
npm WARN notsup Unsupported engine for proc-log@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: proc-log@3.0.0
npm WARN notsup Unsupported engine for hosted-git-info@6.1.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hosted-git-info@6.1.1
npm WARN notsup Unsupported engine for validate-npm-package-name@5.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: validate-npm-package-name@5.0.0
npm WARN notsup Unsupported engine for npm-package-arg@10.1.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-package-arg@10.1.0
npm WARN notsup Unsupported engine for proc-log@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: proc-log@3.0.0
npm WARN notsup Unsupported engine for npm-registry-fetch@14.0.3: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-registry-fetch@14.0.3
npm WARN notsup Unsupported engine for cacache@17.0.4: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: cacache@17.0.4
npm WARN notsup Unsupported engine for npm-packlist@7.0.4: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-packlist@7.0.4
npm WARN notsup Unsupported engine for read-package-json-fast@3.0.2: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: read-package-json-fast@3.0.2
npm WARN notsup Unsupported engine for ssri@10.0.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: ssri@10.0.1
npm WARN notsup Unsupported engine for read-package-json@6.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: read-package-json@6.0.0
npm WARN notsup Unsupported engine for @npmcli/installed-package-contents@2.0.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @npmcli/installed-package-contents@2.0.1
npm WARN notsup Unsupported engine for @npmcli/promise-spawn@6.0.2: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @npmcli/promise-spawn@6.0.2
npm WARN notsup Unsupported engine for @npmcli/git@4.0.3: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @npmcli/git@4.0.3
npm WARN notsup Unsupported engine for @npmcli/run-script@6.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @npmcli/run-script@6.0.0
npm WARN notsup Unsupported engine for proc-log@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: proc-log@3.0.0
npm WARN notsup Unsupported engine for which@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: which@3.0.0
npm WARN notsup Unsupported engine for npm-bundled@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-bundled@3.0.0
npm WARN notsup Unsupported engine for @npmcli/node-gyp@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @npmcli/node-gyp@3.0.0
npm WARN notsup Unsupported engine for json-parse-even-better-errors@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: json-parse-even-better-errors@3.0.0
npm WARN notsup Unsupported engine for @npmcli/fs@3.1.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: @npmcli/fs@3.1.0
npm WARN notsup Unsupported engine for fs-minipass@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: fs-minipass@3.0.0
npm WARN notsup Unsupported engine for unique-filename@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: unique-filename@3.0.0
npm WARN notsup Unsupported engine for unique-slug@4.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: unique-slug@4.0.0
npm WARN notsup Unsupported engine for hosted-git-info@6.1.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hosted-git-info@6.1.1
npm WARN notsup Unsupported engine for validate-npm-package-name@5.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: validate-npm-package-name@5.0.0
npm WARN notsup Unsupported engine for ignore-walk@6.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: ignore-walk@6.0.0
npm WARN notsup Unsupported engine for npm-package-arg@10.1.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: npm-package-arg@10.1.0
npm WARN notsup Unsupported engine for minipass-fetch@3.0.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: minipass-fetch@3.0.1
npm WARN notsup Unsupported engine for proc-log@3.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: proc-log@3.0.0
npm WARN notsup Unsupported engine for make-fetch-happen@11.0.2: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: make-fetch-happen@11.0.2
npm WARN notsup Unsupported engine for hosted-git-info@6.1.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hosted-git-info@6.1.1
npm WARN notsup Unsupported engine for validate-npm-package-name@5.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: validate-npm-package-name@5.0.0
npm WARN notsup Unsupported engine for normalize-package-data@5.0.0: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: normalize-package-data@5.0.0
npm WARN notsup Unsupported engine for hosted-git-info@6.1.1: wanted: {"node":"^14.17.0 || ^16.13.0 || >=18.0.0"} (current: {"node":"12.22.10","npm":"6.14.16"})
npm WARN notsup Not compatible with your version of node/npm: hosted-git-info@6.1.1
npm WARN enoent ENOENT: no such file or directory, open '/home/egs/temp/v12/package.json'
npm WARN v12 No description
npm WARN v12 No repository field.
npm WARN v12 No README data
npm WARN v12 No license field.

+ @angular/cli@15.0.4
added 237 packages from 164 contributors and audited 237 packages in 9.17s

26 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

安裝 angualr/cli 産生錯誤的原因是因為安裝指令中未指定版本時，預設是要安裝最新版（現在這個時候最新版是 v15）。
Angular Cli 的版本與 Node.js 的版本必須要搭配合宜，譬如說 Angular Cli v15 就必須搭配 Node.js v14.20.0以上或 v16.13.0以上或 v18.10.0。

註：詳細的資料可參考 [Angular 與 Node.js 對照表](https://calvinegs.github.io/posts/ngcli-nodejs-verion/)

前述的問題，解決方法有二：
 - 方法一：不要使用最新版本的 Angular Cli，而採用可以搭配 Node.js 12 版本的 Cli(也就是 Angular Cli v13)
 - 方法二：更新 Node.js 的版本
 

### 安裝 Angular Cli v13 在 local 目錄中
```bash
$ npm install @angular/cli@13	# 安裝 cli 時指定特定版本
$
$ npx ng version

     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/
    

Angular CLI: 13.3.10
Node: 12.22.12
Package Manager: npm 6.14.16
OS: linux x64

Angular: 
... 

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.1303.10
@angular-devkit/core         13.3.10
@angular-devkit/schematics   13.3.10
@schematics/angular          13.3.10
```

### 使用 Angular Cli v13 建立 Angular App
```bash
$ ng new testApp --routing --style=scss && cd testApp
$ npm start
```


接下來要詳述方法二，也就是本篇筆記的重點。

將 Node.js 安裝在 global 環境可能是一種最簡潔的方式，但對開發人員而言，卻可能不是最佳解。

原因很簡單，因為開發人員可能要面對是不同專案、不同的開發工具、及各種不同版本環境...，將單一版本安裝在 global 中，衍生出來的版本切換問題可能造成更多不便。


## 使用 nvm 來管理系統中的 Node.js & npm 

(在 windows 環境中可以使用 nvs，請參考這篇　https://calvinegs.github.io/categories/nvs/)

在 ubuntu 中我們要用 wget 來進行 nvm 的安裝，所以首先先確認系統中已是否已安裝了 wget

```bash
$ wget --version

GNU Wget 1.21.2 built on linux-gnu.

-cares +digest -gpgme +https +ipv6 +iri +large-file -metalink +nls 
+ntlm +opie +psl +ssl/openssl 

Wgetrc: 
    /etc/wgetrc (system)
Locale: 
    /usr/share/locale 
Compile: 
    gcc -DHAVE_CONFIG_H -DSYSTEM_WGETRC="/etc/wgetrc" 
    -DLOCALEDIR="/usr/share/locale" -I. -I../../src -I../lib 
    -I../../lib -Wdate-time -D_FORTIFY_SOURCE=2 -DHAVE_LIBSSL -DNDEBUG 
    -g -O2 -ffile-prefix-map=/build/wget-8g5eYO/wget-1.21.2=. 
    -flto=auto -ffat-lto-objects -flto=auto -ffat-lto-objects 
    -fstack-protector-strong -Wformat -Werror=format-security 
    -DNO_SSLv2 -D_FILE_OFFSET_BITS=64 -g -Wall 
Link: 
    gcc -DHAVE_LIBSSL -DNDEBUG -g -O2 
    -ffile-prefix-map=/build/wget-8g5eYO/wget-1.21.2=. -flto=auto 
    -ffat-lto-objects -flto=auto -ffat-lto-objects 
    -fstack-protector-strong -Wformat -Werror=format-security 
    -DNO_SSLv2 -D_FILE_OFFSET_BITS=64 -g -Wall -Wl,-Bsymbolic-functions 
    -flto=auto -ffat-lto-objects -flto=auto -Wl,-z,relro -Wl,-z,now 
    -lpcre2-8 -luuid -lidn2 -lssl -lcrypto -lz -lpsl ftp-opie.o 
    openssl.o http-ntlm.o ../lib/libgnu.a 

Copyright (C) 2015 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later
<http://www.gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Originally written by Hrvoje Niksic <hniksic@xemacs.org>.
Please send bug reports and questions to <bug-wget@gnu.org>.
```


### 在 ubuntu 22.04 上安裝 nvm

使用 wget 下載最新稳定版本 nvm

```bash
$ wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

上述指令將 nvm repository 下載至 ~/.nvm 目錄中

```bash
$ srouce ~/.bashrc	# 在當前環境中執行 .bashrc script
$ command -v nvm	# 確認已安裝好 NVM
$ nvm -v			# 顯示目前安裝的 nvm 版本
0.39.1
```


### 使用 nvm 查詢 local 端 Node.js 目前安裝的情況

```bash
$ nvm ls
               
         system
iojs -> N/A (default)
node -> stable (-> N/A) (default)
unstable -> N/A (default)
```

### 使用 nvm 查詢 Node.js 目前所有版本清單

```bash
$ nvm ls-remote
		...
        v17.6.0
        v17.7.0
        v17.7.1
        v17.7.2
        v17.8.0
        v17.9.0
        v17.9.1
        v18.0.0
        v18.1.0
        v18.2.0
        v18.3.0
        v18.4.0
        v18.5.0
        v18.6.0
        v18.7.0
        v18.8.0
        v18.9.0
        v18.9.1
       v18.10.0
       v18.11.0
       v18.12.0   (LTS: Hydrogen)
->     v18.12.1   (Latest LTS: Hydrogen)
        v19.0.0
        v19.0.1
        v19.1.0
        v19.2.0
        v19.3.0
```


### 安裝最新版本 Node.js

```bash
$ nvm install node	# 未指定版本，即表示要安裝最新版本

Downloading and installing node v19.3.0...
Downloading https://nodejs.org/dist/v19.3.0/node-v19.3.0-linux-x64.tar.xz...
--2023-01-04 10:38:43--  https://nodejs.org/dist/v19.3.0/node-v19.3.0-linux-x64.tar.xz
Resolving nodejs.org (nodejs.org)... 104.20.22.46, 104.20.23.46, 2606:4700:10::6814:162e, ...
Connecting to nodejs.org (nodejs.org)|104.20.22.46|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23840516 (23M) [application/x-xz]
Saving to: ‘/home/calvin-egs/.nvm/.cache/bin/node-v19.3.0-linux-x64/node-v19.3.0-linux-x64.tar.xz’

/home/calvin-egs/.nvm/.cache/ 100%[===============================================>]  22.74M  5.32MB/s    in 4.6s    

2023-01-04 10:38:48 (4.96 MB/s) - ‘/home/calvin-egs/.nvm/.cache/bin/node-v19.3.0-linux-x64/node-v19.3.0-linux-x64.tar.xz’ saved [23840516/23840516]

Computing checksum with sha256sum
Checksums matched!
Now using node v19.3.0 (npm v9.2.0)
Creating default alias: default -> node (-> v19.3.0)
```

查看安裝情況

```bash
$ nvm ls			# 列出目前所有已安裝的 Node.js 版本
->      v19.3.0		# 目前只安裝了一個版本 v19.3.0
default -> node (-> v19.3.0)
iojs -> N/A (default)
unstable -> N/A (default)
node -> stable (-> v19.3.0) (default)
stable -> 19.3 (-> v19.3.0) (default)
lts/* -> lts/hydrogen (-> N/A)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.17.0 (-> N/A)
lts/dubnium -> v10.24.1 (-> N/A)
lts/erbium -> v12.22.12 (-> N/A)
lts/fermium -> v14.21.2 (-> N/A)
lts/gallium -> v16.19.0 (-> N/A)
lts/hydrogen -> v18.12.1 (-> N/A)
```

### 安裝特定版本的最更新版 Node.js

```bash
$ nvm install 18 # 安裝 version 18 版中的最更新版(如：v18.12.1)

Downloading and installing node v18.12.1...
Downloading https://nodejs.org/dist/v18.12.1/node-v18.12.1-linux-x64.tar.xz...
--2023-01-04 10:42:41--  https://nodejs.org/dist/v18.12.1/node-v18.12.1-linux-x64.tar.xz
Resolving nodejs.org (nodejs.org)... 104.20.23.46, 104.20.22.46, 2606:4700:10::6814:172e, ...
Connecting to nodejs.org (nodejs.org)|104.20.23.46|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23726988 (23M) [application/x-xz]
Saving to: ‘/home/calvin-egs/.nvm/.cache/bin/node-v18.12.1-linux-x64/node-v18.12.1-linux-x64.tar.xz’

/home/calvin-egs/.nvm/.cache/ 100%[===============================================>]  22.63M  5.10MB/s    in 4.8s    

2023-01-04 10:42:46 (4.68 MB/s) - ‘/home/calvin-egs/.nvm/.cache/bin/node-v18.12.1-linux-x64/node-v18.12.1-linux-x64.tar.xz’ saved [23726988/23726988]

Computing checksum with sha256sum
Checksums matched!
Now using node v18.12.1 (npm v8.19.2)
```

安裝成功後，再次查看目前安裝有那些版本，可以看到有 v18.12.1 與 v19.3.0，且目前使用的是 v18.12.1（ "->" 指示的即表示目前環境下所採用 Node.js 版本）

```bash
$ nvm ls

->     v18.12.1
        v19.3.0
default -> node (-> v19.3.0)
iojs -> N/A (default)
unstable -> N/A (default)
node -> stable (-> v19.3.0) (default)
stable -> 19.3 (-> v19.3.0) (default)
lts/* -> lts/hydrogen (-> v18.12.1)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.17.0 (-> N/A)
lts/dubnium -> v10.24.1 (-> N/A)
lts/erbium -> v12.22.12 (-> N/A)
lts/fermium -> v14.21.2 (-> N/A)
lts/gallium -> v16.19.0 (-> N/A)
lts/hydrogen -> v18.12.1```
```

## 如何不使用 Angular Cli 來新建一支 Angular Web App 程式範本

透過　npm init @angular　指令就可以不必先安裝 Angular Cli 的前題下來建立最新版本的 Angular Web App 程式範本

```bash
$ mkdir angular15 && cd angular15
$ node -v   # 先確認現行 Node.js 版本
v19.3.0


# 使用 npm init 指令
$ npm init @angular firstExample -- --routing --style=scss

Need to install the following packages:
  @angular/create@15.0.4
Ok to proceed? (y) 
npm WARN deprecated @npmcli/move-file@2.0.1: This functionality has been moved to @npmcli/fs
npm WARN deprecated sourcemap-codec@1.4.8: Please use @jridgewell/sourcemap-codec instead
Node.js version v19.3.0 detected.
Odd numbered Node.js versions will not enter LTS status and should not be used for production. For more information, please see https://nodejs.org/en/about/releases/.
? Would you like to share pseudonymous usage data about this project with the Angular Team
at Google under Google's Privacy Policy at https://policies.google.com/privacy. For more
details and how to change this setting, see https://angular.io/analytics. No
Global setting: disabled
Local setting: No local workspace configuration file.
Effective status: disabled
CREATE firstExample/README.md (1066 bytes)
CREATE firstExample/.editorconfig (274 bytes)
CREATE firstExample/.gitignore (548 bytes)
CREATE firstExample/angular.json (2949 bytes)
CREATE firstExample/package.json (1044 bytes)
CREATE firstExample/tsconfig.json (901 bytes)
CREATE firstExample/tsconfig.app.json (263 bytes)
CREATE firstExample/tsconfig.spec.json (273 bytes)
CREATE firstExample/.vscode/extensions.json (130 bytes)
CREATE firstExample/.vscode/launch.json (474 bytes)
CREATE firstExample/.vscode/tasks.json (938 bytes)
CREATE firstExample/src/favicon.ico (948 bytes)
CREATE firstExample/src/index.html (298 bytes)
CREATE firstExample/src/main.ts (214 bytes)
CREATE firstExample/src/styles.scss (80 bytes)
CREATE firstExample/src/assets/.gitkeep (0 bytes)
CREATE firstExample/src/app/app-routing.module.ts (245 bytes)
CREATE firstExample/src/app/app.module.ts (393 bytes)
CREATE firstExample/src/app/app.component.scss (0 bytes)
CREATE firstExample/src/app/app.component.html (23115 bytes)
CREATE firstExample/src/app/app.component.spec.ts (1091 bytes)
CREATE firstExample/src/app/app.component.ts (217 bytes)
✔ Packages installed successfully.
```

範本建立成功後，可查看 package.json 中採用的那個版本的 Angular 程式架構

```bash
$ cd firstExample
$ cat package.json

{
  "name": "first-example",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "^15.0.0",
    "@angular/common": "^15.0.0",
    "@angular/compiler": "^15.0.0",
    "@angular/core": "^15.0.0",
    "@angular/forms": "^15.0.0",
    "@angular/platform-browser": "^15.0.0",
    "@angular/platform-browser-dynamic": "^15.0.0",
    "@angular/router": "^15.0.0",
    "rxjs": "~7.5.0",
    "tslib": "^2.3.0",
    "zone.js": "~0.12.0"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "^15.0.4",
    "@angular/cli": "~15.0.4",
    "@angular/compiler-cli": "^15.0.0",
    "@types/jasmine": "~4.3.0",
    "jasmine-core": "~4.5.0",
    "karma": "~6.4.0",
    "karma-chrome-launcher": "~3.1.0",
    "karma-coverage": "~2.2.0",
    "karma-jasmine": "~5.1.0",
    "karma-jasmine-html-reporter": "~2.0.0",
    "typescript": "~4.8.2"
  }
}
```

執行産生的預設程式範本

```bash
$ npm start

> first-example@0.0.0 start
> ng serve

Node.js version v19.3.0 detected.
Odd numbered Node.js versions will not enter LTS status and should not be used for production. For more information, please see https://nodejs.org/en/about/releases/.
✔ Browser application bundle generation complete.

Initial Chunk Files   | Names         |  Raw Size
vendor.js             | vendor        |   2.09 MB | 
polyfills.js          | polyfills     | 314.28 kB | 
styles.css, styles.js | styles        | 209.87 kB | 
main.js               | main          |  48.10 kB | 
runtime.js            | runtime       |   6.52 kB | 

                      | Initial Total |   2.66 MB

Build at: 2023-01-04T02:54:06.004Z - Hash: ef1d647f832bd9c0 - Time: 11301ms

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **


✔ Compiled successfully.
```

## 讓特定目錄自動使用特定版本的 Node.js

最後來說說如何在切換目錄時由系統自動切換不同的 Node.js 版本。

### 修改 ~/.bashrc 檔案

首先，在 ~/.bashrc 檔案最後處加入以下 script。

```bash
### for nvm auto switch version on change directory
cdnvm() {
    command cd "$@";
    nvm_path=$(nvm_find_up .nvmrc | tr -d '\n')

    # If there are no .nvmrc file, use the default nvm version
    if [[ ! $nvm_path = *[^[:space:]]* ]]; then

        declare default_version;
        default_version=$(nvm version default);

        # If there is no default version, set it to `node`
        # This will use the latest version on your machine
        if [[ $default_version == "N/A" ]]; then
            nvm alias default node;
            default_version=$(nvm version default);
        fi

        # If the current version is not the default version, set it to use the default version
        if [[ $(nvm current) != "$default_version" ]]; then
            nvm use default;
        fi

    elif [[ -s $nvm_path/.nvmrc && -r $nvm_path/.nvmrc ]]; then
        declare nvm_version
        nvm_version=$(<"$nvm_path"/.nvmrc)

        declare locally_resolved_nvm_version
        # `nvm ls` will check all locally-available versions
        # If there are multiple matching versions, take the latest one
        # Remove the `->` and `*` characters and spaces
        # `locally_resolved_nvm_version` will be `N/A` if no local versions are found
        locally_resolved_nvm_version=$(nvm ls --no-colors "$nvm_version" | tail -1 | tr -d '\->*' | tr -d '[:space:]')

        # If it is not already installed, install it
        # `nvm install` will implicitly use the newly-installed version
        if [[ "$locally_resolved_nvm_version" == "N/A" ]]; then
            nvm install "$nvm_version";
        elif [[ $(nvm current) != "$locally_resolved_nvm_version" ]]; then
            nvm use "$nvm_version";
        fi
    fi
}
alias cd='cdnvm'
cd "$PWD"
```

### 讓修改的 ~/.bashrc Script file 即刻生效

修改完 ~/.bashrc Script file　後，使用 source 指令讓上述的 script 立即生效

```bash
$ source ~/.bashrc
```

### 搭配 .nvmrc 檔案實現在切換工作目錄時自動切換不同的 Node.js 版本

在　angular15 目錄下再建一個名為 angular 子目錄，在這個子目錄我們要使用的 node 環境是 v18。

```bash
$ mkdir angular && cd angular	# 建立 angular 子目錄，並將工作目錄切換到這個子目錄中
$
$ nvm use 18	# 使用 nvm use 指令將 Node.js 環境切換成 第18版，因為我們之前安裝的是 v18.12.1，nvm 自動採得這個最新版的第18版
$ node -v		# 查看目前使用的工作版本
v18.12.1
$ node -v > .nvmrc	# 將 v.18.12.1 這個版本寫入到 .nvmrc 這個檔案中。
```

nvm 搭配上各目錄中 .nvmrc 檔案的設定就能實現｀自動｀切換 Node.js 版本的功能。

```bash
$ cd ~
~$ node -v
v19.3.0
~$ cd angular15
~/angular15$ node -v
v19.3.0
~/angular15$ cd angualr
Now using node v18.12.1 (npm v8.19.2)
~/angular15/angular$ node -v
v18.12.1
```

使用 node v18.12.1 環境來建立 angualr 15 的 app
```bash
$ npm init @angular secondExample -- --routing --style=css
CREATE secondExample/README.md (1067 bytes)
CREATE secondExample/.editorconfig (274 bytes)
CREATE secondExample/.gitignore (548 bytes)
CREATE secondExample/angular.json (2780 bytes)
CREATE secondExample/package.json (1045 bytes)
CREATE secondExample/tsconfig.json (901 bytes)
CREATE secondExample/tsconfig.app.json (263 bytes)
CREATE secondExample/tsconfig.spec.json (273 bytes)
CREATE secondExample/.vscode/extensions.json (130 bytes)
CREATE secondExample/.vscode/launch.json (474 bytes)
CREATE secondExample/.vscode/tasks.json (938 bytes)
CREATE secondExample/src/favicon.ico (948 bytes)
CREATE secondExample/src/index.html (299 bytes)
CREATE secondExample/src/main.ts (214 bytes)
CREATE secondExample/src/styles.css (80 bytes)
CREATE secondExample/src/assets/.gitkeep (0 bytes)
CREATE secondExample/src/app/app-routing.module.ts (245 bytes)
CREATE secondExample/src/app/app.module.ts (393 bytes)
CREATE secondExample/src/app/app.component.css (0 bytes)
CREATE secondExample/src/app/app.component.html (23115 bytes)
CREATE secondExample/src/app/app.component.spec.ts (1094 bytes)
CREATE secondExample/src/app/app.component.ts (217 bytes)
✔ Packages installed successfully.
```

執行時也不會有任可版本不相搭配的警告訊息出現了。

```bash
$ npm start

> second-example@0.0.0 start
> ng serve

✔ Browser application bundle generation complete.

Initial Chunk Files   | Names         |  Raw Size
vendor.js             | vendor        |   2.09 MB | 
polyfills.js          | polyfills     | 314.28 kB | 
styles.css, styles.js | styles        | 209.41 kB | 
main.js               | main          |  48.11 kB | 
runtime.js            | runtime       |   6.52 kB | 

                      | Initial Total |   2.66 MB

Build at: 2023-01-04T03:09:19.398Z - Hash: 64669f1b8675c995 - Time: 13758ms

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **


✔ Compiled successfully.
```

結論：

- nvm 是管理你電腦中各式 Node.js 版本的好工具。
- 在新版本 Angular 中你可以不必先安裝 Cli 來建立程式範本，直接使用　npm init @angular 　指令即可。
