---
title: "使用 Docker Compose 執行 MSSQL Server 2022"
date: 2023-02-26
description: "在開發的環境中我們常會需要搭配資料庫，而常見的資料庫管理系統種類、版本繁多，如果搭直接安裝在 local 端作業系統中，可能會造成開發環境混亂，其中採 Docker 方式來安裝測試用資料庫的方式是一個不錯的解決方案"
tags: ["mssql", "docker", "docker compose", "Ubuntu"]
categories: ["Database"]
---

# 使用 Docker Compose 執行 MSSQL Server 2022

對開發人員而言，使用各式的資料庫管理系統來當應用程式的後端資料存取層是家常便飯。若再考慮上每種資料庫管理系統有不同的版本，肯定會讓你的開發環境負擔過重、過於複雜。使用 Docker 來於適當的時機開啟適用的資料庫管理系統會是一個不錯的選擇。

本篇筆記紀錄使用 docker (docker compose) 來快速的設置 MSSQL 2022。

## 檢查是否已安裝了 Docker (docker version)
```bash
$ docker version

Client: Docker Engine - Community
 Version:           23.0.1
 API version:       1.42
 Go version:        go1.19.5
 Git commit:        a5ee5b1
 Built:             Thu Feb  9 19:46:56 2023
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          23.0.1
  API version:      1.42 (minimum version 1.12)
  Go version:       go1.19.5
  Git commit:       bc3805a
  Built:            Thu Feb  9 19:46:56 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.18
  GitCommit:        2456e983eb9e37e47538f59ea18f2043c9a73640
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

Docker Compose version v2.16.0
```

# 使用 docker compose 來執行 MSSQL Server 2022

## 建置專案目錄結構
```bash
$ mkdir mssql && cd mssql
$ touch docker-compose.yml 
``` 

## 建立 docker-compose.yml

在這個 compose command file 中一個 services: mssql，用來下載並啟動 MSSQL Server 2022。指令與說明如下：

特別注意到 “command:” 這行的設定，它的用意是希望在第一次啟動這個 conatiner 時可以在 MSSQL 啟動成功後，自動建立一個名為 testdb 的資料庫，由於在 compose file 也有設定 volume，所以第一次啟動成功自動建立的 testdb 也會被永久保存在 local 資料庫，不會因為 docker 結束後而消失，所以這行指令可在成功建立 testdb 後把它註解掉。

```yml
version: '3.3'

services:
  mssql:
    container_name: sql-server-2022
    image: mcr.microsoft.com/mssql/server:2022-latest
    restart: always
    environment:
      ACCEPT_EULA: "Y"
      SA_PASSWORD: "<password>"   # 把 <password> 替換成你需要的密碼
    ports:
      - 1433:1433
    volumes:
      - volume2022:/var/opt/mssql2022  # 將 SQL Server 的 Data file 等映射到 local 的目錄中來，其中 ”volume2022“ 是 volume 名稱，而“/var/opt/mssql2022" 則為 server 端資料庫檔案放置的目錄名稱
    command: /bin/sh -c "(/opt/mssql/bin/sqlservr &) && sleep 10s && /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -d master -Q 'CREATE DATABASE [testdb]' && sleep infinity"    # 
      
volumes:
  volume2022:
```

## 執行 docker compose

上述的目錄與檔案內容準備完成後，在與 docker-compose.yml 的相同目錄中執行 `docker compose up -d`來啟動 db  Services，啟動成功後可使用 ｀$ docker ps｀ 指令查看容器啟動狀況

```bash
$ docker compose up 

sql-server-2022  | /opt/mssql/bin/permissions_check.sh: line 4: [: : integer expression expected
sql-server-2022  | /opt/mssql/bin/permissions_check.sh: line 59: [: : integer expression expected
sql-server-2022  | SQL Server 2022 will run as non-root by default.
sql-server-2022  | This container is running as user mssql.
sql-server-2022  | To learn more visit https://go.microsoft.com/fwlink/?linkid=2099216.
# ....
[+] Running 1/1
 ⠿ Container sql-server-2022  Stopped
```
### 查看 docker 執行的情況

