---
title: "使用 Docker 執行 PostgresSQL 與 pgAdmin"
date: 2023-02-08
description: "在開發的環境中我們常會需要搭配資料庫，而常見的資料庫管理系統種類、版本繁多，如果直接安裝在 local 端作業系統中，可能會造成開發環境混亂，其中採 Docker 方式來安裝測試用資料庫的方式是一個不錯的解決方案"
tags: ["mongodb", "mongodb compass", "docker", "docker compose"]
categories: ["Database"]
---

# 使用 Docker 執行 MongoDb

在之前的筆記中　*[使用 Node.js + express 建立一個電子商務後端服務的 REST API](https://calvinegs.github.io/posts/nodejs-restapi-example/)*　已有紀錄了使用 https://cloud.mongodb.com/ 線上的免費服務版本的 MonogoDb，本篇再把 Docker 版的 MongoDb 也一併紀錄下來。把如何使用 docker (docker compose) 來快速設置 MongoDb 及其管理工具 MongoDb Compass 的相關設定過程寫下來。

## 檢查是否已安裝了 Docker (docker version)
```bash
$ docker version

Client: Docker Engine - Community
 Version:           20.10.23
 API version:       1.41
 Go version:        go1.18.10
 Git commit:        7155243
 Built:             Thu Jan 19 17:36:25 2023
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.23
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.10
  Git commit:       6051f14
  Built:            Thu Jan 19 17:34:14 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.16
  GitCommit:        31aa4358a36870b21a992d3ad2bef29e1d693bec
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## 檢查是否已安裝了 docker compose

```bash
$ docker compose version

Docker Compose version v2.15.1
```

## 建置專案

### 目錄結構
```bash
$ mkdir mkdir mongodb && cd mongodb
$ touch init-mongo.js docker-compose.yml
``` 
```
./mongodb
├── docker-compose.yml
└── init-mongo.js
```

### 建立 docker-compose.yml

在這個 compse command file 中指令與說明如下：

```yml
version: '3.8'
services:
  db:
    container_name: mongo_db
    image: mongo
    restart: always
    environment:
      - MONGO_INITDB_DATABASE=mongo-db
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=<password>
    ports:
      - '27017-27019:27017-27019'
    volumes:
      - ./init-mongo.js:/docker-entrypoint-initdb.d/init-mongo.js:ro # 設置環境的設定檔
      - ./mongo-volume:/data/db　# 保存資料的目錄
```

### 建立 init-mongo.js

這用檔案的用途是給 docker compose 啟動時設置環境用。

```js
db.createUser(
  {
    user : "admin",
    pwd : "admin1911",
    roles : [
      {
      	role : "readWrite",
	db : "mongo-db"
      }    
    ]  
  }
)
```

## 執行 docker compose

上述的目錄與檔案內容準備完成後，在與 docker-compose.yml 的相同目錄中執行 `docker compose up　-d`來啟動 MongoDb Container Services，啟動成功後使用 ｀$ docker ps｀ 指令查看容器啟動狀況

```bash
$ docker compose up -d

# ...
[+] Running 2/0
 ⠿ Network mongodb_default  Created                                                                        0.0s
 ⠿ Container mongo_db       Created                                                                        0.0s
Attaching to mongo_db
...
```

查看 docker 執行的情況

```bash
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS          PORTS                                                                   NAMES
9d3924dcd94e   mongo     "docker-entrypoint.s…"   About a minute ago   Up 12 seconds   0.0.0.0:27017-27019->27017-27019/tcp, :::27017-27019->27017-27019/tcp   mongo_db
```

## 使用 MongoDb Compass 連結到 Mongo DB

打開 MongoDb Compass 應用程式

![image](https://user-images.githubusercontent.com/21993717/217527251-b34ab2e6-be6f-404a-ba89-50f4b9dcf247.png)


第一次開啟時要設定 Connections String 後按下 Connect 按鈕。Connection String 格式如：mongodb://<userid>:<password>@localhost:27017/?authMechanism=DEFAULT

![image](https://user-images.githubusercontent.com/21993717/217528377-b1fbcf07-0f40-46a4-b475-c46fa8593ece.png)


連結成功後，點選畫面下方的 'CREATE DATABASE'

![image](https://user-images.githubusercontent.com/21993717/217529377-28bd1ee5-bdf3-40c0-9b59-8633675c766d.png)

再輸入 Database Name　及 Collection Name(即 Document 或稱 Table Name)，按下"Create Database"

![image](https://user-images.githubusercontent.com/21993717/217530056-7162d413-0c44-4019-a607-e6a9860d5f47.png)

資料庫及表單 建立完成，如下圖。

![image](https://user-images.githubusercontent.com/21993717/217531155-e3345838-492e-440b-b339-175b7438d326.png)



## 新增資料

![image](https://user-images.githubusercontent.com/21993717/217531599-a066f8b9-875b-44c1-9eb7-a2170784e988.png)

要新增資料到 Collection 中，按下"ADD DATA"按鈕，可選擇二種方式來進行
 - Import File
 - Insert Document


### 方法一　Import File

匯入檔案的格式可以是 Json 或 CSV

![image](https://user-images.githubusercontent.com/21993717/217532635-b99e76e2-e988-49cd-8c4c-1bf40ea3ff65.png)


### 方法二　Insert Document

可直接在此功能中輸入 JSON 格式資料來新增資料到 collection 中。

![image](https://user-images.githubusercontent.com/21993717/217532903-05c80e95-153e-4021-8bd1-ecf9467edbdc.png)


```json
[
    {
        "imageName": "offer1",
        "title": "10% off on personal cares",
        "handsetCols": 2,
        "handsetRows": 1,
        "webCols": 2,
        "webRows": 1
    },
    {
        "imageName": "offer2",
        "title": "Flash sales in Footwears",
        "handsetCols": 2,
        "handsetRows": 1,
        "webCols": 1,
        "webRows": 1
    },
    {
        "imageName": "offer3",
        "title": "Extended warranty for Apple products",
        "handsetCols": 2,
        "handsetRows": 1,
        "webCols": 1,
        "webRows": 2
    },
    {
        "imageName": "offer4",
        "title": "5% discount for grocery",
        "handsetCols": 2,
        "handsetRows": 1,
        "webCols": 1,
        "webRows": 1
    }
]
```

新增成功畫面：

![image](https://user-images.githubusercontent.com/21993717/217533456-8bc9d6b4-8107-4889-a10d-99159a084c0b.png)


## 資料庫的實際檔案存放在 Local 的 ./pgdbdata 目錄中

使用作業系統管理者權限來查看此目錄

```bash
$ ls -al ./mongo-volume/
[總用量 528
drwxr-xr-x 5 systemd-coredump root              4096  2月  8 20:46 .
drwxrwxr-x 3 egs              egs               4096  2月  8 12:37 ..
-rw------- 1 systemd-coredump root                86  2月  8 12:54 .bash_history
-rw------- 1 systemd-coredump systemd-coredump 20480  2月  8 20:14 collection-0-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 20480  2月  8 20:45 collection-0-5598796086185130121.wt
-rw------- 1 systemd-coredump systemd-coredump 36864  2月  8 20:15 collection-2-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 36864  2月  8 20:34 collection-4-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 20480  2月  8 20:24 collection-7-3111193408736420755.wt
drwx------ 2 systemd-coredump systemd-coredump  4096  2月  8 20:47 diagnostic.data
-rw------- 1 systemd-coredump systemd-coredump 20480  2月  8 20:14 index-1-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 20480  2月  8 20:45 index-1-5598796086185130121.wt
-rw------- 1 systemd-coredump systemd-coredump 36864  2月  8 20:15 index-3-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 36864  2月  8 20:34 index-5-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 36864  2月  8 20:34 index-6-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 20480  2月  8 12:37 index-8-3111193408736420755.wt
-rw------- 1 systemd-coredump systemd-coredump 20480  2月  8 20:24 index-9-3111193408736420755.wt
drwx------ 2 systemd-coredump systemd-coredump  4096  2月  8 20:14 journal
-rw------- 1 systemd-coredump systemd-coredump 36864  2月  8 20:30 _mdb_catalog.wt
drwx------ 3 systemd-coredump systemd-coredump  4096  2月  8 12:37 .mongodb
-rw------- 1 systemd-coredump systemd-coredump     2  2月  8 20:14 mongod.lock
-rw------- 1 systemd-coredump systemd-coredump 36864  2月  8 20:46 sizeStorer.wt
-rw------- 1 systemd-coredump systemd-coredump   114  2月  8 12:37 storage.bson
-rw------- 1 systemd-coredump systemd-coredump    50  2月  8 12:37 WiredTiger
-rw------- 1 systemd-coredump systemd-coredump  4096  2月  8 20:14 WiredTigerHS.wt
-rw------- 1 systemd-coredump systemd-coredump    21  2月  8 12:37 WiredTiger.lock
-rw------- 1 systemd-coredump systemd-coredump  1475  2月  8 20:46 WiredTiger.turtle
-rw------- 1 systemd-coredump systemd-coredump 86016  2月  8 20:46 WiredTiger.wt
```