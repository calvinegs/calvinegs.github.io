---
title: "使用 Node.js + express + MariaDB 建立一個後端服務 REST API"
date: 2022-07-08
draft: false
description: "Node.js + express 來建立 REST API 服務，同時為提高網路安全性採取了 JWT JSON Web Token）來實作使用者驗證機制。資料庫的部份是使用 MySQL，為方便起見，採用 Docker 來執行 MySQL。"
tags: ["Node.js", "express", "mariadb", "JWT", "webapi"]
categories: ["Node.js"]
---

*[github Source code](https://github.com/calvinegs/nodejs-webapi-jwt-mariadb.git)*

Technology:
- NodeJs 17.6.0
- Express 4.18.1
- cors 2.8.5
- crypto-js 4.1.1 # 加解密套件
- jsonwebtoken 8.5.1 # Json Web Token 的功能套件
- sequelize 6.20.2 # ORM 套件
- mysql2 2.3.3 # MySQL client for Node.js
- MariaDB 10.8.3  # 使用的資料庫

## 專案完成後的檔案結構

```
./專案目錄
├── app/
│   ├── config/
│   │   └── db.config.js
│   ├── middleware/
│   │   ├── auth.jwt.js
│   │   ├── index.js
│   │   └── verify.signup.js
│   ├── models/
│   │   ├── index.js
│   │   ├── role.model.js
│   │   ├── todo.model.js
│   │   └── user.model.js
│   ├── routes/
│   │   ├── auth.routes.js
│   │   ├── todo.routes.js
│   │   └── user.routes.js
│   └── services/
│       ├── auth.service.js
│       ├── todo.service.js
│       └── user.service.js
├── node_modules/
├── .env
├── .gitignore
├── package.json
├── README.md
├── server.js
└── yarn-lock
```

## 專案完成後所提供的 API 端點

|Methods|Urls|Actions|
|-------|-----------------------------|-------------------------------------------------|
|POST| /api/auth/signup|註冊新使用者帳號|
|POST| /api/auth/signin|使用者帳號登入|
|GET| /api/todos|get all Todos|
|GET| /api/todos/:id|get Todo by id|
|GET| /api/todos/done|find all done Todos|
|GET| /api/todos/title=[`keyword`]|find all Todos whick title contains `keyword`|
|POST| /api/todos|add New Todo|
|PUT| /api/todos/:id|update Todo by id|
|DELETE| /api/todos/:id|remove Todo by id|
|DELETE| /api/todos|remove all Todos|

## 設置專案環境

這個專案不會從頭從無到有紀錄每一個建置的步驟，而是採取套用 **"使用 Node.js + express + MySQL 建立一個後端服務 REST API"** 這篇筆記內容來修改成為使用 Mariadb 資料庫而完成。

### 複製即有專案
首先 git clone 即有的 MySQL 專案

```bash
$ git clone https://github.com/calvinegs/nodejs-webapi-jwt-mysql.git nodejs-webapi-jwt-mariadb

$ cd nodejs-webapi-jwt-mariadb
```

clone 成功後，再來調整相依的套件。調整前先回復原專案所使用的套件

```base
$ yarn install

yarn install v1.22.19
info No lockfile found.
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Saved lockfile.
Done in 4.92s.
```

在 **"使用 Node.js + express + MySQL 建立一個後端服務 REST API"** 這專案裡採用了 ORM 的套件來存取資料庫，使用 ORM 工具層的好處是它是一個抽象層，不直接使用 SQL 來，當後端資料庫轉換時，只要搭配不用的 Driver 便可完成，完全不須修改相關程式。

### 移除 mysql driver  套件

```bash
$ yarn remove mysql2

yarn remove v1.22.19
[1/2] Removing module mysql2...
[2/2] Regenerating lockfile and installing missing dependencies...
success Uninstalled packages.
Done in 0.49s.
```

### 安裝 mariadb driver 套件
``` bash
$ yarn add mariadb

arn add v1.22.19
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Saved lockfile.
success Saved 7 new dependencies.
info Direct dependencies
└─ mariadb@3.0.0
info All dependencies
├─ @alloc/quick-lru@5.2.0
├─ @types/geojson@7946.0.8
├─ @types/node@17.0.45
├─ denque@2.0.1
├─ mariadb@3.0.0
├─ please-upgrade-node@3.2.0
└─ semver-compare@1.0.0
Done in 2.45s.
```

修改完成後的 package.json 預設的內容
```json{linenos=table,hl_lines=[23]}
{
  "name": "nodejs-webapi-jwt-mariadb",
  "version": "1.0.0",
  "description": "Node.js + express + JWT + MariaDB",
  "main": "server.js",
  "scripts": {
    "start": "nodemon server.js"
  },
  "keywords": [
    "node.js",
    "express",
    "jwt",
    "mysql"
  ],
  "author": "calvin",
  "license": "ISC",
  "dependencies": {
    "cors": "^2.8.5",
    "crypto-js": "^4.1.1",
    "dotenv": "^16.0.1",
    "express": "^4.18.1",
    "jsonwebtoken": "^8.5.1",
    "mariadb": "^3.0.0",
    "sequelize": "^6.21.2"
  },
  "devDependencies": {
    "nodemon": "^2.0.18"
  }
}
```

## 修改程式

### 調整資料庫連結資料
修改 app/config/db.config.js
```js
module.exports = {
    HOST: "localhost",
    USER: "root",
    PASSWORD: "mariadb@12345",
    DB: "testdb",
    dialect: "mariadb",
    pool: {
        max: 5,
        min: 0,
        acquire: 3000,
        idle: 10000
    }
}
```

### 調整 Server.js
將原本被註解掉的 “資料庫初始化程式碼”打開，以便初次執行前程式可以自動建立相關資料表(Tables)
```js{linenos=table,hl_lines=[28-35,54-64],linenostart=1}
const express = require("express");
const dotenv = require("dotenv");
const cors = require("cors");
dotenv.config();
const app = express();
const corsOptions = {
  origin: "http://localhost:4200"
};
app.use(cors(corsOptions));

// const { Sequelize } = require('sequelize');
// const sequelize = new Sequelize('testdb', 'root', 'mysql@12345', {
//     host: 'localhost',
//     dialect: 'mysql'
// });
// try {
//   sequelize.authenticate();
//   console.log('Connection has been established successfully.');
// } catch (error) {
//   console.error('Unable to connect to the database:', error);
// }

// app.get("/api/test", (req, res) => {
// 	console.log("test is successful");
//     res.send("test is successful");
// });

const db = require("./app/models");
const Role = db.role;
db.sequelize.sync({ force: true }).then(() => {
  console.log('Drop and Resync Database with { force: true }');
  initial();
}).catch((err) => {
  console.log(err);
});

app.use(express.json());

const userRoute = require("./app/routes/user.routes");
app.use("/api/users", userRoute);

const todoRoute = require("./app/routes/todo.routes");
app.use("/api/todos", todoRoute);

const authRoute = require("./app/routes/auth.routes");
app.use("/api/auth", authRoute);

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`Backend server is running on port ${PORT}`);
});

// 為 roles table 新增二筆初始資料
function initial() {
  Role.create({
    id: 1,
    name: "user"
  });

  Role.create({
    id: 2,
    name: "admin"
  });
}
```

## 開始執行程式

## 安裝 MariaDB 資料庫
程式執行前要備妥 MariaDB 資料庫，為方便起見，採用 docker 方式來執行 MariaDB 資料管理系統，詳細內容可參考 *[使用 Docker 執行 Mariadb
](https://calvinegs.github.io/posts/docker-mariadb/)* 這篇筆紀。

## 執行程式
MariaDB Docker 啟動成功後就可以開始執行程式
```base
$ npm start
```

### 使用 DBeaver 查看資料庫
使用 DBeaver 連結到 MariaDB 後，發現透過程式已成功的産生相關的 Tables (資料表)。

![image](https://user-images.githubusercontent.com/21993717/177905596-ca82666b-00de-4095-a7c7-af034d36c93a.png)

### 使用 Postman 操作 Web API

註冊一個使用者帳號

![image](https://user-images.githubusercontent.com/21993717/177905988-31e2dc29-10f1-47ce-937f-7aa9890a66d3.png)

使用註冊成功的帳號登入系統，登入成功後會回傳一個 Token

![image](https://user-images.githubusercontent.com/21993717/177906145-30a5692d-b653-4156-8a1b-bba1d285a9a3.png)

帶入 token 就可成功的新增一個 todo

![image](https://user-images.githubusercontent.com/21993717/177906558-447596e3-75ee-4140-b057-7c7c81a4140a.png)

## 結語
至此你可看到只須少許的程式調整就已完成抽換後端資料庫的動作。在程式中所採用 ORM 套件是 Sequelize，它只要搭配不同的 dirver 便可支援 Postgres、MySQL、MariaDb、SQLite、MS SQL Server 等不同的關連式資料庫管理系統。（Sequelize 詳細資訊請參考　*[Sequelize Getting Started
](https://sequelize.org/docs/v6/getting-started/)* 　）