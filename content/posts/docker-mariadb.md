---
title: "使用 Docker 執行 Mariadb"
date: 2022-07-07
description: "在開發的環境中我們常會需要搭配資料庫，而常見的資料庫管理系統種類、版本繁多，如果搭直接安裝在 local 端作業系統中，可能會造成開發環境混亂，其中採 Docker 方式來安裝測試用資料庫的方式是一個不錯的解決方案"
tags: ["mariadb", "docker", "docker compose", "Ubuntu"]
categories: ["Database"]
---

# 使用 Docker 執行 Mariadb

對開發人員而言，使用各式的資料庫管理系統來當應用程式的後端資料存取層是家常便飯。若再考慮上每種資料庫管理系統有不同的版本，肯定會讓你的開發環境負擔過重、過於複雜。使用 Docker 來於適當的時機開啟適用的資料庫管理系統會是一個不錯的選擇。

本篇筆記紀錄使用 docker (docker compose) 來快速的設置 Mariadb。

## 檢查是否已安裝了 Docker (docker version)
```bash
$ docker version

Client: Docker Engine - Community
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.17.11
 Git commit:        100c701
 Built:             Mon Jun  6 23:02:57 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.17
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.11
  Git commit:       a89b842
  Built:            Mon Jun  6 23:01:03 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.6
  GitCommit:        10c12954828e7c7c9b6e0ea9b0c02b01407d3ae1
 runc:
  Version:          1.1.2
  GitCommit:        v1.1.2-0-ga916309
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## 檢查是否已安裝了 docker compose

```bash
$ docker compose version

Docker Compose version v2.6.0
```

# 使用 docker run 來執行 Mariadb 10.8.3

參數說明：
 --name: docker name
 --rm: 停止時移除 container
 -v: 將 container 中的 Mariadb 資料映射到 local 端的目錄(/tmp/mariadb)中
 -e: 環境變數設定, MARIADB_ROOT_PASSWORD 指的是 root 帳號的密碼，MARIADB_DATABASE 指的是資料庫名稱
 -p: Port number

```bash
$ docker run --name mdb --rm \
-v /tmp/mariadb:/var/lib/mysql \
-e MARIADB_ROOT_PASSWORD=Password123! \
-e MARIADB_DATABASE=testing \
-p 3306:3306 \
-it mariadb:10.8.3

$ docker exec -it mdb mariadb --user root -pPassword123!

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 10.8.3-MariaDB-1:10.8.3+maria~jammy mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>

# 建立一個資料庫
MariaDB [(none)]> CREATE DATABASE mariadb_is_awesome;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]>
```

## 查看 docker 執行的情況

```bash
$ docker ps

CONTAINER ID   IMAGE            COMMAND                  CREATED              STATUS              PORTS                                       NAMES
f42da79c0a72   mariadb:10.8.3   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mdb
```

## 查看 local Mariadb 資料庫目錄 (/tmp/mariadb)

```bash
$ lm /tmp/mariadb/
總用量 139276
drwxr-xr-x  7 systemd-coredump systemd-coredump      4096  七   7 18:04 .
drwxrwxrwt 27 root             root                 12288  七   7 18:06 ..
-rw-rw----  1 systemd-coredump systemd-coredump  16719872  七   7 18:03 aria_log.00000001
-rw-rw----  1 systemd-coredump systemd-coredump        52  七   7 18:03 aria_log_control
-rw-rw----  1 systemd-coredump systemd-coredump         9  七   7 18:03 ddl_recovery.log
-rw-rw----  1 systemd-coredump systemd-coredump       868  七   7 18:03 ib_buffer_pool
-rw-rw----  1 systemd-coredump systemd-coredump  12582912  七   7 18:03 ibdata1
-rw-rw----  1 systemd-coredump systemd-coredump 100663296  七   7 18:06 ib_logfile0
-rw-rw----  1 systemd-coredump systemd-coredump  12582912  七   7 18:03 ibtmp1
drwx------  2 systemd-coredump systemd-coredump      4096  七   7 18:04 mariadb_is_awesome
-rw-rw----  1 systemd-coredump systemd-coredump         0  七   7 18:03 multi-master.info
drwx------  2 systemd-coredump systemd-coredump      4096  七   7 18:03 mysql
-rw-r--r--  1 systemd-coredump systemd-coredump        14  七   7 18:03 mysql_upgrade_info
drwx------  2 systemd-coredump systemd-coredump      4096  七   7 18:03 performance_schema
drwx------  2 systemd-coredump systemd-coredump     12288  七   7 18:03 sys
drwx------  2 systemd-coredump systemd-coredump      4096  七   7 18:03 testing
```

## 使用 dbeaver-cd 連結到 Mariadb

開啟 dbeaver-cd 程式，按下 ｀New Database Connection` 按鈕

