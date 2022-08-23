---
title: "使用 React 來建立網路應用程式"
date: 2022-08-23
description: "這是三篇紀錄使用不同的三個 framework(Angular、React、Vue)來建立功能一模一樣的網路應用程式的筆記中的第二篇， 本篇中是使用 React 來建立 Web APP"
tags: ["react", "bootstrap", "redux", "react-redux", "react router", "npm-run-all", "Node.js", "express", "Json Server"]
categories: ["react"]
---
*[github Source code](https://github.com/calvinegs/reactApp.git)*


Angular、React、Vue 是三個最受歡迎的前端框架，這是三篇筆記分別使用這三個不同的框架來建立功能一模一樣的網路應用程式中的第二篇。

這個網路應用程式的需求
- 産品訂購功能
    - 表頭：訂購商品總數、總額及送出訂單功能
    - 商品過濾功能：依産品類別來顯示商品
    - 商品清單：顯示商品明細、商品售價、訂購數量下拉選單及加入購物車等功能
- 訂單確認功能：顯示訂購商品明細及總金額
- 訂購成功功能：顯示訂購完成資訊

![image](https://user-images.githubusercontent.com/21993717/185827887-9df81f19-49fa-4948-adc3-1851acf9dc5a.png)

![image](https://user-images.githubusercontent.com/21993717/185831273-8dd2f971-ea12-4151-aed7-294303430af9.png)

![2022-08-22 11-12-24 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/185831337-ca82deea-dacf-4c88-b3f3-83110f30cb12.png)

![2022-08-22 11-13-51 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/185831442-f3cfe442-673f-4be6-b3e1-a5e71c45533b.png)


使用技術:

- React 18
- Bootstrap 5 (UI Framework)
- React Router 6
- React Redux 8
- NodeJS & Express
- json-server
- npm-run-all (npm 套件)

## 建立新專案

使用下列的 npm init 語法即可建立最新版 Angular 預設的應用程式結構

```bash
$ npx create-react-app reactapp --template typescript

Creating a new React app in /home/egs/cal-data/tech-test/typescript/book/reactapp.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts with cra-template-typescript...


added 1393 packages in 45s

207 packages are looking for funding
  run `npm fund` for details

Initialized a git repository.

Installing template dependencies using npm...

added 35 packages, and changed 1 package in 5s

207 packages are looking for funding
  run `npm fund` for details

We detected TypeScript in your project (src/App.test.tsx) and created a tsconfig.json file for you.

Your tsconfig.json has been populated with default values.

Removing template package using npm...


removed 1 package, and audited 1428 packages in 2s

207 packages are looking for funding
  run `npm fund` for details

6 high severity vulnerabilities

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.

Created git commit.

Success! Created reactapp at /home/egs/cal-data/tech-test/typescript/book/reactapp
Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd reactapp
  npm start

Happy hacking!
```

### 查看 package.json 中相依套件及版本
```bash
$ cd reactapp

$ cat package.json 
{
  "name": "reactapp",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^5.16.5",
    "@testing-library/react": "^13.3.0",
    "@testing-library/user-event": "^13.5.0",
    "@types/jest": "^27.5.2",
    "@types/node": "^16.11.54",
    "@types/react": "^18.0.17",
    "@types/react-dom": "^18.0.6",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "5.0.1",
    "typescript": "^4.7.4",
    "web-vitals": "^2.1.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}
```

### 産生的預設程式結構

![image](https://user-images.githubusercontent.com/21993717/185837091-3fac5ad7-b418-406e-98d2-f790958f6c2f.png)


### 執行由 CLI 産生的預設程式架構
```bash
$ npm i
$ npm start


Compiled successfully!

You can now view reactapp in the browser.

  Local:            http://localhost:3000
  On Your Network:  http://192.168.0.125:3000

Note that the development build is not optimized.
To create a production build, use npm run build.

webpack compiled successfully
No issues found.

```
開啟瀏覽器，輸入 http://localhost:3000/
![image](https://user-images.githubusercontent.com/21993717/186106838-96a4551d-77c6-4edb-befc-ef0567c8597c.png)


```bash
$ git add . && git commit -m "Initial Commit" # 加入 git 新版本
$ code .    # 打開 vscode
```

## 使用 json-server 當作後端 Web Api
在我們的前端應用程式中會使用到後端伺服器的資料，方便起見直接使用 json-server　來當作後端 Web Api

### 安裝 json-server 
`$ npm install --save-dev json-server@0.17.0 npm-run-all@4.1.5`

### 建立給 json-server 使用的資料
```bash
$ touch data.json
# data.json 內容如下
{
  "products": [
    {
      "id": 1,
      "name": "kayak",
      "category": "Watersports",
      "description": "A boat form on person",
      "price": 275
    },
    {
      "id": 2,
      "name": "Lifejacket",
      "category": "Watersports",
      "description": "Protective and fashionable boat form on person",
      "price": 48.95
    },
    {
      "id": 3,
      "name": "Soccer Ball",
      "category": "Soccer",
      "description": "FIFA-approved size and weight",
      "price": 19.5
    },
    {
      "id": 4,
      "name": "Corner Flags",
      "category": "Soccer",
      "description": "Give your playing field a professional touch",
      "price": 34.95
    },
    {
      "id": 5,
      "name": "Stadium",
      "category": "Soccer",
      "description": "Flat-packed 35,000-seat stadium",
      "price": 79500
    },
    {
      "id": 6,
      "name": "Thinking Cap",
      "category": "Chess",
      "description": "Imporove brain efficiency by 75%",
      "price": 16
    },
    {
      "id": 7,
      "name": "Unsteady Chair",
      "category": "Chess",
      "description": "Secretly give your opponent a disadvantage",
      "price": 29.95
    },
    {
      "id": 8,
      "name": "Human Chess Board",
      "category": "Chess",
      "description": "A fun game for the family",
      "price": 75
    },
    {
      "id": 9,
      "name": "Bling Bling King",
      "category": "Chess",
      "description": "Gold-plated, diamond-studded King",
      "price": 1200
    }
  ],
  "orders": [
  ]
}
```
### 測試 json-server
```bash
$ npx json-server data.json -p 4600 # 使用 4600 port


  \{^_^}/ hi!

  Loading data.json
  Done

  Resources
  http://localhost:4600/products
  http://localhost:4600/orders

  Home
  http://localhost:4600

  Type s + enter at any time to create a snapshot of the database
GET /db 304 1.630 ms - -
GET /__rules 404 4.536 ms - 2
```

![image](https://user-images.githubusercontent.com/21993717/185843921-21bd33f3-7721-4b48-83f2-fc9d88757925.png)


![2022-08-22 13-12-47 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/185844008-f153311c-1571-423f-91a2-17d9e5c54853.png)


### 設定 npm script in package.sjon
```json
// ...
	"scripts": {
    "serve": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "json": "json-server data.json -p 4600",
    "start": "npm-run-all -p serve json"
  },
// ...
```

## 在專案中使用 Bootstrap CSS 套件
### 安裝 bootstrap 5
`$ npm install bootstrap@5.2.0`

### 引入 bootstrap in index.tsx

```tsx
// ./src/index.tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import 'bootstrap/dist/css/bootstrap.css';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

## 修改 tsconfig.json

將 "target" 改成 "es2015"

```json  {linenos=table,hl_lines=[3]}
{
  "compilerOptions": {
    "target": "es2015",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    // ...
  }
}  
```

## 為網站加入資料

### 建立資料模型
`$ mkdir ./src/data && touch  ./src/data/entities.ts`

entities.ts 檔案內容如下：

```ts
// ./src/data/entities.ts
export type Product = {
    id: number,
    name: string,
    description: string,
    category: string,
    price: number
};

export class OrderLine {
    constructor(public product: Product, public quantity: number) { }

    get total(): number {
        return this.product.price * this.quantity;
    }
}

export class Order {
    private lines = new Map<number, OrderLine>();

    constructor(initialLines?: OrderLine[]) {
        if (initialLines) {
            initialLines.forEach(ol => this.lines.set(ol.product.id, ol));
        }
    }

    public addProduct(prod: Product, quantity: number) {
        if (this.lines.has(prod.id)) {
            if (quantity === 0) {
                this.removeProduct(prod.id);
            } else {
                this.lines.get(prod.id)!.quantity += quantity;
            }
        } else {
            this.lines.set(prod.id, new OrderLine(prod, quantity));
        }
    }

    public removeProduct(id: number) {
        this.lines.delete(id);
    }
    
    get orderLines(): OrderLine[] {
        return [...this.lines.values()];
    }

    get productCount(): number {
        return [...this.lines.values()]
            .reduce((total, ol) => total += ol.quantity, 0);
    }
    
    get total(): number {
        return [...this.lines.values()].reduce((total, ol) => total += ol.total, 0);
    }
}
```


## 建立商品訂購相關元件
```bash
$ mkdir ./src/pages
$ touch ./src/pages/productItem.tsx
$ touch ./src/pages/categoryList.tsx
$ touch ./src/pages/header.tsx
$ touch ./src/pages/productList.tsx
```
### 商品明細元件 (productItem)
```tsx
// productItem.tsx
import { ChangeEvent, FunctionComponent, useState } from "react";
import { Product } from "../data/entities";

interface Props {
    product: Product,
    callback: (product: Product, quantity: number) => void
}

export const ProductItem: FunctionComponent<Props> = (props) => {
    const [quantity, setQuantity] = useState<number>(1);

    return <div className="card m-1 p-1 bg-light">
        <h4>
            {props.product.name}
            <span className="badge rounded-pill bg-primary float-end">
                ${props.product.price.toFixed(2)}
            </span>
        </h4>
        <div className="card-text bg-white p-1">
            {props.product.description}
            <button className="btn btn-success btn-sm float-end" 
                onClick={() => props.callback(props.product, quantity)} >
                Add To Cart
            </button>
            <select className="form-control-inline float-end m-1" 
                onChange={(ev: ChangeEvent<HTMLSelectElement>) =>
                setQuantity(Number(ev.target.value))}>
                <option>1</option>
                <option>2</option>
                <option>3</option>
            </select>
        </div>
    </div>
}
```

### 商品分類按鈕元件 (categoryList.tsx)
```tsx
// categoryList.tsx
import React, { ChangeEvent, FunctionComponent, useState } from 'react'

interface Props {
    selected: string,
    categories: string[],
    selectCategory: (category: string) => void
}

// export default function productItem() {
export const CategoryList: FunctionComponent<Props> = (props) => {
    return (
        <div className='d-grid gap-2'>
            {["All", ...props.categories].map(c => {
                let btnClass = props.selected === c ? "btn-primary" : "btn-secondary";

                return <button key={c}
                    className={`btn ${btnClass}`}
                    onClick={() => props.selectCategory(c)}>
                    {c}
                </button>
            })}

        </div>
    )
}
```

### 表頭元件 (header)
```tsx
// header.tsx
import { FunctionComponent } from 'react'
import { Order } from '../data/entities'

interface Props {
    order: Order
}

// export default function productItem() {
export const Header: FunctionComponent<Props> = (props) => {
    const count = props.order.productCount;
    return (
        <div className='p-1 bg-secondary text-white text-end'>
            {count === 0 ? "(No Selection)" : `${count} product(s), $${props.order.total.toFixed(2)}`}
            <button className='btn btn-sm btn-primary m-1'>
                Submit Order
            </button>
            
        </div>
    )
}
```

### 商品清單元件 (productList)
```tsx
// productList.tsx
import { ChangeEvent, FunctionComponent, useState } from 'react'
import { Order, Product } from '../data/entities';
import { CategoryList } from './categoryList';
import { Header } from './header';
import { ProductItem } from './productItem';

interface Props {
    products: Product[],
    categories: string[],
    order: Order,
    addToOrder: (product: Product, quantity: number) => void
}

export const ProductList: FunctionComponent<Props> = (props) => {
    const [selectedCategory, setSelectedCategory] = useState<string>("All");
    
    let products : Product[] = props.products.filter(
        p => selectedCategory === "All" || p.category === selectedCategory);
    
    return (
        <div>
            <Header order={props.order} />
            {/* <Header order={Orders} /> */}
            <div className='container-fluid'>
                <div className='row'>
                    <div className='col-3 p-2'>
                        <CategoryList categories={props.categories}
                            selected={selectedCategory}
                            selectCategory={(cat: string) => {
                                setSelectedCategory(cat);
                            }} />
                    </div>
                    <div className='col-9 p-2'>
                        {
                            products.map(p =>
                                <ProductItem key={p.id} product={p}
                                    callback={props.addToOrder} />)
                        }
                    </div>
                </div>
            </div>
        </div>
    )
}
```

### 檔案結構

![2022-08-23 17-19-48 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/186121822-fc1371a9-5207-4bd0-b731-d66fb51fefaa.png)


##　執行程式
```bash
$ npm start
```

![image](https://user-images.githubusercontent.com/21993717/186121980-e8bcc53c-d74e-4b91-b200-56ac297d7e4f.png)


![image](https://user-images.githubusercontent.com/21993717/186122218-b271c697-41b1-4800-8547-72a2596652f7.png)


## 使用 Data Store 來管理 React 應用程式的資料
在大多數的 React 專案中，應用程式的資料是交由 "data store" 資料倉儲來管理，由最多人使用的管理資料套件就是 Redux。

### 安裝 Redux
```bash
$ npm install redux@4.2.0 react-redux@8.0.2
$ npm install --save-dev @types/react-redux@7.1.24
```

### 建立 Redux 的 action 類別
```bash
$ touch ./src/data/types.ts
```
```typescript
// ./src/data/types.ts
import { Action } from "redux";
import { Order, Product } from "./entities";

export interface StoreData {
    products: Product[],
    order: Order
}

export enum ACTIONS {
    ADD_PRODUCTS,
    MODIFY_ORDER,
    RESET_ORDER
}

export interface AddProductsAction extends Action<ACTIONS.ADD_PRODUCTS> {
    payload: Product[]
}

export interface ModifyOrderAction extends Action<ACTIONS.MODIFY_ORDER> {
    payload: {
        product: Product,
        quantity: number
    }
}

export interface ResetOrderAction extends Action<ACTIONS.RESET_ORDER> {}

export type StoreAction = AddProductsAction | ModifyOrderAction | ResetOrderAction;
```

### 建立 action creator

```bash
$ touch ./src/data/actionCreators.ts
```
```typescript
// ./src/data/actionCreators.ts
import { Product } from "./entities";
import { ACTIONS, AddProductsAction, ModifyOrderAction, ResetOrderAction } from "./types";

export const addProduct =
    (...products: Product[]): AddProductsAction => ({
        type: ACTIONS.ADD_PRODUCTS,
        payload: products
    });

export const modifyOrder =
    (product: Product, quantity: number): ModifyOrderAction => ({
        type: ACTIONS.MODIFY_ORDER,
        payload: { product, quantity}
    });

export const resetOrder =
    (product: Product, quantity: number): ResetOrderAction => ({ type: ACTIONS.RESET_ORDER });
```

### 建立 Redux reducer 來處理 action

```bash
$ touch ./src/data/reducer.ts
```
```typescript
import { Reducer } from "redux";
import { Order } from "./entities";
import { ACTIONS, StoreAction, StoreData } from "./types";

export const StoreReducer: Reducer<StoreData, StoreAction>
    = (data: StoreData | undefined , action) => {

        data = data || { products: [], order: new Order()}

        switch (action.type) {
            case ACTIONS.ADD_PRODUCTS:
                return {
                    ...data,
                    products: [...data.products, ...action.payload]
                };
            case ACTIONS.MODIFY_ORDER:
                data.order.addProduct(
                    action.payload.product, action.payload.quantity
                )
                return {...data};
            case ACTIONS.RESET_ORDER:
                return {
                    ...data,
                    order:new Order()
                };
            default:
                return data;
        }
    }
```

### 建立 Data store

```bash
$ touch ./src/data/dataStore.ts
```
```typescript
import { createStore, Store } from "redux";
import { StoreReducer } from "./reducer";
import { StoreAction, StoreData } from "./types";

export const dataStore: Store<StoreData, StoreAction>
    = createStore(StoreReducer);
```


## 新增 HTTP 請求類別

### 安裝 axios

```bash
$ npm install axios@0.27.2
```
### 建立 httpHandler.ts
```bash
$ touch ./src/data/httpHandler.ts
```

```typescript
import Axios from "axios";
import { Order, Product } from "./entities";

const protocol = "http";
const hostname = "localhost";
const port = 4600;
const urls = {
    products: `${protocol}://${hostname}:${port}/products`,
    orders: `${protocol}://${hostname}:${port}/orders`,
};

export class HttpHandler {
    loadProducts(callback: (producs: Product[]) => void): void {
        Axios.get(urls.products).then(response => callback(response.data))        
    }

    storeOrder(order: Order, callback: (id: number) => void): void {
        let orderData = {
            lines: [...order.orderLines.values()].map(ol => ({
                productId: ol.product.id,
                productName: ol.product.name,
                quantity: ol.quantity
            }))
        }
        Axios.post(urls.orders, orderData).then(response => callback(response.data.id));
    }
}
```

## 連結 Data Store 與各個元件
使用 React-Redux 套件來將應用程式元件連接到 Redux data store。

```bash
$ touch .src/data/productLitConnector.ts
```

```typescript
import { connect } from "react-redux";
import { ProductList } from "../pages/productList";
import { modifyOrder } from "./actionCreators";
import { StoreData } from "./types";

const mapStateToProps = (data: StoreData) => ({
    products: data.products,
    categories: [...new Set(data.products.map(p => p.category))],
    order: data.order
})

const mapDispatchToProps = {
    addToOrder: modifyOrder
}

const connectFunction = connect(mapStateToProps, mapDispatchToProps);

export const ConnectedProductList = connectFunction(ProductList);
```

## 修改 App.tsx
```tsx
import React, { FunctionComponent, useState } from 'react';
import { Provider } from 'react-redux';
import { addProduct } from './data/actionCreators';
import { dataStore } from './data/dataStore';
import { HttpHandler } from './data/httpHandler';
import { ConnectedProductList } from './data/productLitConnector';

interface Props { }

let httpHandler = new HttpHandler();
httpHandler.loadProducts(data => dataStore.dispatch(addProduct(...data)));

export const App: FunctionComponent<Props> = (props) => {
  let submitCallback = () => {
    console.log("Submit order");
  };

  return <div className='App'>
    <Provider store={dataStore}>
      <ConnectedProductList />
    </Provider>
  </div>
}

export default App;
```

## 執行測試

程式至此已將資料連結由 local 改到 Json-Server，並將取得的資料放入 Redux data store 中了。

```bash
$ npm start
```
![image](https://user-images.githubusercontent.com/21993717/186132903-1b1af1ec-fb05-4a43-8ea3-0e95dfb45951.png)


## 完成其他功能
接下來我們要完成其餘的二個功能：訂單確認功能及訂購成功功能，在我們已完成的商品訂購元件與前述二個元件中做切換時，會需要使用到URL路由功能，在 React 核心中並未內建路由功能，所以我們必須安裝額外的套件 -- React Router 套件。

### 安裝 React Router 套件
```bash
$ npm install react-router-dom@6.3.0
$ npm install --save-dev @types/react-router-dom5.3.3
```

### URL 路由設定

修改 App.tsx

```tsx
import { FunctionComponent } from 'react';
import { Route, Routes, useNavigate } from 'react-router-dom';
import { addProduct } from './data/actionCreators';
import { dataStore } from './data/dataStore';
import { Order } from './data/entities';
import { HttpHandler } from './data/httpHandler';
import { ConnectedProductList } from './data/productLitConnector';

interface Props { }

let httpHandler = new HttpHandler();
httpHandler.loadProducts(data => dataStore.dispatch(addProduct(...data)));

export const App: FunctionComponent<Props> = (props) => {
  
  const navigate = useNavigate();
  let submitCallback = () => {
    httpHandler.storeOrder(dataStore.getState().order, 
      id => navigate(`/summary/${id}`, { replace: true}));
    dataStore.getState().order = new Order();
  };

  return <div className='App'>
    <Routes>
      <Route index element={<ConnectedProductList/>}/>
      <Route path="/products" element={<ConnectedProductList/>}/>
      {/* <Route path="/order" element={<OrderDetails 
        submitCallback={() => 
          submitCallback()}/>} />
      <Route path="/summary/:id" element={<Summary />} /> */}
    </Routes>
  </div>
}

export default App;
```

修改 index.tsx
```tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import 'bootstrap/dist/css/bootstrap.css';
import { BrowserRouter } from 'react-router-dom';
import { Provider } from 'react-redux';
import { dataStore } from './data/dataStore';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(
  <React.StrictMode>
    <Provider store={dataStore}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </Provider>
  </React.StrictMode>
);
```


### 加入訂單確認元件
`$ touch ./src/pages/orderDetails.tsx`

```tsx
import { connect } from "react-redux"
import { FunctionComponent } from "react"
import { NavLink } from "react-router-dom"
import { Order } from "../data/entities"
import { StoreData } from "../data/types"

const mapStateToProps = (data: StoreData) => ({
    order: data.order
})

interface Props {
    order: Order,
    submitCallback: () => void
}

const OrderDetailComponent: FunctionComponent<Props> = (props) => {
    return <div>
        <h3 className="text-center bg-primary text-white p-2">
            Order Summary
        </h3>
        <div className="p-3">
            <table className="table table-sm table-striped">
                <thead>
                    <tr>
                        <th>Quantity</th>
                        <th>Product</th>
                        <th className="text-end">Price</th>
                        <th className="text-end">Subtotal</th>
                    </tr>
                </thead>
                <tbody>
                    {props.order.orderLines.map(line =>
                        <tr key={line.product.id}>
                            <td>{line.quantity}</td>
                            <td>{line.product.name}</td>
                            <td className="text-end">{line.product.price.toFixed(2)}</td>
                            <td className="text-end">{line.total.toFixed(2)}</td>
                        </tr>
                    )}
                </tbody>
                <tfoot>
                    <tr>
                        <th className="text-end" colSpan={3}>Total:</th>
                        <th className="text-end" colSpan={3}>${props.order.total.toFixed(2)}</th>
                    </tr>
                </tfoot>
            </table>
        </div>
        <div className="text-center">
            <NavLink to="/products" className="btn btn-secondary m-1">
                Back
            </NavLink>
            <button className="btn btn-primary m-1" onClick={props.submitCallback}>
                Submit Order
            </button>

            {/* <NavLink to="/summary" className="btn btn-primary m-1">
                Submit Order
            </NavLink> */}
        </div>
    </div>
}

const connectFunction = connect(mapStateToProps);

export const OrderDetails = connectFunction(OrderDetailComponent);
```

### 加入訂購成功元件

`$ touch ./src/pages/summary.tsx`

```tsx
import { FunctionComponent } from "react"
import { NavLink } from "react-router-dom"
import { useParams } from "react-router-dom"

interface Params {
    id: string
}

export const Summary: FunctionComponent = () => {
    let params = useParams();
    const id = params.id;

    return <div className="m-2 text-center">
        <h2>Thanks!</h2>
        <p>Thanks for placing your order.</p>
        <p>Your order is #{id}</p>
        <p>We'll ship your goods as soon as possible.</p>
        <NavLink to="/products" className="btn btn-primary">OK</NavLink>
    </div>
}
```

### 修改 Header.tsx URL 轉址功能
```tsx
// header.tsx

```

### 完成路由設定
```tsx
// App.tsx
import { FunctionComponent } from 'react';
import { Route, Routes, useNavigate } from 'react-router-dom';
import { addProduct } from './data/actionCreators';
import { dataStore } from './data/dataStore';
import { Order } from './data/entities';
import { HttpHandler } from './data/httpHandler';
import { ConnectedProductList } from './data/productLitConnector';
import { OrderDetails } from './pages/orderDetails';
import { Summary } from './pages/summary';

interface Props { }

let httpHandler = new HttpHandler();
httpHandler.loadProducts(data => dataStore.dispatch(addProduct(...data)));

export const App: FunctionComponent<Props> = (props) => {
  const navigate = useNavigate();
  let submitCallback = () => {
    httpHandler.storeOrder(dataStore.getState().order, 
      id => navigate(`/summary/${id}`, { replace: true}));
    dataStore.getState().order = new Order();
  };

  return <div className='App'>
    <Routes>
      <Route index element={<ConnectedProductList/>}/>
      <Route path="/products" element={<ConnectedProductList/>}/>
      <Route path="/order" element={<OrderDetails 
        submitCallback={() => 
          submitCallback()}/>} />
      <Route path="/summary/:id" element={<Summary />} />
    </Routes>
  </div>
}

export default App;
```

### 執行測試

![image](https://user-images.githubusercontent.com/21993717/186138695-887ae2e6-e0a1-4ea6-85fc-6f18d67bbebf.png)

![image](https://user-images.githubusercontent.com/21993717/186138853-f5df2d71-6ece-483e-b16e-fe38cc60ff47.png)

![image](https://user-images.githubusercontent.com/21993717/186138894-3224da0c-4531-4c51-9b5a-9c7fd26409d6.png)



## 佈署應用程式
使用 NodeJS & Express 來當作 Http Server & Web Api Server

### 安裝 express 套件 & -connect-history-api-fallback 套件

`$ npm install --save-dev express@4.18.1 connect-history-api-fallback@2.0.0`

### 建立 node 伺服器
`$ touch server.js`

```js
const express = require("express");
const jsonServer = require("json-server");
const history = require("connect-history-api-fallback");
const app = express();

app.use(history());
app.use("/", express.static("build"));

const router = jsonServer.router("data.json");
app.use(jsonServer.bodyParser);
app.use("/api", (req, res, next) => router(req, res, next));

const port = process.argv[3] || 4002;
app.listen(port, ()=> console.log(`Running on port ${port}`));
```

### 修改 url 路徑
修改 httpHandler.ts
```typescript
import Axios from "axios";
import { Order, Product } from "./entities";

// const protocol = "http";
// const hostname = "localhost";
// const port = 4600;
// const urls = {
//     products: `${protocol}://${hostname}:${port}/products`,
//     orders: `${protocol}://${hostname}:${port}/orders`,
// };
const urls = {
    products: "/api/products",
    orders: "/api/orders"
}

export class HttpHandler {
    loadProducts(callback: (producs: Product[]) => void): void {
        Axios.get(urls.products).then(response => callback(response.data))        
    }

    storeOrder(order: Order, callback: (id: number) => void): void {
        let orderData = {
            lines: [...order.orderLines.values()].map(ol => ({
                productId: ol.product.id,
                productName: ol.product.name,
                quantity: ol.quantity
            }))
        }
        Axios.post(urls.orders, orderData).then(response => callback(response.data.id));
    }
}
```

### 建置 React App
```bash
$ npm run build

> reactapp@0.1.0 build
> react-scripts build

Creating an optimized production build...
Compiled with warnings.

[eslint] 
src/pages/categoryList.tsx
  Line 1:17:  'ChangeEvent' is defined but never used  @typescript-eslint/no-unused-vars
  Line 1:49:  'useState' is defined but never used     @typescript-eslint/no-unused-vars

src/pages/productList.tsx
  Line 1:10:  'ChangeEvent' is defined but never used  @typescript-eslint/no-unused-vars

src/pages/summary.tsx
  Line 5:11:  'Params' is defined but never used  @typescript-eslint/no-unused-vars

Search for the keywords to learn more about each warning.
To ignore, add // eslint-disable-next-line to the line before.

File sizes after gzip:

  63.26 kB (-45 B)  build/static/js/main.2ece24f2.js
  27.93 kB          build/static/css/main.24b68c36.css

The project was built assuming it is hosted at /.
You can control this with the homepage field in your package.json.

The build folder is ready to be deployed.
You may serve it with a static server:

  npm install -g serve
  serve -s build

Find out more about deployment here:

  https://cra.link/deployment


$ ls -al build

總用量 52
drwxrwxr-x 3 egs egs 4096  八  23 18:50 .
drwxrwxr-x 7 egs egs 4096  八  23 18:46 ..
-rw-rw-r-- 1 egs egs  369  八  23 18:50 asset-manifest.json
-rw-rw-r-- 1 egs egs 3870  八  23 18:50 favicon.ico
-rw-rw-r-- 1 egs egs  644  八  23 18:50 index.html
-rw-rw-r-- 1 egs egs 5347  八  23 18:50 logo192.png
-rw-rw-r-- 1 egs egs 9664  八  23 18:50 logo512.png
-rw-rw-r-- 1 egs egs  492  八  23 18:50 manifest.json
-rw-rw-r-- 1 egs egs   67  八  23 18:50 robots.txt
drwxrwxr-x 4 egs egs 4096  八  23 18:50 static
```

### 執行
```bash
$ node server.js

Running on port 4002
```

![image](https://user-images.githubusercontent.com/21993717/186140693-2c840a37-c955-47fb-86c2-1499ba0ac85b.png)

功能完成後的程式檔案結構：

![image](https://user-images.githubusercontent.com/21993717/186141275-ff042832-f010-41ef-b9b6-b1390c33f292.png)