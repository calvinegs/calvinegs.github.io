---
title: "在 Windows 10 使用 Hugo"
date: 2021-11-27T11:51:00+08:00
draft: false
tags: ["install hugo", "windows 10"]
categories: ["hugo"]
ShowToc: true
TocOpen: false
---

<mark>Hugo</mark> 是一個静態網站生成器，搭配 git 很適合來建立個人部落格。在一系列的筆記中將介紹如何安裝/使用相關工具與技術，目的是搭建一個完全免費的個人部落格。

使用的相關工具/技術:
-    Hugo 
-    git
-    vs code
-    gist
-    github page
-    Markdown

##  Chocolatey ##
Chocolatey 是一個 Windows 上的套件管理工具，它結合了 Nuget 基礎服務與 powershell 指令，提供快速安裝應用程式與工具的服務，將安裝軟體的內容封裝到一個安裝包中，在 Chocolatey 中只需要一條簡單的指令就能完成搜尋、安裝、更新、解安裝等操作。

### 在 Windows 10 上安裝 Chocolatey 
在命令提示字元下執行以下指令
```shell
PS C:\>Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

### 移除 Chocolatey 

刪除 C:\ProgramData\chocolatey 目錄及移除環境變數即可，搜尋 "編輯系統環境變數" \ "進階" \ "環境變數"

##

## 安裝 hugo

### Step 1: 在 Windows 10 上使用 choco 安裝 hugo

```shell
C:\> choco install hugo -confirm
C:\> choco install hugo-extended - config
C:\> hugo version
```
![image](https://user-images.githubusercontent.com/21993717/144010547-49f3b2f2-08c8-4065-809c-598c512ac5b4.png)
### Step 2: Create a New Site (建立部落格網站)

```shell
C:\> hugo new site quickstart
C:\> hugo new site quickstart -f yml # 若要使用 config.yaml 為 config 設定檔，則加入額外參數
```

### Step 3: Add a Theme (新增部落格主題)
請先確認在您的作業系統中已經安裝了 git
```shell
C:\>cd quickstart
C:\>git init
C:\>git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
C:\> git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)

```

在 config.toml 中加入 theme = "ananke"

```shell
C:\>echo 'theme = "ananke"' >> config.toml
```

### Step 4: Add Some Content (建立新文章)

```shell
C:\>hugo new posts/my-first-post.md
```

在 md file 中加入以下內容

> \# Test Page  
> This is my first test page.

### Step 5: Start the Hugo server (本機測試)

```shell
C:\>hugo server -D
```
開啟瀏覽器，輸入網址 https://localhost:1313
---