![image](https://user-images.githubusercontent.com/21993717/171553528-ffb07bb4-1b4f-461d-8ced-eed86b0f8202.png)

開始設置連線資料設定，選擇資料庫 - Mariadb 按下 ｀Next`

![image](https://user-images.githubusercontent.com/21993717/177749049-eda8d6b5-f443-4a8e-a0b8-a715f3389ed7.png)

輸入相關資訊，如 Database name、Username、Password...等，按下｀Test Connectiono...｀來測試設定是否成功。

![image](https://user-images.githubusercontent.com/21993717/177750629-8a11d50f-314c-4343-9a42-0c4a03ff3d9f.png)

設定無誤會顯示 ｀已連結的訊息視窗｀。按下｀OK｀回到設置畫面，再按下 "Finish" 即可進入 DBeaver 資料庫管理功能中。

![image](https://user-images.githubusercontent.com/21993717/177751095-c96c9e0f-0715-4c23-977b-6cc418aeb513.png)

# 使用 docker compose 來執行 Mariadb （10.8.3）

## 建置專案目錄結構
```bash
$ mkdir mariadb && cd mariadb
$ touch docker-compose.yml README.md
$ mkdir sql
$ touch ./sql/init.sql
``` 
```
./mariadb
├── docker-compose.yml
├── README.md
└── sql
    └── init.sql
```

## 建立 docker-compose.yml

在這個 compse command file 中一個 services: db，用來下載並啟動 Mariadb。指令與說明如下

```yml
version: '3.8'
services:
  db:
    container_name: maria_db    # container name
    image: mariadb:10.8.3    # image name
    cap_add:    # 將　cap_add　設定為 SYS_NICE會抑制一些無用的錯誤消息
      - SYS_NICE
    restart: always # 當發生系統錯誤時永遠都會重啟
    environment:
      - MARIADB_DATABASE=testdb   # 預設資料庫名稱
      - MARIADB_ROOT_PASSWORD=mariadb@12345 # root 密碼
    ports:
      - '3306:3306'
    volumes:
      - /tmp/mariadb:/var/lib/mariadb # 將 local /tmp/mariadb 映射成 Mariadb 實際資料庫檔案
      #- db:/var/lib/mariadb  # 若以此語法將可搭配下面 volumes 的額外參數來指定實際資料庫檔案存放的位置，預設是 /var/lib/docker/volumes/mariadb/_data
      - ./sql:/docker-entrypoint-initdb.d # 在 local 目錄中可置入相關的 sql 檔案 到initdb.d 目錄下來進行資料庫資料 initial 的動作。
#volumes:
#  db:
#    driver: local
```

## 透過 init.sql 來建立一個 Table

在 init.sql 檔案中輸入以下 SQL 語法。這個檔案的內容會在 docker 啟動成功後被執行。

```sql
use testdb;

CREATE TABLE IF NOT EXISTS tasks (
  task_id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  start_date DATE,
  due_date DATE,
  status TINYINT NOT NULL,
  priority TINYINT NOT NULL,
  description TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)  ENGINE=INNODB;
```

## 執行 docker compose

上述的目錄與檔案內容準備完成後，在與 docker-compose.yml 的相同目錄中執行 `docker compose up -d`來啟動 db  Services，啟動成功後，在 terminal 使用 ｀$ docker ps｀ 指令查看容器啟動狀況

```bash
$ docker compose up -d
[+] Running 2/2
 ⠿ Network mariadb_default  Created                                                                                                                  0.0s
 ⠿ Container maria_db       Started                                                                                                                  0.5s
```

### 查看 docker 執行的情況

```bash
$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                                       NAMES
1db21d9e4eab   mariadb:10.8.3   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   maria_db
e
```

## 使用 dbeaver-cd 連結到 Mariadb DB
設置連線資料設定後開始資料庫會發現已自動新建立一個 task 的 Table了。

![image](https://user-images.githubusercontent.com/21993717/177753467-e408bb85-4ced-4075-b116-c12d8bc71aca.png)


## 資料庫的實際檔案存放在 Local 的 /tmp/mariadb 目錄中

使用作業系統管理者權限來查看此目錄

```bash
$ ls -al /tmp/mariadb/
總用量 139272
drwxr-xr-x  6 systemd-coredump systemd-coredump      4096  七   7 18:26 .
drwxrwxrwt 27 root             root                 12288  七   7 18:31 ..
-rw-rw----  1 systemd-coredump systemd-coredump  16719872  七   7 18:26 aria_log.00000001
-rw-rw----  1 systemd-coredump systemd-coredump        52  七   7 18:26 aria_log_control
-rw-rw----  1 systemd-coredump systemd-coredump         9  七   7 18:26 ddl_recovery.log
-rw-rw----  1 systemd-coredump systemd-coredump       884  七   7 18:26 ib_buffer_pool
-rw-rw----  1 systemd-coredump systemd-coredump  12582912  七   7 18:26 ibdata1
-rw-rw----  1 systemd-coredump systemd-coredump 100663296  七   7 18:28 ib_logfile0
-rw-rw----  1 systemd-coredump systemd-coredump  12582912  七   7 18:26 ibtmp1
-rw-rw----  1 systemd-coredump systemd-coredump         0  七   7 18:26 multi-master.info
drwx------  2 systemd-coredump systemd-coredump      4096  七   7 18:26 mysql
-rw-r--r--  1 systemd-coredump systemd-coredump        14  七   7 18:26 mysql_upgrade_info
drwx------  2 systemd-coredump systemd-coredump      4096  七   7 18:26 performance_schema
drwx------  2 systemd-coredump systemd-coredump     12288  七   7 18:26 sys
drwx------  2 systemd-coredump systemd-coredump      4096  七   7 18:26 testdb
```

## 停止 Docker 關閉資料庫
```bash
$ docker compose down
[+] Running 2/2
 ⠿ Container maria_db       Removed                                                                                                     0.5s
 ⠿ Network mariadb_default  Removed                                                                                                     0.2s
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