```bash
$ docker ps

CONTAINER ID   IMAGE                                        COMMAND                   CREATED         STATUS         PORTS                                       NAMES
b9ec8aa9e83e   mcr.microsoft.com/mssql/server:2022-latest   "/opt/mssql/bin/perm…"   5 seconds ago   Up 5 seconds   0.0.0.0:1433->1433/tcp, :::1433->1433/tcp   sql-server-2022
```

## 使用 dbeaver-cd 連結到 MSSQL DB

開啟 dbeaver-cd 程式，按下 ｀New Database Connection` 按鈕

![image](https://user-images.githubusercontent.com/21993717/171553528-ffb07bb4-1b4f-461d-8ced-eed86b0f8202.png)

開始設置連線資料設定，選擇資料庫 - MSSQL 按下 ｀Next`

![image](https://user-images.githubusercontent.com/21993717/221390381-f5b594fc-b3f5-4589-aae3-11ec98b58714.png)


輸入相關資訊，如 Database name、Username、Password...等，按下｀Test Connectiono...｀來測試設定是否成功。

![image](https://user-images.githubusercontent.com/21993717/221390781-071d5710-2e45-4566-bab9-9da975156e8e.png)

![image](https://user-images.githubusercontent.com/21993717/221390486-9687b90e-2fe9-43b0-8329-3713e9e9ceae.png)

![image](https://user-images.githubusercontent.com/21993717/221390547-5ee2f943-6bea-47e5-a1ea-4707ef865133.png)

## 在 testdb 中建立資料表

```sql
use testdb;
CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT);
INSERT INTO Inventory VALUES (1, 'banana', 150); 
INSERT INTO Inventory VALUES (2, 'orange', 154);

```
![image](https://user-images.githubusercontent.com/21993717/221390870-c7866daf-4edd-4a72-9103-359ff4f08b79.png)

![image](https://user-images.githubusercontent.com/21993717/221390906-dc30b92f-d37c-4246-b735-cb2c1d34556c.png)


## 查看 volume 相關資訊

```bash
$ docker volume inspect mssql2022_volume2022
[
    {
        "CreatedAt": "2023-02-26T11:12:47+08:00",
        "Driver": "local",
        "Labels": {
            "com.docker.compose.project": "mssql2022",
            "com.docker.compose.version": "2.16.0",
            "com.docker.compose.volume": "volume2022"
        },
        "Mountpoint": "/var/lib/docker/volumes/mssql2022_volume2022/_data",
        "Name": "mssql2022_volume2022",
        "Options": null,
        "Scope": "local"
    }
]
```
## 資料庫的實際檔案存放在 Local 的 /var/opt/mssql 目錄中

使用作業系統管理者權限來查看此目錄

```bash
$ sudo ls -al /var/lib/docker/volumes/mssql2022_volume2022/_data
總用量 24
drwxrwx--- 6 root  root 4096  2月 26 11:12 .
drwx-----x 3 root  root 4096  2月 26 11:12 ..
drwxr-xr-x 2 10001 root 4096  2月 26 11:12 data
drwxr-xr-x 2 10001 root 4096  2月 26 11:12 log
drwxr-xr-x 2 10001 root 4096  2月 26 11:12 secrets
drwxr-xr-x 5 10001 root 4096  2月 26 11:12 .system
```

## 重新啟動 docker compose

關閉 docker 

```bash
$ docker compose down
[+] Running 2/2
 ⠿ Container sql-server-2022  Removed                                                                                                                                      10.5s
 ⠿ Network mssql2022_default  Removed        
```

重新使用 `docker compose up -d` 來啟動 MSSQL Server 2022 container。

提醒： compose file 中“command:” 這行的設定記得把它註解掉！

啟動成功後，再開啟 DBeaver 會發現前面新建立的 Inventory Table 及 資料都還存在！
