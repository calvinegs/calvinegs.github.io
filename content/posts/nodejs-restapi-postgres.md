---
title: "使用 Node.js + express + postgres 建立一個後端服務 REST API"
date: 2022-06-07
draft: false
description: "Node.js + express 來建立 REST API 服務，同時為提高網路安全性採取了 JWT JSON Web Token）來實作使用者驗證機制。資料庫的部份是使用 PostgresSQL，為方便起見，採用 Docker 來執行 PostgresSQL。"
tags: ["Node.js", "express", "Postgres", "JWT", "webapi"]
categories: ["Node.js"]
---

*[github Source code](https://github.com/calvinegs/nodejs-restapi-postgres.git)*

Technology:
- NodeJs 17.6.0
- Express 4.17.1
- cors 2.8.5
- crypto-js 4.1.1       // 加密套件
- jsonwebtoken 8.5.1    // 支援 Json Web Token　的功能套件
- Sequelize 6.20.1      // ORM 套件
- pg 8.7.3
- pg-hstore 2.3.4
- PostgreSQL 14.3

## 專案完成後的檔案結構

```
./專案目錄
├── app/
│	├── config/
│   │	└── db.config.js
│	├── middleware/
│   │	├── auth.jwt.js
│   │	├── index.js
│   │	└── verify.signup.js
│	├── models/
│   │	├── index.js
│   │	├── role.model.js
│   │	└── user.model.js
│	├── routes/
│   │	├── auth.routes.js
│   │	└── user.routes.js
│   └── services/
│   	├── auth.service.js
│   	└── user.service.js
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
|GET| /api/users/test/all|造訪公開內容區|
|GET| /api/users/test/user|造訪｀一般使用者｀內容區|
|GET| /api/users/test/moderator| 造訪｀版主｀內容區|
|GET| /api/users/test/admin| 造訪一般｀管理者｀內容區|

## 設置專案環境

```bash
$ node --version 	# 檢測環境已裝妥 node.js (若已安裝會顯示目前安裝的版本）
v17.6.0

$ mkdir nodejs-webapi-jwt-postgre && cd nodejs-webapi-jwt-postgre ＃ 建立一個專案目錄
$ npm init	＃ 産生一專案設定檔 package.json
$ touch server.js	＃ 産生一個新檔案
```
> package.json 預設的內容

```json
{
  "name": "nodejs-webapi-jwt-postgres",
  "version": "1.0.0",
  "description": "Node.js + exporess + JWT + PostgresSQL",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "node.js",
    "express",
    "jwt",
    "postgres"
  ],
  "author": "calvin",
  "license": "ISC"
}
```

> 安裝相依套件

```bash
$ yarn add express dotenv sequelize pg pg-hstore    # 加入相依套件
$ yarn add --dev nodemon  # 加入開發時期相依套件
```

> 建立 git 初始版本

```
$ git init
$ echo 'node_modules/' > .gitignore  # 新增 git ignore 設定檔，並設定 node_modules/ 目錄不加入版控
$ echo 'yarn.lock' >> .gitignore
$ git add . && git commit -m "Initial commit" 建立 git 初始版本的資訊
```

> 設定專案啟動指令（如第七行的指令設定），當輸入 npm start 時系統自動以 node 來執行 server.js 程式，並即時監測 server.js 檔案有變化存檔時馬上重新啟動 node server.js 來執行該程式。
```json {linenos=table,hl_lines=[7],linenostart=1}
{
  "name": "nodejs-webapi-jwt-postgres",
  "version": "1.0.0",
  "description": "Node.js + exporess + JWT + PostgresSQL",
  "main": "server.js",
  "scripts": {
    "start": "nodemon server.js"
  },
  "keywords": [
    "node.js",
    "express",
    "jwt",
    "postgres"
  ],
  "author": "calvin",
  "license": "ISC",
  "dependencies": {
    "dotenv": "^16.0.1",
    "express": "^4.18.1",
    "pg": "^8.7.3",
    "pg-hstore": "^2.3.4",
    "sequelize": "^6.20.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.16"
  }
}
```
> 在 server.js 中加入此行程式，並在 vscode Terminal 中輸入 npm start
```js
console.log("Hi NodeJS...")	
```
> 顯示結果如下
```bash
Hi NodeJS...
```

## 建立一個 Express 應用程式

> 使用以下程式覆蓋 server.js 檔案

```js
const express = require("express");
const app =  express();
app.get('/', function (req, res) {
    res.send('Hello World')
  });
