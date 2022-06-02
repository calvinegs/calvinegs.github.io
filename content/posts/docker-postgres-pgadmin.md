---
title: "使用 Docker 執行 PostgresSQL 與 pgAdmin"
date: 2022-06-01
description: "在開發的環境中我們常會需要搭配資料庫，而常見的資料庫管理系統種類、版本繁多，如果搭直接安裝在 local 端作業系統中，可能會造成開發環境混亂，其中採 Docker 方式來安裝測試用資料庫的方式是一個不錯的解決方案"
tags: ["PostgresSQL", "pgadmin", "docker", "docker compose", "Ubuntu"]
categories: ["Database"]
---


*[github Source code](https://github.com/calvinegs/angular13-form-validation.git)*

# 使用 Docker 執行 PostgresSQL 與 pgAdmin

對開發人員而言，使用各式的資料庫管理系統來當應用程式的後端資料存取層是家常便飯。若再考慮上每種資料庫管理系統有不同的版本，肯定會讓你的開發環境負擔過重、過於複雜。使用 Docker 來於適當的時機開啟適用的資料庫管理系統會是一個不錯的選擇。

本篇筆記紀錄使用 docker (docker compose) 來快速的設置 postgresSQL 及其管理工具 pgAdmin。

## 檢查是否已安裝了 Docker (docker version)
```bash
$ docker version

Client: Docker Engine - Community
 Version:           20.10.16
 API version:       1.41
 Go version:        go1.17.10
 Git commit:        aa7e414
 Built:             Thu May 12 09:17:23 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.16
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.10
  Git commit:       f756502
  Built:            Thu May 12 09:15:28 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.4
  GitCommit:        212e8b6fa2f44b9c21b2798135fc6fb7c53efc16
 runc:
  Version:          1.1.1
  GitCommit:        v1.1.1-0-g52de29d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## 檢查是否已安裝了 docker compose

```bash
$ docker compose version

Docker Compose version v2.5.0
```

## 建置專案

### 目錄結構
```bash
$ mkdir postgres-pgadmin && cd postgres-pgadmin
$ touch docker-compose.yml README.md
$ mkdir pgadmin pgdbdata sql
$ touch ./pgadmin/pgdb_servers.json
$ touch ./sql/initdb.sql
``` 
```
./postgres-pgadmin
├── docker-compose.yml
├── README.md
├── pgadmin
│   └── pgdb_servers.json
├── pgdbdata
└── sql
    └── initdb.sql
```

### 建立 docker-compose.yml

在這個 compse command file 中分別建立了二個 services，用來啟動 postgres 與 pgadmin。指令與說明如下

```yml
version: '3'
services:

  postgres:
    container_name: pg_server # 自定 container 名稱
    image: postgres:14.3      # 使用 postgres@14.3 docker image 檔
    restart: always
    ports:
    - "5432:5432"
    environment:
    - POSTGRES_USER=docker     # 建立一個 postgres user 
    - POSTGRES_PASSWORD=docker # 這個 user 的密碼
    volumes:  # 將 local 的 ./sql/initdb.sql 掛載到 postgres docker /docker-entrypoint-initdb.d/init.sql，目的是建立一個名為 pg_testdb 資料庫
      - ./sql/initdb.sql:/docker-entrypoint-initdb.d/init.sql 
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U docker"]
      interval: 10s
      timeout: 5s
      retries: 5

  pgadmin:
    container_name: pg_admin    # 自定 container 名稱
    image: dpage/pgadmin4:6.9   # 使用 pgadmin4＠6.9 docker image 檔
    depends_on:
      postgres:
        condition: service_healthy
    environment: 
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_DEFAULT_EMAIL:-user@domain.org}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_DEFAULT_PASSWORD:-docker}
      PGADMIN_CONFIG_SERVER_MODE: 'False'
      PGADMIN_SERVER_JSON_FILE: '/config/pgdb_servers.json'   # 使用這個 Json 檔案來設定 pgadmin 
    volumes:
      - ./pgadmin:/config   # 將 local 的 ./pgadmin 目錄掛載到 pgadmin4 docker 中
    ports: 
      - "5431:80"   # 將 pgadmin4 docker 中的 80 Port 映射到 local 端的 5431 Port
    restart: always
```

### 建立 pgdb_servers.json

這用檔案的用途是給 pgAdmin docker 啟動時設置環境用。

```json
{
    "Servers": {
        "1": {
            "Name": "pg-test",
            "Group": "Servers",
            "Port": 5432,
            "Username": "docker",
            "Host": "postgres",
            "SSLMode": "prefer",
            "MaintenanceDB": "postgres"
        }
    }
}
```

### 建立 initdb.sql

這用檔案的用途是給 postgres docker 啟動時透過 volumn 指令掛戴將此 SQL 檔案映射到 docker 內的 /docker-entrypoint-initdb.d/init.sql 檔，使得 docker initial 時會自動建立一個名為 pg_testdb 的資料庫。

```sql
CREATE DATABASE pg_testdb;
```

## 執行 docker compose

上述的目錄與檔案內容準備完成後，在與 docker-compose.yml 的相同目錄中執行 `docker compose up`來啟動這個二 Services，啟動成功後開啟另一個 terminal 使用 ｀$ docker ps｀ 指令查看容器啟動狀況

```bash
$ docker compose up

