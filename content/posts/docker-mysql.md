---
title: "使用 Docker 執行 MySQL"
date: 2022-06-17
description: "在開發的環境中我們常會需要搭配資料庫，而常見的資料庫管理系統種類、版本繁多，如果搭直接安裝在 local 端作業系統中，可能會造成開發環境混亂，其中採 Docker 方式來安裝測試用資料庫的方式是一個不錯的解決方案"
tags: ["mysql", "docker", "docker compose", "Ubuntu"]
categories: ["Database"]
---

# 使用 Docker 執行 MySQL

對開發人員而言，使用各式的資料庫管理系統來當應用程式的後端資料存取層是家常便飯。若再考慮上每種資料庫管理系統有不同的版本，肯定會讓你的開發環境負擔過重、過於複雜。使用 Docker 來於適當的時機開啟適用的資料庫管理系統會是一個不錯的選擇。

本篇筆記紀錄使用 docker (docker compose) 來快速的設置 MySQL。

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

# 使用 docker run 來執行 MySQL 8.0

參數說明：
 --name: docker name
 --rm: 停止時移除 container
 -v: 將 container 中的 MySQL 資料映射到 local 端的目錄(/tmp/mysqldb)中
 -e: 環境變數設定, MYSQL_ROOT_PASSWORD 指的是 root 帳號的密碼，MYSQL_DATABASE 指的是資料庫名稱
 -p: Port number

```bash
$ mkdir /tmp/mysqldb
$ docker run --name mysql-test --rm \
-v /tmp/mysqldb:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=mysql@12345 \
-e MYSQL_DATABASE=testing \
-p 3306:3306 -it mysql:8.0

2022-06-17 04:45:36+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.29-1debian10 started.
2022-06-17 04:45:36+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2022-06-17 04:45:36+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.29-1debian10 started.
2022-06-17T04:45:37.264452Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.29) starting as process 1
2022-06-17T04:45:37.270146Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-06-17T04:45:37.445525Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-06-17T04:45:37.644757Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2022-06-17T04:45:37.644831Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2022-06-17T04:45:37.646485Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2022-06-17T04:45:37.667321Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.29'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
2022-06-17T04:45:37.667312Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
```

## 查看 docker 執行的情況

```bash
$ docker ps

CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
986d25a3e618   mysql:8.0   "docker-entrypoint.s…"   37 seconds ago   Up 36 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-tes
```

## 查看 local MySQL 資料庫目錄 (/tmp/mysqldb)

```bash
$ lm /tmp/mysqldb/
總用量 198088
drwxrwxr-x  7 systemd-coredump egs                  4096  六  17 15:45  .
drwxrwxrwt 26 root             root                12288  六  17 15:45  ..
-rw-r-----  1 systemd-coredump systemd-coredump       56  六  17 15:45  auto.cnf
-rw-r-----  1 systemd-coredump systemd-coredump  3117132  六  17 15:45  binlog.000001
-rw-r-----  1 systemd-coredump systemd-coredump      157  六  17 15:45  binlog.000002
-rw-r-----  1 systemd-coredump systemd-coredump       32  六  17 15:45  binlog.index
-rw-------  1 systemd-coredump systemd-coredump     1680  六  17 15:45  ca-key.pem
-rw-r--r--  1 systemd-coredump systemd-coredump     1112  六  17 15:45  ca.pem
-rw-r--r--  1 systemd-coredump systemd-coredump     1112  六  17 15:45  client-cert.pem
-rw-------  1 systemd-coredump systemd-coredump     1680  六  17 15:45  client-key.pem
-rw-r-----  1 systemd-coredump systemd-coredump     1382  六  17 15:45  d7ac1e06b7cb.err
-rw-r-----  1 systemd-coredump systemd-coredump   196608  六  17 15:45 '#ib_16384_0.dblwr'
-rw-r-----  1 systemd-coredump systemd-coredump  8585216  六  17 15:45 '#ib_16384_1.dblwr'
-rw-r-----  1 systemd-coredump systemd-coredump     5683  六  17 15:45  ib_buffer_pool
-rw-r-----  1 systemd-coredump systemd-coredump 12582912  六  17 15:45  ibdata1
-rw-r-----  1 systemd-coredump systemd-coredump 50331648  六  17 15:45  ib_logfile0
-rw-r-----  1 systemd-coredump systemd-coredump 50331648  六  17 15:45  ib_logfile1
-rw-r-----  1 systemd-coredump systemd-coredump 12582912  六  17 15:45  ibtmp1
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 15:45 '#innodb_temp'
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 15:45  mysql
-rw-r-----  1 systemd-coredump systemd-coredump 31457280  六  17 15:45  mysql.ibd
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 15:45  performance_schema
-rw-------  1 systemd-coredump systemd-coredump     1680  六  17 15:45  private_key.pem
-rw-r--r--  1 systemd-coredump systemd-coredump      452  六  17 15:45  public_key.pem
-rw-r--r--  1 systemd-coredump systemd-coredump     1112  六  17 15:45  server-cert.pem
-rw-------  1 systemd-coredump systemd-coredump     1676  六  17 15:45  server-key.pem
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 15:45  sys
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 15:45  testing
-rw-r-----  1 systemd-coredump systemd-coredump 16777216  六  17 15:45  undo_001
-rw-r-----  1 systemd-coredump systemd-coredump 16777216  六  17 15:45  undo_002
```