app.get("/api/test", (req, res) => {
	console.log("test is successful");
    res.send("test is successful");
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
    console.log(`Backend server is running on port ${PORT}`);
})
```

> 存檔後，開啟 瀏覽器，輸入 localhost:5000/api/test，在 vscode terminal 視窗中會顯示：

```bash
Backend server is running on port 5000
test is successful
```

## 使用 Node.js 連結 PostgresSQL

若你還沒有安裝 PostgresSQL 可以參考這筆記先將資料庫管理系統備妥 *[使用 Docker 執行 PostgresSQL 與 pgAdmin](https://calvinegs.github.io/posts/docker-postgres-pgadmin/)*

在程式中使用了 sequelize 這個套件的功能來連結 Postgres 資料庫，直接透過 Sequelize constructor 來給定連結資料庫的參數，包含 “資料庫名稱“、”User Id"、"Password"、“Host Name"、"資料庫類別“(dialect)等。

```js {linenos=table,hl_lines=[4-8],linenostart=1}
const express = require("express");
const app = express();

const { Sequelize } = require('sequelize');
const sequelize = new Sequelize('testdb', 'postgres', '12345', {
    host: 'localhost',
    dialect: 'postgres'
});
try {
  sequelize.authenticate();
  console.log('Connection has been established successfully.');
} catch (error) {
  console.error('Unable to connect to the database:', error);
}