# ...
pg_server  | 2022-06-01 13:39:11.333 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
pg_server  | 2022-06-01 13:39:11.333 UTC [1] LOG:  listening on IPv6 address "::", port 5432
pg_server  | 2022-06-01 13:39:11.335 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
pg_server  | 2022-06-01 13:39:11.340 UTC [64] LOG:  database system was shut down at 2022-06-01 13:39:11 UTC
pg_server  | 2022-06-01 13:39:11.346 UTC [1] LOG:  database system is ready to accept connections
pg_admin   | NOTE: Configuring authentication for DESKTOP mode.
pg_admin   | ----------
pg_admin   | Loading servers with:
pg_admin   | User: pgadmin4@pgadmin.org
pg_admin   | SQLite pgAdmin config: /var/lib/pgadmin/pgadmin4.db
pg_admin   | ----------
pg_admin   | Added 0 Server Group(s) and 1 Server(s).
pg_admin   | [2022-06-01 13:39:48 +0000] [1] [INFO] Starting gunicorn 20.1.0
pg_admin   | [2022-06-01 13:39:48 +0000] [1] [INFO] Listening at: http://[::]:80 (1)
pg_admin   | [2022-06-01 13:39:48 +0000] [1] [INFO] Using worker: gthread
pg_admin   | [2022-06-01 13:39:48 +0000] [91] [INFO] Booting worker with pid: 91
```
> 查看 docker 執行的情況

```bash
$ docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS                    PORTS                                            NAMES
11bb5b91463c   dpage/pgadmin4:6.9   "/entrypoint.sh"         17 seconds ago   Up 5 seconds              443/tcp, 0.0.0.0:5431->80/tcp, :::5431->80/tcp   pg_admin
1dbca769db67   postgres:14.3        "docker-entrypoint.s…"   17 seconds ago   Up 16 seconds (healthy)   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp        pg_serve
```

## 使用 PgAdmin 連結到 Postgres DB

打開瀏覧器輸入： localhost:5431

第一次開啟時要先設定 pgAdmin 的密碼，輸入 pgAdmin 的密碼後按下 OK 按鈕。

![image](https://user-images.githubusercontent.com/21993717/171552013-445522d5-6007-47d6-a617-75e818b3791e.png)

接下來會顯示要連結到 pg-test 資料庫的使用者(本例中是 docker)的密碼輸入畫面，輸入密碼後按下 OK 按鈕。

![image](https://user-images.githubusercontent.com/21993717/171552411-1e3de4f7-598f-4ab6-a228-064d2dcd9fb9.png)

輸入密碼完成後，即可顯示資料庫的相關資訊：

![image](https://user-images.githubusercontent.com/21993717/171553231-750e85e7-669f-43bc-8371-333803bcc72e.png)


有關 pgadmin 更詳細資訊請 [打開][pgadmin]

[pgadmin]: https://www.pgadmin.org/docs/


## 使用 dbeaver-cd 連結到 Postgres DB

開啟 dbeaver-cd 程式，按下 ｀New Database Connection` 按鈕

![image](https://user-images.githubusercontent.com/21993717/171553528-ffb07bb4-1b4f-461d-8ced-eed86b0f8202.png)

開始設置連線資料設定，選擇資料庫 - PostgreSQL 按下 ｀Next`

![image](https://user-images.githubusercontent.com/21993717/171553828-60166d3b-79be-4000-9103-ae6d679f82f7.png)

輸入相關資訊，如 Database name、Username、Password...等，按下｀Test Connectiono...｀來測試設定是否成功。

![image](https://user-images.githubusercontent.com/21993717/171554089-7b293f24-0505-4577-9821-79067f17e844.png)

設定無誤會顯示 ｀已連結的訊息視窗｀。按下｀OK｀回到設置畫面，再按下 "Finish" 即可進入 DBeaver 資料庫管理功能中。

![2022-06-01 21-56-40](https://user-images.githubusercontent.com/21993717/171554329-5e37f3af-2ad6-4898-a4e5-640679902467.png)

![2022-06-01 21-57-08](https://user-images.githubusercontent.com/21993717/171554711-dfc5bf73-6e76-4a6a-8f46-93ac643270dd.png)


## 資料庫的實際檔案存放在 Local 的 ./pgdbdata 目錄中

使用作業系統管理者權限來查看此目錄

```bash
$ sudo ls -al ./pgdbdata/
[sudo] egs 的密碼：

總用量 132
drwx------ 19 systemd-coredump egs               4096  六   2 11:26 .
drwxrwxr-x  6 egs              egs               4096  六   2 10:23 ..
drwx------  7 systemd-coredump systemd-coredump  4096  六   2 10:27 base
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:28 global
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_commit_ts
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_dynshmem
-rw-------  1 systemd-coredump systemd-coredump  4821  六   2 10:27 pg_hba.conf
-rw-------  1 systemd-coredump systemd-coredump  1636  六   2 10:27 pg_ident.conf
drwx------  4 systemd-coredump systemd-coredump  4096  六   2 11:26 pg_logical
drwx------  4 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_multixact
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_notify
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_replslot
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_serial
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_snapshots
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 11:26 pg_stat
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 11:26 pg_stat_tmp
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_subtrans
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_tblspc
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_twophase
-rw-------  1 systemd-coredump systemd-coredump     3  六   2 10:27 PG_VERSION
drwx------  3 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_wal
drwx------  2 systemd-coredump systemd-coredump  4096  六   2 10:27 pg_xact
-rw-------  1 systemd-coredump systemd-coredump    88  六   2 10:27 postgresql.auto.conf
-rw-------  1 systemd-coredump systemd-coredump 28835  六   2 10:27 postgresql.conf
-rw-------  1 systemd-coredump systemd-coredump    36  六   2 10:27 postmaster.opts
```