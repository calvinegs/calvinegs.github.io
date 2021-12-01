---
title: "在 Github Pages 部署 Hugo 靜態網站"
date: 2021-12-01T17:17:02+08:00
draft: false
---

GitHub Page 功能是 GitHub 提供的一項支援靜態網頁的服務，只要在你的 repository 中加入一個名為 gh-pages 的分支，則 GitHub 便會預設將此分支內的檔案以靜態網頁的方式呈現，因此利用此特性就可很容易的製作一個簡單的網站。GitHub Pages 提供了二種不同形態的頁面 "User or organization site" & "Project Site"。其中個人/組織 網站一個 GitHub 帳號只能有一個、而專案網站則可以很多個。

> https://calvinegs.github.io #只有一個    
> https://calvinegs.github.io/project1  
> https://calvinegs.github.io/project2  
> https://calvinegs.github.io/project3  
> ...

## 安裝步驟

### Step 1: 在 GitHub 上建立一個 new repository

![image](https://user-images.githubusercontent.com/21993717/144195300-dba6a8fa-7ec6-495b-8109-fa66accd2553.png)

Repository name 若為 Owner_name.github.io 即所謂的 User site，Repository name 即為靜態網站的網址。以下圖為例，有效網址為 https://calvinegs.github.io

![image](https://user-images.githubusercontent.com/21993717/144207161-7c80f2e2-fd2e-43d2-820c-a5446032dfa3.png)

### Step 2: 使用 GitHub Action 構建 Hugo

透過 GitHub Action 功能，在你每次將新版本的 Hugo 網站內容推送到 GitHub repository 時，GitHub 將自動建置網站。

新增一個 .github/workflows/gh-pages.yml，內容如下:

```bash
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

### Step 3: GitHub Page 的設置

在第一次推送 Hugo 網站內容推送到 GitHub repository 時，透過 GitHub Action 的建置，將在你的 repository 中新新一個分支名稱叫 gh-pages。請在 GitHub Page 的設置功能中將它由 "Main" 指向 "gh-pages" 分支。

![image](https://user-images.githubusercontent.com/21993717/144203485-313c0ad2-e7a2-439c-9975-6979e712a52b.png)

### Step 4: 設置 Hugo 設定檔

將 Hugo 設定檔 config.toml 中的 baseURL 設定成 https://<mark>Owner_name</mark>.github.io。

```bash
baseurl = "https://calvinabba.github.io"
DefaultContentLanguage = "en"
title = "Beautiful Blog"
theme = "beautifulhugo"
```