app.get("/api/test", (req, res) => {
    console.log("test is successful");
    res.send("test is successful");
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
    console.log(`Backend server is running on port ${PORT}`);
})
```

> 存檔後，在 vscode terminal 視窗中會顯示：

```bash
Connection has been established successfully.
Backend server is running on port 5000
```
> 表示連結成功。

## 專案目錄

接下來要陸續完成相關程式，為使程式架構更顯清晰，專案目錄規劃如下：

```
./專案目錄
├── app/  ＃ 程式目錄
│   ├── config/  ＃ 設置連結 PostgresSQL 資料庫的參數
│   ├── middleware/  
│   ├── models/
│   ├── routes/
│   └── services/  ＃ 業務邏輯
├── node_modules/
├── .env  ＃ 程式中的相關“設定值”
├── .gitignore
├── package-lock.json
├── package.json
├── README.md
├── server.js   # 主程式
└── yarn.loc
```
## 彈性管理參數值

### 管理程式中的“資料庫連結設定值”

在 app/config/目錄中新增一支 db.config.js 程式內容如下：
```js
module.exports = {
  HOST: "localhost",    // Host Name
  USER: "postgres",     // User Name
  PASSWORD: "12345",    // Password
  DB: "testdb",         // Database Name
  dialect: "postgres",  // 資料庫類別
  pool: {
    max: 5,             //　連結池中最大的 connection 數
    min: 0,
    acquire: 30000,     //　連結 Timeout 時間(毫秒)
    idle: 10000         //　連結被釋放的 idle 時間(毫秒)
  }
};
```

### 管理程式中的“設定值”
> 使用 dotenv 套件的功能來管理程式中相關的設定值
- 首先在專案根目錄下建立一個名為 ".env" 的檔案

```ini
JWT_SEC=Jason-Web-Token-Secret-key-jaslkdjfhjwkej01kd1954
```
- 在程式中先匯入 dotenv 套件(第1行），再“啟動它”(第2行），使用時透過 “process.env.JWT_SEC" 語法取得 JWT_SEC的設定值
- 第四行程式碼中的 process.env.PORT 為相同的原則可在 .evn 檔案中加入 PORT 的設定值調整

```js {linenos=table,hl_lines=[“1-2",4],linenostart=1}
const dotenv = require("dotenv");
dotenv.config();
// ...
const PORT = process.env.PORT || 5000;  // port 預設為 5000 ，並可以在 .env 檔案中進行客製化 （如：PORT＝5001）
app.listen(PORT, () => {
    console.log(`Backend server is running on port ${PORT}`);
})
```

## 定義 Sequelize 模型

在 app/models/目錄中新增一支 user.model.js 程式內容如下，這個設定資料可搭合 sequelize.sync() 功能自動在 PostgresSQL 資料庫中建立一個名為 users 的資料表(table)，共有三個皆為 string 型態的欄位，分別為 username、email、password。

```js
module.exports = (sequelize, Sequelize) => {
  const User = sequelize.define("users", {
    username: {
      type: Sequelize.STRING
    },
    email: {
      type: Sequelize.STRING
    },
    password: {
      type: Sequelize.STRING
    }
  });
  return User;
};
```
同時在 initial Sequelize 後，我們不需要編寫 CRUD 函數，Sequelize 支持所有這些函數
- 建立一個新的 user: create(object)
- 透過 id 找到一個　user: findByPk(id)
- 透過 email 找到一個　user: findOne({ where: { email: ... } })
- 取得所有使用者: findAll()
- 透過 username 找到符合的　user: findAll({ where: { username: ... } })

在 app/models/目錄中新增一支 role.model.js 程式內容如下

```js
module.exports = (sequelize, Sequelize) => {
  const Role = sequelize.define("roles", {
    id: {
      type: Sequelize.INTEGER,
      primaryKey: true
    },
    name: {
      type: Sequelize.STRING
    }
  });
  return Role;
};
```

## 初始化 Sequelize

在 app/models/目錄中新增一支 index.js 程式內容如下

```js
const config = require("../config/db.config.js");   // 引入資料庫連結設定檔
const Sequelize = require("sequelize");
const sequelize = new Sequelize(                    // 由資料庫連結設定檔的設定值來備置 Sequelize
  config.DB,
  config.USER,
  config.PASSWORD,
  {
    host: config.HOST,
    dialect: config.dialect,
    operatorsAliases: false,
    pool: {
      max: config.pool.max,
      min: config.pool.min,
      acquire: config.pool.acquire,
      idle: config.pool.idle
    }
  }
);
const db = {};
db.Sequelize = Sequelize;
db.sequelize = sequelize;
db.user = require("../models/user.model.js")(sequelize, Sequelize);
db.role = require("../models/role.model.js")(sequelize, Sequelize);
// 設定兩資料表的對應關係（多對多，所以會多出一個新的表 user_roles）
// 一個使用者可能有多個角色
// 一個角色也可能有多個使用者
db.role.belongsToMany(db.user, {
  through: "user_roles",
  foreignKey: "roleId",
  otherKey: "userId"
});
db.user.belongsToMany(db.role, {
  through: "user_roles",
  foreignKey: "userId",
  otherKey: "roleId"
});
db.ROLES = ["user", "admin", "moderator"];
module.exports = db;
```

## 執行程式産生資料表與資料

### 在 server.js 主程式中加入以下程式

```js
const db = require("./app/models");     // 引入 app/models/index.js 匯出的程式碼(即 sequelize model 定義檔)
const Role = db.role;

// 呼叫 sync function 將會依 model 定義內容産生資料表，force 參數值為 true 將會重建已存在的資料表
db.sequelize.sync({ force: true }).then(() => {
    console.log('Drop and Resync Database with { force: true }');
    initial();  // 産生資料表後，呼叫 initial function 為 roles table 新增三筆初始資料
}).catch((err) => {
    console.log(err);
});

const PORT = process.env.PORT || 5000;  // port 預設為 5000 ，並可以在 .env 檔案中進行客製化 （如：PORT＝5001）
app.listen(PORT, () => {
    console.log(`Backend server is running on port ${PORT}`);
})

// 為 roles table 新增三筆初始資料
function initial() {
    Role.create({
        id: 1,
        name: "user"
    });

    Role.create({
        id: 2,
        name: "moderator"
    });

    Role.create({
        id: 3,
        name: "admin"
    });
}
```
### 執行程式産生資料表

程式執行成功後可以查看資料庫已順利産生三個資料表以及 roles table 中的三筆初始資料

![image](https://user-images.githubusercontent.com/21993717/172360620-07eb656e-d0b8-4c1a-9166-a144ff8ef053.png)


## 了解 Node.js 路由

### 建立 user.routes.js router file

新增 routes 目錄，在此目錄下新增 user.routes.js 檔案

> routes/user.routes.js

```js
const router = require("express").Router();
router.get("/usertest", (req, res) => {
	res.send("user test is successful");
});

module.exports = router;
```

在 server.js 程式中先匯入 "./app/routes/user.routes" 這個 router 設定檔，再透過 app.use 語法來使用這個 router(第9行)。

```js {linenos=table,hl_lines=[5,9],linenostart=1}
const express = require("express");
const app =  express();
const dotenv = require("dotenv");

const userRoute = require("./app/routes/user.routes");

dotenv.config();

app.use("/api/users", userRoute);

app.listen(process.env.PORT || 5000, ()=>{
    console.log("Backend server is running...");
});
```

開啟瀏覧器，輸入 http:5000/api/users/usertest，瀏覧器將呈現成功訊息

```bash
user test is successfull
```
為 routes/user.routes.js 再新增一個 post method

```js {linenos=table,hl_lines=["6-9"],linenostart=1}
const router = require("express").Router();
router.get("/usertest", (req, res) => {
	res.send("user test is successful");
});

router.post("/userposttest", (req, res) => {
    const username = req.body.username;
    res.send("your username is: " + username)
})

module.exports = router;
```

使用 postman 來測試 post，結果回傳的是 Server Error，原因是 express 預設是不接受 json 格式的資料。

![image](https://user-images.githubusercontent.com/21993717/165109395-6fe656e9-4d12-448e-9fee-f47e4a6c6d3f.png)

在 server.js 程式中加入如第一行的設定

```js {linenos=table,hl_lines=[1],linenostart=1}
app.use(express.json());
app.use("/api/users", userRoute);
```
設定完成後就可正常了

![image](https://user-images.githubusercontent.com/21993717/165110665-149df942-59a9-40c4-8de8-8061c2f7af76.png)


### 建立 auth.routes.js router file
將使用者資料註冊和資用者帳號驗證的機制獨立在這個 route file 中，讓程式結構更清晰。內容如下：

```js {linenos=table,hl_lines=[3,6,8],linenostart=1}
// routes/auth.routes.js
const router = require("express").Router();
const authService = require("../services/auth.service");

// 帳號註冊
router.post("/signup", authService.signup);
// 登入
router.post("/signin", authService.signin);

module.exports = router;
```
> 在 auth.routes.js route 程式中基於｀關注點分離原則｀把｀商業邏輯｀的部份再分離至 services 中。

> 在 ./app 目錄下新增 services 子目錄，並新增一支 auth.service.js 程式，將使用者註冊及登入邏輯放在這支 service 程式中，內容如下：

> 在這支程式中會使用到額外的套件，必須先進行安裝。`$ yarn add crypto-js jsonwebtoken`。其中 crypto-js 用來進行使用者密碼加解密(程式第2、15、50、51行)，而 jsonwebtoken 套件則是支援 Json Web Token 功能(程式第55行使用 jwt.sign 産生合法 Token)。

```js　{linenos=table,hl_lines=[2,16,51,52,56],linenostart=1}
// auth.service.js
const db = require("../models");
const CryptoJS = require("crypto-js");
const jwt = require("jsonwebtoken");

const User = db.user;
const Role = db.role;

const Op = db.Sequelize.Op;

const signup = (req, res) => {
    // Save User to Database
    User.create({
        username: req.body.username,
        email: req.body.email,
        password: CryptoJS.AES.encrypt(req.body.password, process.env.PASS_SEC).toString(),
    }).then(user => {
        if (req.body.roles) {
            Role.findAll({
                where: {
                    name: {
                        [Op.or]: req.body.roles
                    }
                }
            }).then(roles => {
                user.setRoles(roles).then(() => {
                    res.send({ message: "User registered successfully!" });
                });
            });
        } else {
            // user role = 1
            user.setRoles([1]).then(() => {
                res.send({ message: "User registered successfully!" });
            });
        }
    }).catch(err => {
        res.status(500).send({ message: err.message });
    });
};

const signin = (req, res) => {
    User.findOne({
        where: {
            username: req.body.username
        }
    }).then(user => {
        if (!user) {
            return res.status(404).send({ message: "Wrong Credentials." });
        }

        const hashedPassword = CryptoJS.AES.decrypt(user.password, process.env.PASS_SEC);
        const orginalPassword = hashedPassword.toString(CryptoJS.enc.Utf8);

        orginalPassword !== req.body.password && res.status(401).json("Wrong Credentials");

        const accessToken = jwt.sign(
            {id: user.id}, 
            process.env.JWT_SEC,
            { expiresIn: "3d" }
        );

        var authorities = [];
        user.getRoles().then(roles => {
            for (let i = 0; i < roles.length; i++) {
                authorities.push("ROLE_" + roles[i].name.toUpperCase());
            }
            res.status(200).send({
                id: user.id,
                username: user.username,
                email: user.email,
                roles: authorities,
                accessToken: accessToken
            });
        });
    }).catch(err => {
        res.status(500).send({ message: err.message });
    });
};

module.exports = { signup, signin };
```

> 在 server.js 引用這個新的 router

```js {linenos=table,hl_lines=[3,7],linenostart=1}
// ...
const userRoute = require("./app/routes/user.routes");
const authRoute = require("./app/routes/auth.routes");
app.use(express.json());
app.use("/api/users", userRoute);

app.use("/api/auth", authRoute);

const PORT = process.env.PORT || 5000;  // port 預設為 5000 ，並可以在 .env 檔案中進行客製化 （如：PORT＝5001）
app.listen(PORT, () => {
    console.log(`Backend server is running on port ${PORT}`);
})
```

### 測試使用者註冊及登入功能

使用 postman 進行使用者註冊功能測試

![image](https://user-images.githubusercontent.com/21993717/172516923-b6163f6b-a42e-4648-ab7a-2260d54ebfb5.png)

> 註冊成功後，在資料庫中已新增一筆使用者資料

![2022-06-08 10-19-46](https://user-images.githubusercontent.com/21993717/172517325-9dc0d87f-f12d-4637-a799-10f6df019413.png)

使用 postman 進行使用者登入功能測試

> 登入成功後會回傳一個 Token

![image](https://user-images.githubusercontent.com/21993717/172559515-ee428543-e8ad-4315-8869-2a2bb93db169.png)


## 在 Node.js 中使用 JWT 來進行 Token-Based 的使用者授權驗證

使用 `jsonwebtoken`套件可以實現 Token-base 的身份驗證與授權讓我們的 API 程式更安全 

JWT 實作的過程大致可以分成三個部分:
- 在登入成功後産生合法的 JWT Token
- 每次收到 request 時驗證是否為合法有效的 JWT Token
- 在特定 API Endpoint 上驗證是否帶有 “合法有效的 JWT Token”，以達到權限管理的需求

### 産生合法 JWT
在登入證驗中加入産生 Token 的邏輯，在檢核使用者輸入的密碼正確後，將 User ID (_id這個內部 Key)這個屬性值透過 sign function 來産生 access token，並回傳給前端。
```js {linenos=table,hl_lines=[1,3,5],linenostart=1}
const jwt = require("jsonwebtoken");  // 匯入 JsonWebToken套件
//...
        const accessToken = jwt.sign({
            id: user._id
        }, process.env.JWT_SEC,
            { expiresIn: "3d" }
        );

        var authorities = [];
        user.getRoles().then(roles => {
            for (let i = 0; i < roles.length; i++) {
                authorities.push("ROLE_" + roles[i].name.toUpperCase());
            }
            res.status(200).send({
                id: user.id,
                username: user.username,
                email: user.email,
                roles: authorities,
                accessToken: accessToken
            });
        });

```

> encrypt function 參數除了要加密的字串外，需要一個加密 Key，為彈性起見，把它寫在 .env 檔案中 (PASS_SEC)

```ini {linenos=table,hl_lines=[2]}
# .env
JWT_SEC=Jason-Web-Token-Secret-key-jaslkdjfhjwkej01kd1954
PASS_SEC=cal
```

### 使用 JWT 來驗證 Token 
在前端取得合法的 JWT Token後，來看看當使用者在呼叫其他 API 時一併回傳的 Token　如何在 server 端來進行驗證。

首先，我們要在 app/ 目錄下新增一個 middleware/ 的子目錄，並在其中新增一個名為 auth.jwt.js 的 express Middleware，程式內容如下

在程式第14行中使用 jsonwebtoken 套件的 verify function 就是用來驗證 request 中的 Token 是否為合法 Token。驗證時一樣需要｀加密 Key｀來當參數。

在這程式中除了驗證 request 中是否有合法的 Token 外，還有其他授權檢核的邏輯：驗證是否為管理者、驗證是否為版主、驗證是否為管理者或是版主等。

```js {linenos=table,hl_lines=[5,14,25,43,60]}
const jwt = require("jsonwebtoken");
const db = require("../models");
const User = db.user;

const verifyToken = (req, res, next) => {
  let authHeader = req.headers.authorization;
  if (!authHeader) {
    return res.status(403).send({
      message: "Your are not authenticated!"
    });
  }
  const token = authHeader.split(" ")[1];

  jwt.verify(token, process.env.JWT_SEC, (err, decoded) => {
    if (err) {
      return res.status(401).send({
        message: "Token is not valid!"
      });
    }
    req.userId = decoded.id;
    next();
  });
};

isAdmin = (req, res, next) => {
  User.findByPk(req.userId).then(user => {
    user.getRoles().then(roles => {
      for (let i = 0; i < roles.length; i++) {
        if (roles[i].name === "admin") {
          next();
          return;
        }
      }

      res.status(403).send({
        message: "Require Admin Role!"
      });
      return;
    });
  });
};

const isModerator = (req, res, next) => {
  User.findByPk(req.userId).then(user => {
    user.getRoles().then(roles => {
      for (let i = 0; i < roles.length; i++) {
        if (roles[i].name === "moderator") {
          next();
          return;
        }
      }

      res.status(403).send({
        message: "Require Moderator Role!"
      });
    });
  });
};

const isModeratorOrAdmin = (req, res, next) => {
  User.findByPk(req.userId).then(user => {
    user.getRoles().then(roles => {
      for (let i = 0; i < roles.length; i++) {
        if (roles[i].name === "moderator") {
          next();
          return;
        }

        if (roles[i].name === "admin") {
          next();
          return;
        }
      }

      res.status(403).send({
        message: "Require Moderator or Admin Role!"
      });
    });
  });
};

const authJwt = {
  verifyToken: verifyToken,
  isAdmin: isAdmin,
  isModerator: isModerator,
  isModeratorOrAdmin: isModeratorOrAdmin
};
module.exports = authJwt;
```

為簡化 middleware 使用時的匯入路徑，我們在　middleware/ 的子目錄，再新增一個名為 index.js 的程式，內容如下

```js
const authJwt = require("./auth.jwt");

module.exports = {
  authJwt
};
```
### 在特定 API Endpoint 上驗證是否帶有 “合法有效的 JWT Token”
在前面完成了驗證 Token 的 Middleware 後，我們來看看如何在 router 中套用這些 middleware， 打開　routers/userroutes.js 程式檔，並將內容修改如下：

在程式第九行 post 的第二個參數，加入呼叫 authJwt.verifyToken 這個 middleware 驗證 token 的 function。

```js {linenos=table,hl_lines=[2,9]}
const router = require("express").Router();
const { authJwt } = require("../middleware");

router.get("/usertest", (req, res) => {
	res.send("user test is successful");
});

router.post("/userposttest", 
    authJwt.verifyToken,
    (req, res) => {
        const username = req.body.username;
        res.send("your username is: " + username)
    }
)

module.exports = router;
```
再次執行前面已執行過的 Postman 的 postusertest 這個 request，結果這次會回傳 ｀未授權｀的警告訊息。

![image](https://user-images.githubusercontent.com/21993717/172527700-5f28b849-a17a-4e66-9c69-b32b8b11c55c.png)

將登入成功時回傳的 token，加入到 Header 中，再執行一次 postusertest 即可執行成功。

![image](https://user-images.githubusercontent.com/21993717/172528020-8d70b2bb-d1df-4d6b-998d-ed6fcf1f2f95.png)

> 若送出 request 中未包含 Token 則會回傳“未被授權執行本功能”。

> 若送出 request 中包含的是不合法的 Token 則會回傳“Token不合法“。

## 使用 middleware 來進行檢核使用者角色
將 app/routes/user.routes.js 內容修改成如下：

```js
const router = require("express").Router();
const { authJwt } = require("../middleware");
const userService = require("../services/user.service");

// router.get("/usertest", (req, res) => {
//     res.send("user test is successful");
// });

// router.post("/userposttest",
//     authJwt.verifyToken,
//     (req, res) => {
//         const username = req.body.username;
//         res.send("your username is: " + username)
//     }
// )

router.get("/test/all", userService.allAccess);

router.get(
    "/test/user",
    [authJwt.verifyToken],
    userService.userBoard
);

router.get(
    "/test/moderator",
    [authJwt.verifyToken, authJwt.isModerator],
    userService.moderatorBoard
);

router.get(
    "/test/admin",
    [authJwt.verifyToken, authJwt.isAdmin],
    userService.adminBoard
);

module.exports = router;
```
>上述程式中可以看到不同 api 端點由不同角色使用者可以造訪，"api/users/test/all" 是個開放式的端點所有人皆可造訪、"api/users/test/user" 是所有已註冊的｀使用者｀可造訪、"api/users/test/moderator" 是角色為｀版主｀的使用者可造訪、 "api/users/test/admin" 是角色為｀管理者｀的｀使用皆｀可造訪

在 app/services/ 目錄下新增一支 service user.service.js，內容如下
```js
const allAccess = (req, res) => {
    res.status(200).send("Public Content.");
};

const userBoard = (req, res) => {
    res.status(200).send("User Content.");
};

const adminBoard = (req, res) => {
    res.status(200).send("Admin Content.");
};

const moderatorBoard = (req, res) => {
    res.status(200).send("Moderator Content.");
};

module.exports = { allAccess, userBoard, adminBoard, moderatorBoard };
```
註冊一個新使用者且具備有二個角色：moderator 及 admin

![image](https://user-images.githubusercontent.com/21993717/172561731-5dd54eb1-edce-47a2-adeb-ac93e26715cc.png)


打開瀏覽器，輸入　http://localhost:5000/api/users/test/all，可以發現這個開放式的端點任何人皆可造訪(未註冊的人也可以)。

![image](https://user-images.githubusercontent.com/21993717/172566209-13603ddc-bb8e-4526-af7a-65edf1e826ef.png)

在瀏覽器，重新輸入　http://localhost:5000/api/users/test/user，發現這個端點未註冊的人不能造訪。
![image](https://user-images.githubusercontent.com/21993717/172567113-379dbf29-4213-4353-ba20-352d9494f4fc.png)

打開 Postman 先使用已註冊的使用者帳號登入，取得 token

![image](https://user-images.githubusercontent.com/21993717/172559515-ee428543-e8ad-4315-8869-2a2bb93db169.png)

再將這個 token (使表使用者是 tom) 加入 request authorization header 中，再次造訪 http://localhost:5000/api/users/test/user，結果是可正常造訪

![image](https://user-images.githubusercontent.com/21993717/172568545-ca9cbfba-7579-41d3-826c-66bd902c86ac.png)

再以相同 token (代表使用者是 tom) 造訪 http://localhost:5000/api/users/test/moderator ，結果回傳 "須為版主角色者"｀"才能造訪。
![image](https://user-images.githubusercontent.com/21993717/172569428-0d29ee3d-bd60-48df-860b-3e5e1e8f6c21.png)

改用新註冊的使用者 Jeff 來重新登入，並取得回傳的 token

![image](https://user-images.githubusercontent.com/21993717/172570932-cd02fa97-1387-4000-a1d6-ac9ed5595ea0.png)

改採此 token (代表使用者是 jeff) 造訪 http://localhost:5000/api/users/test/moderator ，結果顯示可正常造訪。

![image](https://user-images.githubusercontent.com/21993717/172571549-828b314b-d44d-4d65-9e21-103f6f4fd8b1.png)

## 使用 middleware 來進行其他資料檢核

程式至此已經可以透過 JWT 相關功能查核使用者是否已正確登入系統、是以何種身份（角色）登入的。

最後要再呈現的是使用 middleware 功能來查核其他資料正確性，如：使用者註冊時是否使用了相同的使用者名稱？是否 email 已經被其他使用者使用過？

在 app/middleware 目錄下新增 verify.signup.js，內容如下：

```js {linenos=table,hl_lines=[5,37]}
const db = require("../models");
const ROLES = db.ROLES;
const User = db.user;

checkDuplicateUsernameOrEmail = (req, res, next) => {
  // Username
  User.findOne({
    where: {
      username: req.body.username
    }
  }).then(user => {
    if (user) {
      res.status(400).send({
        message: "Failed! Username is already in use!"
      });
      return;
    }

    // Email
    User.findOne({
      where: {
        email: req.body.email
      }
    }).then(user => {
      if (user) {
        res.status(400).send({
          message: "Failed! Email is already in use!"
        });
        return;
      }

      next();
    });
  });
};

checkRolesExisted = (req, res, next) => {
  if (req.body.roles) {
    for (let i = 0; i < req.body.roles.length; i++) {
      if (!ROLES.includes(req.body.roles[i])) {
        res.status(400).send({
          message: "Failed! Role does not exist = " + req.body.roles[i]
        });
        return;
      }
    }
  }
  
  next();
};

const verifySignUp = {
  checkDuplicateUsernameOrEmail: checkDuplicateUsernameOrEmail,
  checkRolesExisted: checkRolesExisted
};

module.exports = verifySignUp;
```

修改 app/middleware/index.js 檔案如下：

```js {linenos=table,hl_lines=[2,6]}
const authJwt = require("./auth.jwt");
const verifySignUp = require("./verify.signup");

module.exports = {
  authJwt,
  verifySignUp
};
```

將新的 middleware　功能放入到 SignUp 功能中。
打開　app/routes/auth.routes.js 程式檔，修改如下：
```js {linenos=table,hl_lines=[3,7,8]}
const router = require("express").Router();
const authService = require("../services/auth.service");
const { verifySignUp } = require("../middleware")
// 帳號註冊
router.post("/signup", 
[
    verifySignUp.checkDuplicateUsernameOrEmail,
    verifySignUp.checkRolesExisted
],
authService.signup);

// 登入
router.post("/signin", authService.signin);

module.exports = router;
```

先使用已經被註冊過的 EMail來進行測試，結果回傳 EMail 已被使用。

![image](https://user-images.githubusercontent.com/21993717/172577871-acb686cd-9b5f-4018-87bb-6f3f1d1b47d7.png)

再使用已被註冊過的 UserName 來進行註冊，結果回傳：

![image](https://user-images.githubusercontent.com/21993717/172578295-91688eb1-4af3-46e5-bdf9-259c61d7a3d4.png)

## cors
這個後端專案預計要給前端 Angular 來使用，跨域存取問題就用 cors 設定來解決。

先安裝 cors 套件
```bash
$ yarn add cors

yarn add v1.22.19
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Saved lockfile.
success Saved 2 new dependencies.
info Direct dependencies
└─ cors@2.8.5
info All dependencies
├─ cors@2.8.5
└─ object-assign@4.1.1
Done in 0.86s.
```
打開 server.js，修改如下：

```js {linenos=table,hl_lines=[3,6,7]}
const express = require("express");
const dotenv = require("dotenv");
const cors = require("cors");
dotenv.config();
const app = express();
var corsOptions = {
    origin: "http://localhost:4200"
};
app.use(cors(corsOptions));

//...
```