## 使用 dbeaver-cd 連結到 MySQL

開啟 dbeaver-cd 程式，按下 ｀New Database Connection` 按鈕

![image](https://user-images.githubusercontent.com/21993717/171553528-ffb07bb4-1b4f-461d-8ced-eed86b0f8202.png)

開始設置連線資料設定，選擇資料庫 - MySQL 按下 ｀Next`

![image](https://user-images.githubusercontent.com/21993717/171553828-60166d3b-79be-4000-9103-ae6d679f82f7.png)

輸入相關資訊，如 Database name、Username、Password...等，按下｀Test Connectiono...｀來測試設定是否成功。

![image](https://user-images.githubusercontent.com/21993717/174239485-eb5240d1-d7b0-4dfa-b663-789779bb41ba.png)

若出現以下錯誤,請回到設置畫面的 "Driver properties" 頁簽中去修改 "allowPublicKeyRetrieval" 的值。

![image](https://user-images.githubusercontent.com/21993717/174227825-327906b4-a0bf-4869-b6f9-0ff57fe5fd04.png)

![2022-06-17 12-57-27](https://user-images.githubusercontent.com/21993717/174276078-88ef52cb-c38e-48a4-ad85-1589d2a987d0.png)

將"allowPublicKeyRetrieval" 的值改成 True。按下 "Finish"，回到設置主頁面，再按下 Test Connection 測試一次。

![image](https://user-images.githubusercontent.com/21993717/174239919-4ba22da9-7640-4744-a044-26b01b1b80ad.png)

設定無誤會顯示 ｀已連結的訊息視窗｀。按下｀OK｀回到設置畫面，再按下 "Finish" 即可進入 DBeaver 資料庫管理功能中。

![2022-06-17 14-38-52](https://user-images.githubusercontent.com/21993717/174240215-ecc7430f-4929-4507-bf84-c4f6c14e64cb.png)

# 使用 docker compose 來執行 MySQL 8.0

## 建置專案目錄結構
```bash
$ mkdir mysql && cd mysql
$ touch docker-compose.yml README.md
$ mkdir ./db ./db/initdb.sql
``` 
```
./mysql
├── docker-compose.yml
├── README.md
└── db
    └── initdb.sql
```

## 建立 docker-compose.yml

在這個 compse command file 中一個 services: db，用來下載並啟動 MySQL。指令與說明如下

```yml
version: '3.8'
services:
  db:
    container_name: mysql_db    # container name
    image: mysql:8.0    # image name
    cap_add:    # 將　cap_add　設定為 SYS_NICE會抑制一些無用的錯誤消息
      - SYS_NICE
    restart: always # 當發生系統錯誤時永遠都會重啟
    environment:
      - MYSQL_DATABASE=testdb   # 預設資料庫名稱
      - MYSQL_ROOT_PASSWORD=mysql@12345 # root 密碼
    ports:
      - '3306:3306'
    volumes:
      - /tmp/mysqldb:/var/lib/mysql # 將 local /tmp/mysqldb 映射成 MySQL 實際資料庫檔案
      #- db:/var/lib/mysql  # 若以此語法將可搭配下面 volumes 的額外參數來指定實際資料庫檔案存放的位置，預設是 /var/lib/docker/volumes/mysqldb/_data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql # 在 local 目錄中可置入相關的 sql 檔案來進行資料庫資料 initial 的動作。
#volumes:
#  db:
#    driver: local
```

## 執行 docker compose

上述的目錄與檔案內容準備完成後，在與 docker-compose.yml 的相同目錄中執行 `docker compose up`來啟動 db  Services，啟動成功後開啟另一個 terminal 使用 ｀$ docker ps｀ 指令查看容器啟動狀況

```bash
$ docker compose up

# ...
 ⠿ Container mysql_db  Created                                                                                                                                  0.0s
Attaching to mysql_db
mysql_db  | 2022-06-17 08:23:58+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.29-1debian10 started.
mysql_db  | 2022-06-17 08:23:58+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
mysql_db  | 2022-06-17 08:23:58+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.29-1debian10 started.
mysql_db  | 2022-06-17 08:23:58+00:00 [Note] [Entrypoint]: Initializing database files
mysql_db  | 2022-06-17T08:23:58.480385Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.29) initializing of server in progress as process 43
mysql_db  | 2022-06-17T08:23:58.486258Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
mysql_db  | 2022-06-17T08:23:58.797788Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
mysql_db  | 2022-06-17T08:23:59.921507Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
mysql_db  | 2022-06-17 08:24:01+00:00 [Note] [Entrypoint]: Database files initialized
mysql_db  | 2022-06-17 08:24:01+00:00 [Note] [Entrypoint]: Starting temporary server
mysql_db  | 2022-06-17T08:24:02.084039Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.29) starting as process 92
mysql_db  | 2022-06-17T08:24:02.107066Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
mysql_db  | 2022-06-17T08:24:02.294283Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
mysql_db  | 2022-06-17T08:24:02.522288Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
mysql_db  | 2022-06-17T08:24:02.522366Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
mysql_db  | 2022-06-17T08:24:02.524085Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
mysql_db  | 2022-06-17T08:24:02.545272Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.29'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
mysql_db  | 2022-06-17T08:24:02.545306Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
mysql_db  | 2022-06-17 08:24:02+00:00 [Note] [Entrypoint]: Temporary server started.
mysql_db  | Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
mysql_db  | Warning: Unable to load '/usr/share/zoneinfo/leap-seconds.list' as time zone. Skipping it.
mysql_db  | Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
mysql_db  | Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
mysql_db  | 2022-06-17 08:24:04+00:00 [Note] [Entrypoint]: Creating database testdb
mysql_db  | 
mysql_db  | 2022-06-17 08:24:04+00:00 [Note] [Entrypoint]: /usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/init.sql
mysql_db  | ERROR: Can't initialize batch_readline - may be the input source is a directory or a block device.
mysql_db exited with code 1
mysql_db  | 2022-06-17 08:24:05+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
mysql_db  | 2022-06-17 08:24:05+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.29-1debian10 started.
mysql_db  | 2022-06-17T08:24:05.567299Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.29) starting as process 1
mysql_db  | 2022-06-17T08:24:05.573086Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
mysql_db  | 2022-06-17T08:24:06.719902Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
mysql_db  | 2022-06-17T08:24:06.853179Z 0 [System] [MY-010229] [Server] Starting XA crash recovery...
mysql_db  | 2022-06-17T08:24:06.860721Z 0 [System] [MY-010232] [Server] XA crash recovery finished.
mysql_db  | 2022-06-17T08:24:06.953529Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
mysql_db  | 2022-06-17T08:24:06.953559Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
mysql_db  | 2022-06-17T08:24:06.954832Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
mysql_db  | 2022-06-17T08:24:06.973370Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.29'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
mysql_db  | 2022-06-17T08:24:06.973520Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
```
### 查看 docker 執行的情況

```bash
$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED              STATUS              PORTS                                                  NAMES
d081aaf01c4e   mysql:8.0   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql_db
```

## 使用 dbeaver-cd 連結到 MySQL DB

開啟 dbeaver-cd 程式，按下 ｀New Database Connection` 按鈕

![image](https://user-images.githubusercontent.com/21993717/171553528-ffb07bb4-1b4f-461d-8ced-eed86b0f8202.png)

開始設置連線資料設定，選擇資料庫 - MySQL 按下 ｀Next`

![image](https://user-images.githubusercontent.com/21993717/171553828-60166d3b-79be-4000-9103-ae6d679f82f7.png)

輸入相關資訊，如 Database name、Username、Password...等，按下｀Test Connectiono...｀來測試設定是否成功。

![image](https://user-images.githubusercontent.com/21993717/174261098-914b0b76-8d4e-4920-a6e2-c0c6c8181dd3.png)

![2022-06-17 16-37-35](https://user-images.githubusercontent.com/21993717/174261248-ce135c8c-bf67-49d6-bc38-6f09aaf4ed18.png)

![2022-06-17 16-37-49](https://user-images.githubusercontent.com/21993717/174261337-f55e0f6f-7a86-408e-a338-f3023b080370.png)

## 資料庫的實際檔案存放在 Local 的 /tmp/mysqldb 目錄中

使用作業系統管理者權限來查看此目錄

```bash
$ ls -al /tmp/mysqldb/
總用量 198084
drwxrwxr-x  7 systemd-coredump egs                  4096  六  17 16:24  .
drwxrwxrwt 26 root             root                12288  六  17 16:31  ..
-rw-r-----  1 systemd-coredump systemd-coredump       56  六  17 16:23  auto.cnf
-rw-r-----  1 systemd-coredump systemd-coredump  3117106  六  17 16:24  binlog.000001
-rw-r-----  1 systemd-coredump systemd-coredump      157  六  17 16:24  binlog.000002
-rw-r-----  1 systemd-coredump systemd-coredump       32  六  17 16:24  binlog.index
-rw-------  1 systemd-coredump systemd-coredump     1676  六  17 16:23  ca-key.pem
-rw-r--r--  1 systemd-coredump systemd-coredump     1112  六  17 16:23  ca.pem
-rw-r--r--  1 systemd-coredump systemd-coredump     1112  六  17 16:23  client-cert.pem
-rw-------  1 systemd-coredump systemd-coredump     1680  六  17 16:23  client-key.pem
-rw-r-----  1 systemd-coredump systemd-coredump   196608  六  17 16:26 '#ib_16384_0.dblwr'
-rw-r-----  1 systemd-coredump systemd-coredump  8585216  六  17 16:23 '#ib_16384_1.dblwr'
-rw-r-----  1 systemd-coredump systemd-coredump     5954  六  17 16:24  ib_buffer_pool
-rw-r-----  1 systemd-coredump systemd-coredump 12582912  六  17 16:24  ibdata1
-rw-r-----  1 systemd-coredump systemd-coredump 50331648  六  17 16:26  ib_logfile0
-rw-r-----  1 systemd-coredump systemd-coredump 50331648  六  17 16:23  ib_logfile1
-rw-r-----  1 systemd-coredump systemd-coredump 12582912  六  17 16:24  ibtmp1
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 16:24 '#innodb_temp'
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 16:23  mysql
-rw-r-----  1 systemd-coredump systemd-coredump 31457280  六  17 16:24  mysql.ibd
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 16:23  performance_schema
-rw-------  1 systemd-coredump systemd-coredump     1680  六  17 16:23  private_key.pem
-rw-r--r--  1 systemd-coredump systemd-coredump      452  六  17 16:23  public_key.pem
-rw-r--r--  1 systemd-coredump systemd-coredump     1112  六  17 16:23  server-cert.pem
-rw-------  1 systemd-coredump systemd-coredump     1676  六  17 16:23  server-key.pem
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 16:24  sys
drwxr-x---  2 systemd-coredump systemd-coredump     4096  六  17 16:24  testdb
-rw-r-----  1 systemd-coredump systemd-coredump 16777216  六  17 16:26  undo_001
-rw-r-----  1 systemd-coredump systemd-coredump 16777216  六  17 16:26  undo_002

```