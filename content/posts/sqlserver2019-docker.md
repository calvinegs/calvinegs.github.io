---
title: "Run SQL Server container images with Docker"
date: 2022-05-19
description: "在開發的環境中我們常會需要搭配資料庫，而常見的資料庫管理系統種類、版本繁多，如果搭直接安裝在 local 端作業系統中，可能會造成開發環境混亂，其中採 Docker 方式來安裝測試用資料庫的方式是一個不錯的解決方案"
tags: ["docker", "SQL Server", "Ubuntu"]
categories: ["Database"]
---

# 如何在 Ubuntu 中使用 Docker 啟動 MS SQL Server 2019

> 參考資料：　https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash

## 首先當然在你的作業系統中必須先安裝好 Docker

## 下載　SQL Server Image file

```bash
$ sudo docker pull mcr.microsoft.com/mssql/server:2019-latest
$ docker images

REPOSITORY                         TAG             IMAGE ID       CREATED       SIZE
mcr.microsoft.com/mssql/server     2019-latest     f554c0722914   4 weeks ago   1.64GB
```

## 使用 Docker 把 SQL Server image file 啟動

```base
$ docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Sql@1234" \
   -p 1433:1433 --name sql1 -h sql1 \
   -v sqlvolume:/var/opt/mssql \ # 將資料映射到 local 的 volume 上
   -d mcr.microsoft.com/mssql/server:2019-latest
   
$ docker ps

CONTAINER ID   IMAGE                                        COMMAND                  CREATED        STATUS          PORTS                                       NAMES
36c9fe84ea3e   mcr.microsoft.com/mssql/server:2019-latest   "/opt/mssql/bin/perm…"   42 hours ago   Up 40 minutes   0.0.0.0:1433->1433/tcp, :::1433->1433/tcp   sql1
```

## 修改 SA Password

```bash
$ docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
   -S localhost -U SA -P "Sql@1234" \
   -Q 'ALTER LOGIN SA WITH PASSWORD="Sql@12345"'
```

## 查看 Volume
```base
$ docker volume ls
local sqlvolume

$ docker volume inspect sqlvolume
[
    {
        "CreatedAt": "2022-07-09T12:53:39+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/sqlvolume/_data",
        "Name": "sqlvolume",
        "Options": null,
        "Scope": "local"
    }
]

$ sudo ls -al /var/lib/docker/volumes/sqlvolume/_data/data
總用量 146956
drwxr-xr-x 2 10001 root     4096  七   9 13:08 .
drwxrwx--- 6 root  root     4096  七   9 12:53 ..
-rw-r----- 1 10001 root      256  七   9 12:53 Entropy.bin
-rw-r----- 1 10001 root  4653056  七  10 12:25 master.mdf
-rw-r----- 1 10001 root  2097152  七  10 12:25 mastlog.ldf
-rw-r----- 1 10001 root  8388608  七  10 12:25 modellog.ldf
-rw-r----- 1 10001 root  8388608  七  10 12:25 model.mdf
-rw-r----- 1 10001 root 14090240  七  10 12:21 model_msdbdata.mdf
-rw-r----- 1 10001 root   524288  七  10 12:21 model_msdblog.ldf
-rw-r----- 1 10001 root   524288  七  10 12:21 model_replicatedmaster.ldf
-rw-r----- 1 10001 root  4653056  七  10 12:21 model_replicatedmaster.mdf
-rw-r----- 1 10001 root 14090240  七  10 12:25 msdbdata.mdf
-rw-r----- 1 10001 root   786432  七  10 12:25 msdblog.ldf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb2.ndf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb3.ndf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb4.ndf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb5.ndf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb6.ndf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb7.ndf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb8.ndf
-rw-r----- 1 10001 root  8388608  七  10 12:21 tempdb.mdf
-rw-r----- 1 10001 root  8388608  七  10 12:25 templog.ldf
-rw-r----- 1 10001 root  8388608  七  10 12:25 TestDB_log.ldf
-rw-r----- 1 10001 root  8388608  七  10 12:25 TestDB.mdf
```

## 連接上 Docker 中的 SQL Server

```bash
$ docker exec -it sql1 "bash"
```

## 執行 SQL Commander 工具來操作 SQL Server

```bash
mssql@sql1:/$ /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "Sql@12345"
```
## 查看 SQL Server 版本

```bash
1> select @@version
2> go

Microsoft SQL Server 2019 (RTM-CU16) (KB5011644) - 15.0.4223.1 (X64) 
	Apr 11 2022 16:24:07 
	Copyright (C) 2019 Microsoft Corporation
	Developer Edition (64-bit) on Linux (Ubuntu 20.04.4 LTS) <X64>                                                                                                      

(1 rows affected)
```

## 建立 TestDB 資料庫

```bashe
1> CREATE DATABASE TestDB
2> Select name from sys.databases
3> go
```

## 在 TestDB 資料庫中建立 Inventory 資料表 並 寫入資料

```bash
1> USE TestDB
2> CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
3> INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
4> go
```

## 查詢 Inventory 資料表

```bash
1> SELECT * FROM Inventory WHERE quantity > 152;
2> go
```

## 離開 SQL Commder 工具

```bash
1> quit
```

## 離開 Docker 環境

```bash
mssql@sql1:/$ exit
```

## 安裝 dbeaver-ce
> 有了資料後，通常你會需要一個管理工具來連結上該資料庫來做一些日常的資料管理。由於工作關係，會常使用到各式資料庫，若你的管理工具只適用某類資料，那勢必要安裝好幾個工具了。在此建議可使用 bdeaver-cd (https://dbeaver.io/download/)

```bash
$ wget https://dbeaver.io/files/dbeaver-ce_latest_amd64.deb
$ sudo dpkg -i dbeaver-ce_latest_amd64.deb
```
> 安裝完成後啟動它，進行連結資料庫設定
![image](https://user-images.githubusercontent.com/21993717/169449369-7c28062e-0f50-41af-8cf8-30e9596286f8.png)


> 連結 SQL Server 資料庫
![image](https://user-images.githubusercontent.com/21993717/169449181-897f8903-d703-40b6-a0a7-ab488b776fba.png)

![image](https://user-images.githubusercontent.com/21993717/169449716-1ccafd9c-3811-46d9-9659-31206c3a8e6c.png)

> 管理資料庫 - 查詢資料表資料、結構、修改資料表資料、結構...
![image](https://user-images.githubusercontent.com/21993717/169450121-14ddd510-a71e-41dc-a8ed-4fa73e4dbb1c.png)
