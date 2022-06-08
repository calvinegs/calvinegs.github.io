---
title: "使用 Node.js + express 建立一個電子商務後端服務的 REST API"
date: 2022-04-25
draft: false
description: "Node.js + express 來建立 REST API 服務，同時為提高網路安全性採取了 JWT JSON Web Token）來實作使用者驗證機制。而資料庫的部份是使用 MongoDB，但為方便起見，採直接使用MongoDB Cloud Services。"
tags: ["Node.js", "express", "MongoDB", "JWT", "webapi"]
categories: ["Node.js"]
---

*[github Source code #tag: restapi_nodejs](https://github.com/calvinegs/ecommerceapi)*

KEYWORD：
    MongoDB Cloud Services、Node.js、REST API、JWT、加解密、MongoDB Compass、Postman、express、cryptojs、dotenv、jsonwebtoken、mongoose

## 設置專案環境
```bash
$ node --version 	# 檢測環境已裝妥 node.js (若已安裝會顯示目前安裝的版本）

$ mkdir ecommerceapi && cd ecommerceapi ＃ 建立一個專案目錄
$ npm init -y	＃ 産一專案設定檔 package.json
$ touch index.js	＃ 産生一個新檔案
$ yarn add express mongoose dotenv	# 加入相依套件
$ yarn add --dev nodemon  # 加入開發時期相依套件

$ git init
$ echo 'node_modules/' > .gitignore  # 新增 git ignore 設定檔，並設定 node_modules/ 目錄不加入版控
$ git add . && git commit -m "Initial commit" 建立第一版本的資訊
```

> 設定專案啟動指令（如第七行的指令設定），當輸入 npm start 時系統自動以 node 來執行 index.js 程式，並即時監測 index.js 檔案有變化存檔時馬上重新啟動 node index.js 來執行該程式。

```json {linenos=table,hl_lines=[7],linenostart=1}
{
  "name": "ecom",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "nodemon index.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "dotenv": "^16.0.0",
    "express": "^4.17.3",
    "mongoose": "^6.3.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.15"
  }
}
```
> 在 index.js 中加入此行程式，並在 vscode Terminal 中輸入 npm start

```js
console.log("Hi NodeJS...")	
```
> 顯示結果如下

```bash
Hi NodeJS...
```

## 建立一個 Express 應用程式
> 使用以下程式覆蓋 index.js 檔案

```js
const express = require("express");
const app =  express();
app.get("api/test", () => {
	console.log("test is successful");
});

app.listen(5000, () => {
    console.log("Backend server is running...");
})
```
> 存檔後，開啟 瀏覽器，輸入 localhost:5000/api/test，在 vscode terminal 視窗中會顯示：

```bash
Backend server is running...
test is successful
```

## 使用 Node.js 連結 MongoDB
在使用 MongoDB 除了在 local 端安裝的方式外，也可以使用 Docker 的方式來啟用 MongoDB，而更方便的是直接使用 https://cloud.mongodb.com/ 線上的免費服務。

在註冊完成後，可认在 Database / Connect / Connect your application 中找到連結字串
![image](https://user-images.githubusercontent.com/21993717/165058235-be9d50a6-862a-4f4f-a7e8-9177da33e892.png)

![image](https://user-images.githubusercontent.com/21993717/165058844-5a1403b6-fd53-43f2-90a7-726a84f42c20.png)

> 在程式中使用了 mongoose 這個套件的功能來連結 MongoDB 資料庫（第三行），並在程式第6行，connect function 中放入“連結字串”

```js {linenos=table,hl_lines=[3,6],linenostart=1}
const express = require("express");
const app =  express();
const mongoose = require("mongoose");

mongoose
  .connect("mongodb+srv://calv______ @cluster0.6f8ky.mongodb.net/myFirstDatabase?retryWrites=true&w=majority")
  .then(()=>console.log("DB Connection successful"))
  .catch((err)=> {
    console.log(err)
  }
);

app.listen(5000, () => {
    console.log("Backend server is running...");
})
```
> 存檔後，在 vscode terminal 視窗中會顯示：

```bash
Backend server is running...
DB Connection successful
```
> 表示連結成功。

### 彈性管理程式中的“設定值”
> 使用 dotenv 套件的功能來管理程式中相關的設定值
- 首先在專案根目錄下建立一個名為 ".env" 的檔案

```json
MONGO_URL=mongodb+srv://cal...............
```
- 在程式中先匯入 dotenv 套件(第1行），再“啟動它”(第2行），使用時透過 “process.env.MONGO_URL" 語法(第4行）
- 第十行程式碼中的 process.env.PORT 為相同的原則可在 .evn 檔案中加入 PORT 的設定值調整
```js {linenos=table,hl_lines=[“1-2",4,10],linenostart=1}
const dotenv = require("dotenv");
dotenv.config();
mongoose
  .connect(process.env.MONGO_URL)
  .then(()=>console.log("DB Connection successful"))
  .catch((err)=> {
    console.log(err)
  }
);
app.listen(process.env.PORT || 5000, ()=>{  # port 預設為 5000 ，並可以在 .env 檔案中進行客製化 （如：PORT＝5001）
    console.log("Backend server is running...");
})
```

## 了解 Node.js 路由
> 新增 routes 目錄，在此目錄下新增 user.js 檔案

> routes/user.js

```js
const router = require("express").Router();
router.get("/usertest", (req, res) => {
	res.send("user test is successful");
});

module.exports = router;
```

> 在 index.js 程式中先匯入 "./routes/user" 這個 router 設定檔，再透過 app.use 語法來使用這個 router(第18行)。完整的 route 為 http://localhost:5000/api/user/usertest

```js {linenos=table,hl_lines=[6,18],linenostart=1}
const express = require("express");
const app =  express();
const mongoose = require("mongoose");
const dotenv = require("dotenv");

const userRoute = require("./routes/user");

dotenv.config();

mongoose
  .connect(process.env.MONGO_URL)
  .then(()=>console.log("DB Connection successful"))
  .catch((err)=> {
    console.log(err)
  }
);

app.use("/api/users", userRoute);

app.listen(process.env.PORT || 5000, ()=>{
    console.log("Backend server is running...");
});
```
> 開啟瀏覧器，輸入 http:5000/api/users/usertest，瀏覧器將呈現成功訊息

```bash
user test is successfull
```
> 為 routes/user.js 再新增一個 post method

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

> 使用 postman 來測試 post，結果回傳的是 Server Error，原因是 express 預設是不接受 json 格式的資料。

![image](https://user-images.githubusercontent.com/21993717/165109395-6fe656e9-4d12-448e-9fee-f47e4a6c6d3f.png)

> 在 index.js 程式中加入如第一行的設定

```js {linenos=table,hl_lines=[1],linenostart=1}
app.use(express.json());
app.use("/api/users", userRoute);
```
> 設定完成後就可正常了

![image](https://user-images.githubusercontent.com/21993717/165110665-149df942-59a9-40c4-8de8-8061c2f7af76.png)


## 使用 Node.js MongoDB 模型
> 接下來我們將資料寫入 MongoDB 資料庫中，首先要先建立 model file(模型檔案），在這個檔案中使用了 mongoose 的 Schema 的語法，只要在新增資料時套用這個這個模型檔，則會自動在 MongoDB 中建立相對應的 collection，將資料存入資料庫中。

### 新增 User 模型檔 (models/User.js)
> 先新增一個 models 目錄，在目錄下新增一個 User.js，內容如下

```js
const mongoose = require("mongoose");

const UserSchema = new mongoose.Schema({
    username: { type: String, required: true, unique: true },
    email: { type: String, required: true, unique: true },
    password: { type: String, required: true },
    isAdmin: { type: Boolean, default: false },
},
{ timestamps: true }  // 透過這個屬性設定，會自動在collection中新增二個屬性: createdAt、updateedAt。
);

module.exports = mongoose.model("User", UserSchema);
```

### 建立 auth.js router file (routes/auth.js)
將使用者資料註冊和資用者帳號驗證的機制獨立在這個 route file 中，讓程式結構更清晰。內容如下：
```js {linenos=table,hl_lines=[6,13],linenostart=1}
const router = require("express").Router();
const User = require("../models/User");

// 帳號註冊
router.post("/register", async (req, res) => {
    const newUser = new User({
        username: req.body.username,
        email: req.body.email,
        password: req.body.password,
    });

    try {
        const savedUser = await newUser.save();
        res.status(201).json(savedUser);
    } catch (err) {
        res.status(500).json(err);
    }
});

module.exports = router;
```
> 在 index.js 引用這個新的 router

```js {linenos=table,hl_lines=[7,20],linenostart=1}
const express = require("express");
const app =  express();
const mongoose = require("mongoose");
const dotenv = require("dotenv");

const userRoute = require("./routes/user");
const authRoute = require("./routes/auth");

dotenv.config();

mongoose
  .connect(process.env.MONGO_URL)
  .then(()=>console.log("DB Connection successful"))
  .catch((err)=> {
    console.log(err)
  }
);

app.use(express.json());
app.use("/api/users", userRoute);
app.use("/api/auth", authRoute);

app.listen(process.env.PORT || 5000, ()=>{
    console.log("Backend server is running...");
});
```

![image](https://user-images.githubusercontent.com/21993717/165530808-cf061695-7587-4b4a-8201-3dc5a81e6329.png)

> 執行成功後，查看 MongoDB 資料庫的結果：

![image](https://user-images.githubusercontent.com/21993717/165541279-4867d57f-e13a-46fe-b6a9-8ecac198b379.png)

### 使用 crypto.js 來為敏感資料加密
由上述的查詢畫面可以得知 Password 欄位是以明碼儲存，此類敏感資料可以透過加密技術來加強它的安全性。

cryptojs 是一個 javascript 的加解密函數庫，使用前先安裝套件： `$ yarn add crypto-js`

```js
const CryptoJS = require("crypto-js");

  password: CryptoJS.AES.encrypt(req.body.password, process.env.PASS_SEC),
```

encrypt function 參數除了要加密的字串外，需要一個加密 Key，為彈性起見，把它寫在 .env 檔案中

```ini {linenos=table,hl_lines=[2]}
MONGO_URL=mongodb+srv://cal.........
PASS_SEC=cal
```
加密後的 password 如下：
```json {linenos=table,hl_lines=[4]}
{
    "username": "calvin",
    "email": "cal@gmail.com",
    "password": "U2FsdGVkX19F5ULehTVaQfCzw3B0xIefomDIZTTop7g=",
    "isAdmin": false,
    "_id": "6269f53be0bd74d778bb8934",
    "createdAt": "2022-04-28T02:00:27.195Z",
    "updatedAt": "2022-04-28T02:00:27.195Z",
    "__v": 0
}
```

### login function (解密)
繼續完成 auth.js 中的 LOGIN 功能，
```js
// 帳號登錄
router.post("/login", async (req, res) => {
    try {
        const user =  await User.findOne({ username: req.body.username });
        !user && res.status(401).json("Wrong Credentials!");

        const hashedPassword = CryptoJS.AES.decrypt(user.password, process.env.PASS_SEC);
        const orginalPassword = hashedPassword.toString(CryptoJS.enc.Utf8);

        orginalPassword !== req.body.password && res.status(401).json("Wrong Credentials!");

        res.status(200).json(user);
    } catch(err) {
        res.status(500).json(err)
    }
})

```
> 使用 postman 來執行 Login

![image](https://user-images.githubusercontent.com/21993717/165663154-a7bf03ff-4676-42a0-8d36-43ef9a51a181.png)

> 上圖的 User 回傳資料中為安全起見，應該把 password 欄位隱蔵起來。透過javascript 解構語法很容易就可完成這個需求。

```js {lh_lines=[11,12]}
// 帳號登錄
router.post("/login", async (req, res) => {
    try {
        const user =  await User.findOne({ username: req.body.username });
        !user && res.status(401).json("Wrong Credentials!");

        const hashedPassword = CryptoJS.AES.decrypt(user.password, process.env.PASS_SEC);
        const orginalPassword = hashedPassword.toString(CryptoJS.enc.Utf8);

        orginalPassword !== req.body.password && res.status(401).json("Wrong Credentials!");
        
        const { password, ...others } = user._doc;
        res.status(200).json(others);
    } catch(err) {
        res.status(500).json(err)
    }
})
```

```json
{
    "_id": "6269f53be0bd74d778bb8934",
    "username": "calvin",
    "email": "cal@gmail.com",
    "isAdmin": false,
    "createdAt": "2022-04-28T02:00:27.195Z",
    "updatedAt": "2022-04-28T02:00:27.195Z",
    "__v": 0
}
```

## 在 Node.js 中使用 JWT 來進行 Token-Based 的使用者授權驗證

為使我們的應用程式更安全 Token-Based 的授權驗證方式來設計 API

安裝套件：`$ yarn add jsonwebtoken`

### 産生 JWT
在登入證驗中加入産生 Token 的邏輯，在檢核使用者輸入的密碼正確後，將 User ID (_id這個內部 Key)及 isAdmin這兩個屬性值透過 sign function 來産生 access token，並回傳給前端。
```js
const jwt = require("jsonwebtoken");  // 匯入 JsonWebToken套件
...
const accessToken = jwt.sign(
    {
        id: user._id,
        isAdmin: user.isAdmin
    },
    process.env.JWT_SEC,
    { expiresIn: "3d" }
);
...
res.status(200).json({ ...others, accessToken });

```
> 依照貫例將 Json web token 的 key 值放在 .env 檔案中

```ini {linenos=table,hl_lines=[3]}
MONGO_URL=mongodb+srv://cal.......
PASS_SEC=cal
JWT_SEC=cal
```
> 使用 postman 再 Login 一次，會發現回傳的資料中已經包含一個合法的 JWT Token了

![image](https://user-images.githubusercontent.com/21993717/165675174-ccf7c037-0d02-4f0b-b7c9-e96272d08913.png)

### 使用 JWT
在前端取得合法的 JWT Token後，來看看當使用者在呼叫其他 API 時一併回傳的 Token　如何在 server 端來進行驗證。

首先我們要在 routes 目錄中新增一個名為 verifyToken.js 的 express Middleware
```js {linenos=table,hl_lines=[3,17,27]}
const jwt = require("jsonwebtoken");

const verifyToken = (req, res, next) => {
    const authHeader = req.headers.token;
    if (authHeader) {
        const token = authHeader.split(" ")[1];
        jwt.verify(token, process.env.JWT_SEC, (err, user) => {
            if (err) res.status(403).json("Token 不合法！");
            req.user = user;
            next();
        })
    } else {
        return res.status(401).json("未被授權執行本功能！");
    }
};

const verifyTokenAndAuthorization = (req, res, next) => {
    verifyToken(req, res, () => {
        if (req.user.id === req.params.id || req.user.isAdmin) {
            next();
        } else {
            res.status(403).json("不允許執行本功能！");
        }
    })
};

const verifyTokenAndAdmin = (req, res, next) => {
    verifyToken(req, res, () => {
        if (req.user.isAdmin) {
            next();
        } else {
            res.status(403).json("非管理者不允許執行本功能！");
        }
    })
};

module.exports = { verifyToken, verifyTokenAndAuthorization，　verifyTokenAndAdmin };
```

> 在 user.js　Route file 中加入 update username 的功能

```js {linenos=table,hl_lines=[3,5,16]}
const router = require("express").Router();
const User = require("../models/User");
const { verifyToken, verifyTokenAndAuthorization } = require("./verifyToken");

router.put("/:id", verifyTokenAndAuthorization, async (req, res) => {
    if (req.user.id === req.params.id || req.user.isAdmin) {
        if (req.body.password) {
            req.body.password = CryptoJS.AES.encrypt(
                req.body.password,
                process.env.PASS_SEC
            ).toString();
        }
    };

    try {
        const updatedUser = await User.findByIdAndUpdate(
            req.params.id, 
            {
                $set: req.body,
            }, 
            { new: true }
        );
        res.status(200).json(updatedUser);
    } catch(err) {
        res.status(403).json(err);
    }    
});

module.exports = router;
```
> 先取得 user collection 第一個 document 的 key id

![image](https://user-images.githubusercontent.com/21993717/165723311-a70b9768-55b1-4cfa-9357-ed0a56e18349.png)

> 在 postman 新增一個 put 的 request，URL = localhost:5000/api/users/6269f53be0bd74d778bb8934，其中的“6269f53be0bd74d778bb8934”　指的是要變更的 document 的ID。同時在Headers中新加入一個 token 的 header

![image](https://user-images.githubusercontent.com/21993717/165726492-245c4b52-8574-4cb8-b1b5-548fc7a9ad9c.png)

> 其內容值 = "Bearer token_value"，注意 "Bearer" 後要有一個“空白字元”

> 這個 request 的 body 存放的是一個 json 格式的資料，也就是要變更的 username

![image](https://user-images.githubusercontent.com/21993717/165728220-cc81363a-c2d0-4282-851e-2edd5e92bfe5.png)

> 送出 request 成功後回傳值的是修改後的 user document

```json {linenos=table,hl_lines=[3]}
{
    "_id": "6269f53be0bd74d778bb8934",
    "username": "calUpdate",
    "email": "cal@gmail.com",
    "password": "U2FsdGVkX19F5ULehTVaQfCzw3B0xIefomDIZTTop7g=",
    "isAdmin": false,
    "createdAt": "2022-04-28T02:00:27.195Z",
    "updatedAt": "2022-04-28T09:59:33.456Z",
    "__v": 0
}
```

> 若送出 request 中未包含 Token 則會回傳“未被授權執行本功能”。

> 若送出 request 中包含的是不合法的 Token 則會回傳“Token不合法“。

> 緊接著在 user.js　Route file 中再加入 delete user 資料的功能

```js {linenos=table,hl_lines=[3]}
router.delete("/:id", verifyTokenAndAuthorization, async (req, res) => {
    try {
        await User.findByIdAndDelete(req.params.id);
        res.status(200).json(updatedUser);
    } catch(err) {
        res.status(403).json(err);
    }    
});
```

> 另外在 user.js　Route file 中再加入 get user 的功能，而執行本功能的授權檢核邏輯改成 verifyTokenAndAdmin，也就是必須是具有 admin 身份的使用者才能執行本功能。

```js {linenos=table,hl_lines=[3]}
router.get("/find/:id", verifyTokenAndAdmin, async (req, res) => {
    try {
        const user = await User.findById(req.params.id);
        const { password, ...others } = user._doc;
        res.status(200).json(other);
    } catch(err) {
        res.status(500).json(err);
    }    
});
```
> 若你使用的 token 所代表的 user isAdmin=false 的話，將回傳"非管理者不允許執行本功能！"的警告訊息。

![image](https://user-images.githubusercontent.com/21993717/165879359-1ccf574e-7b54-4986-acb2-18d0f1cd9c95.png)

> 我們再新註冊一個新的使用者：

![image](https://user-images.githubusercontent.com/21993717/165879534-4c7f974d-13a8-4241-918d-0a0747e7ae03.png)

> 並在 MongoDB 中把這個新使用者的isAdmin修改成true

![image](https://user-images.githubusercontent.com/21993717/165879787-719b465b-8108-4495-9ab0-6e33e6878684.png)

> 使用這個帳號來登入並取後 token 值。

![image](https://user-images.githubusercontent.com/21993717/165880088-a34e6bca-7c09-4a5d-a25e-f8994ff98f8b.png)

> 使用這個 token 值來呼叫 GET USER 功能就可以正常的取得要查詢的使用者資料了

``` json {linenos=table,hl_lines=[1]}
{
    "_id": "6269f53be0bd74d778bb8934",
    "username": "calvin",
    "email": "cal@gmail.com",
    "isAdmin": false,
    "createdAt": "2022-04-28T02:00:27.195Z",
    "updatedAt": "2022-04-29T02:56:33.670Z",
    "__v": 0
}
```

### 如何處理 URL 中的 query string
> 在　user.js Route file 中再加入 get all users 的功能。

```js {linenos=table,hl_lines=[3,4]}
router.get("/", verifyTokenAndAdmin, async (req, res) => {
    try {
        const users = await User.find();
        res.status(200).json(users);
    } catch(err) {
        res.status(500).json(err);
    }    
});
```
> 在 url 中輸入：localhost:5000/api/users/，將回傳所有的 users 資料

```json
[
    {
        "_id": "6269f53be0bd74d778bb8934",
        "username": "calvin",
        "email": "cal@gmail.com",
        "password": "U2FsdGVkX19F5ULehTVaQfCzw3B0xIefomDIZTTop7g=",
        "isAdmin": false,
        "createdAt": "2022-04-28T02:00:27.195Z",
        "updatedAt": "2022-04-29T02:56:33.670Z",
        "__v": 0
    },
    {
        "_id": "626b55f9af6c24eb6c911f40",
        "username": "admin",
        "email": "admin@gmail.com",
        "password": "U2FsdGVkX190QQrScanCS64GYHup956hNIQ5ALKbjS4=",
        "isAdmin": true,
        "createdAt": "2022-04-29T03:05:29.788Z",
        "updatedAt": "2022-04-29T03:05:29.788Z",
        "__v": 0
    }
]
```
> 如果想要由 url 中加入額外的 query string 來達到動態執行不同的邏輯，應該憅麼做呢？如：localhost:5000/api/users?new=true
> 我們來看看程式中要如何來處理 url 所傳入的 query string

```js {linenos=table,hl_lines=[2,4]}
// GET ALL USERS
router.get("/", verifyTokenAndAdmin, async (req, res) => {
    const query = req.query.new;    // 透過 req.query.new 來取得 new 這個 query string 的值
    try {
        const users = query
            ? await User.find().sort({ _id: -1 }).limit(5)
            : await User.find();
        res.status(200).json(users);
    } catch(err) {
        res.status(500).json(err);
    }    
});
```
> 上述的程式中 User.find().sort({ _id: -1 }).limit(5) 表示的是找出至多五筆的 User 資料並以 _id 來進行‘反’排序，-1表反排序。整個處理還輯表示若在 url 中輸入 localhost:5000/api/users 的話，會回傳所有 User 的資料。若於 url 中輸入 localhost:5000/api/users?new=true，則會回傳 最多五筆並以 _id 來進行‘反’排序的 User 資料。
