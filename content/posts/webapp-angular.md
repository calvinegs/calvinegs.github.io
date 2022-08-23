---
title: "使用 Angular 來建立網路應用程式"
date: 2022-08-21
description: "接下來的三篇筆記分別要紀錄使用不同的三個 framework(Angular、React、Vue)來建立功能一模一樣的網路應用程式， 而在本篇筆記中是使用 Angular(14) 來建立 Web APP"
tags: ["angular", "bootstrap", "npm-run-all", "Node.js", "express", "Json Server"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angularApp.git)*


Angular、React、Vue 是三個最受歡迎的前端框架，接下來的三篇筆記分別要紀錄使用這三個不同的框架來建立功能一模一樣的網路應用程式。

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

- Angular 14
- Bootstrap 5 (UI Framework)
- Angular 
    - Router
    - Services
- NodeJS & Express
- json-server
- npm-run-all (npm 套件)

## 建立新專案

使用下列的 npm init 語法即可建立最新版 Angular 預設的應用程式結構

```bash
$ npm init @angular angularapp -- --routing --style=scss

Need to install the following packages:
  @angular/create@14.1.3
Ok to proceed? (y) 
CREATE angularapp/README.md (1064 bytes)
CREATE angularapp/.editorconfig (274 bytes)
CREATE angularapp/.gitignore (548 bytes)
CREATE angularapp/angular.json (3160 bytes)
CREATE angularapp/package.json (1041 bytes)
CREATE angularapp/tsconfig.json (863 bytes)
CREATE angularapp/.browserslistrc (600 bytes)
CREATE angularapp/karma.conf.js (1427 bytes)
CREATE angularapp/tsconfig.app.json (287 bytes)
CREATE angularapp/tsconfig.spec.json (333 bytes)
CREATE angularapp/.vscode/extensions.json (130 bytes)
CREATE angularapp/.vscode/launch.json (474 bytes)
CREATE angularapp/.vscode/tasks.json (938 bytes)
CREATE angularapp/src/favicon.ico (948 bytes)
CREATE angularapp/src/index.html (296 bytes)
CREATE angularapp/src/main.ts (372 bytes)
CREATE angularapp/src/polyfills.ts (2338 bytes)
CREATE angularapp/src/styles.scss (80 bytes)
CREATE angularapp/src/test.ts (749 bytes)
CREATE angularapp/src/assets/.gitkeep (0 bytes)
CREATE angularapp/src/environments/environment.prod.ts (51 bytes)
CREATE angularapp/src/environments/environment.ts (658 bytes)
CREATE angularapp/src/app/app-routing.module.ts (245 bytes)
CREATE angularapp/src/app/app.module.ts (393 bytes)
CREATE angularapp/src/app/app.component.scss (0 bytes)
CREATE angularapp/src/app/app.component.html (23115 bytes)
CREATE angularapp/src/app/app.component.spec.ts (1085 bytes)
CREATE angularapp/src/app/app.component.ts (215 bytes)
✔ Packages installed successfully.
    Successfully initialized git.
```

### 查看 package.json 中相依套件及版本
```bash
$ cd angularapp

$ cat package.json 
{
  "name": "angularapp",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "^14.1.0",
    "@angular/common": "^14.1.0",
    "@angular/compiler": "^14.1.0",
    "@angular/core": "^14.1.0",
    "@angular/forms": "^14.1.0",
    "@angular/platform-browser": "^14.1.0",
    "@angular/platform-browser-dynamic": "^14.1.0",
    "@angular/router": "^14.1.0",
    "rxjs": "~7.5.0",
    "tslib": "^2.3.0",
    "zone.js": "~0.11.4"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "^14.1.3",
    "@angular/cli": "~14.1.3",
    "@angular/compiler-cli": "^14.1.0",
    "@types/jasmine": "~4.0.0",
    "jasmine-core": "~4.2.0",
    "karma": "~6.4.0",
    "karma-chrome-launcher": "~3.1.0",
    "karma-coverage": "~2.2.0",
    "karma-jasmine": "~5.1.0",
    "karma-jasmine-html-reporter": "~2.0.0",
    "typescript": "~4.7.2"
  }
}
```

### 産生的預設程式結構

![image](https://user-images.githubusercontent.com/21993717/185837091-3fac5ad7-b418-406e-98d2-f790958f6c2f.png)


### 執行由 CLI 産生的預設程式架構
```bash
$ npm i
$ npm start


> angularapp@0.0.0 start
> ng serve

✔ Browser application bundle generation complete.

Initial Chunk Files   | Names         |  Raw Size
vendor.js             | vendor        |   2.04 MB | 
polyfills.js          | polyfills     | 315.29 kB | 
styles.css, styles.js | styles        | 207.84 kB | 
main.js               | main          |  49.85 kB | 
runtime.js            | runtime       |   6.52 kB | 

                      | Initial Total |   2.61 MB

Build at: 2022-08-22T03:50:11.996Z - Hash: fe04b1895edac604 - Time: 8579ms

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **


✔ Compiled successfully.
```
開啟瀏覽器，輸入 http://localhost:4200/

![2022-06-15 11-26-34](https://user-images.githubusercontent.com/21993717/173730523-94d3c19e-0982-42e1-878a-438f53d558b6.png)


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
	"serve": "ng serve",
	"json": "json-server data.jus -p 4600",
    "start": "npm-run-all -p serve json"
```

## 在專案中使用 Bootstrap CSS 套件
### 安裝 bootstrap 5
`$ npm install bootstrap@5.2.0`

### 引入 bootstrap in angular.json

```json
	"architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:browser",
          "options": {
            "outputPath": "dist/angularapp",
            "index": "src/index.html",
            "main": "src/main.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "tsconfig.app.json",
            "inlineStyleLanguage": "scss",
            "assets": [
              "src/favicon.ico",
              "src/assets"
            ],
            "styles": [
              "src/styles.scss",
              "node_modules/bootstrap/dist/css/bootstrap.min.css"
            ],
```

### 使用 bootstrap
- 修改 app.component.ts
- 修改 app.component.html

```ts
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'angularapp';
  names: string[] = ["John", "Alian", "James"];
}
```

```html
<!-- app.component.html -->
<h4 class="bg-primary text-white text-center p-2">Names</h4>
<ul>
  <li *ngFor="let name of names">{{ name }}</li>
</ul>
```

![2022-08-22 16-30-25 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/185876517-57c469c3-97d0-4454-be1d-cf0caab89f42.png)

## 為網站加入資料

### 建立資料模型
`$ npx ng g class data/entities  --skip-tests`

```ts
// entities.ts
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

### 建立資料來源 服務程式
`$ npx ng g s data/dataSource`

```ts
// data-source.service.ts
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { Order, Product } from './entities';

export type ProductProp = keyof Product;

export abstract class DataSourceBase {
  abstract loadProducts(): Observable<Product[]>;
  abstract storeOrder(order: Order): Observable<number>;
}
@Injectable({
  providedIn: 'root'
})
export class DataSourceService {
  private _products: Product[];
  private _categories: Set<string>;
  public order: Order;

  constructor(private impl: DataSourceBase) { 
    this._products = [];
    this._categories = new Set<string>();
    this.order = new Order();
    this.getData();
  }

  getProducts(sortProp: ProductProp = "id", category?: string): Product[]{
    return this.selectProducts(this._products, sortProp, category);
  }

  protected getData(): void {
    this._products = [];
    this._categories.clear();
    this.impl.loadProducts().subscribe(rawData => {
      rawData.forEach(p => {
        this._products.push(p);
        this._categories.add(p.category);
      });
    });
  }

  protected selectProducts(prods: Product[], sortProp: ProductProp, category?: string): Product[] {
    return prods.filter(p =>
      category === undefined || p.category == category)
      .sort((p1, p2) => p1[sortProp] ? 1 : 0);
  }

  getCategories(): string[] {
    return [...this._categories.values()];
  }

  storeOrder(): Observable<number> {
    return this.impl.storeOrder(this.order);
  }
}
```

### 實作資料來源　服務程式
`$ npx ng g s data/remoteDataSource`

```ts
// remote-data-source.service.ts
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { DataSourceBase } from './data-source.service';
import { Order, Product } from './entities';
import { HttpClient } from '@angular/common/http';

const protocol = "http";
const hostname= "localhost";
const prot = 4600;
const urls = {
  products: `${protocol}://${hostname}:${prot}/products`,
  orders: `${protocol}://${hostname}:${prot}/orders`
}

@Injectable({
  providedIn: 'root'
})
export class RemoteDataSourceService extends DataSourceBase {
  constructor(private http: HttpClient) { 
    super();
  }

  loadProducts(): Observable<Product[]> {
    return this.http.get<Product[]>(urls.products);
  }

  storeOrder(order: Order): Observable<number> {
    let orderData = {
      lines: [...order.orderLines.values()].map(ol => ({
        productId: ol.product.id,
        productName: ol.product.name,
        quantity: ol.quantity
      }))
    }
    return this.http.post<{ id: number }>(urls.orders, orderData).pipe<number>(map(val => val.id));
  }
}
```

### 設定資料來源 模組
`$ npx ng g module data`

```ts
// data.module.ts
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';
import { DataSourceBase } from './data-source.service';
import { RemoteDataSourceService } from './remote-data-source.service';

@NgModule({
  imports: [
    HttpClientModule
  ],
  providers: [
    { provide: DataSourceBase, useClass: RemoteDataSourceService}
  ]
})
export class DataModule { }
```

## 建立商品訂購相關元件
```bash
$ npx ng g c pages/productItem
$ npx ng g c pages/categoryList
$ npx ng g c pages/header
$ npx ng g c pages/productList
```
### 商品明細元件 (productItem)
```typescript
// product-item.component.ts
import { Component, EventEmitter, Input, OnInit, Output } from '@angular/core';
import { Product } from 'src/app/data/entities';

export type productSelection = {
  product: Product,
  quantity: number
}
@Component({
  selector: 'app-product-item',
  templateUrl: './product-item.component.html',
  styleUrls: ['./product-item.component.scss']
})
export class ProductItemComponent {
  quantity: number = 1;

  @Input() product!: Product;

  @Output() addToCart = new EventEmitter<productSelection>();

  handleAddToCart() {
    this.addToCart.emit({
      product: this.product,
      quantity: Number(this.quantity)
    })
  }
}
```

```html
// product-item.component.html
<div class="card m-1 p-1 bg-light">
    <h4>
        {{ product.name }}
        <span class="badge rounded-pill bg-primary float-end">
            ${{ product.price.toFixed(2) }}
        </span>
    </h4>
    <div class="card-text bg-white p-1">
        {{ product.description }}
        <button class="btn btn-success btn-sm float-end" (click)="handleAddToCart()">
            Add To Cart
        </button>
        <select title="quantity" class="form-control-inline float-end m-1" [(ngModel)]="quantity">
            <option>1</option>
            <option>2</option>
            <option>3</option>
        </select>
    </div>
</div>
```

### 商品分類按鈕元件 (categoryList)
```typescript
// category-list.component.ts
import { Component, EventEmitter, Input, OnInit, Output } from '@angular/core';

@Component({
  selector: 'app-category-list',
  templateUrl: './category-list.component.html',
  styleUrls: ['./category-list.component.scss']
})
export class CategoryListComponent implements OnInit {
  @Input() selected!: string;
  @Input() categories!: string[];
  @Output() selectCategory = new EventEmitter<string>();

  constructor() { }

  ngOnInit(): void {
  }

  getBtnClass(category: string): string {
    return "btn " + (category === this.selected ? "btn-primary" : "btn-secondary");
  }
}
```

```html
// category-list.component.html
<button *ngFor="let category of categories" [class]="getBtnClass(category)" (click)="selectCategory.emit(category)">
    {{ category }}
</button>
```

### 表頭元件 (header)
```typescript
// header.component.ts
import { Component, EventEmitter, Input, OnInit, Output } from '@angular/core';
import { Order } from 'src/app/data/entities';

@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.scss']
})
export class HeaderComponent implements OnInit {
  @Input() order!: Order;
  @Output() submit = new EventEmitter<void>();

  constructor() { }

  ngOnInit(): void {
  }

  get headerText(): string {
    let count = this.order.productCount;
    return count === 0 ? "(No Selection)" : `${count} product(s), $${this.order.total.toFixed(2)}`
  }
}
```

```html
// header.component.html
<div class="p-1 bg-secondary text-white text-end">
    {{ headerText }}
    <button class="btn btn-sm btn-primary m-1" (click)="submit.emit()">
        Submit Order
    </button>
</div>
```

### 商品清單元件 (productList)
```typescript
// product-list.component.ts
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
import { DataSourceService } from 'src/app/data/data-source.service';
import { Order, Product } from 'src/app/data/entities';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.scss']
})
export class ProductListComponent implements OnInit {
  selectedCategory = "All";

  constructor(public dataSource: DataSourceService) { 
  }

  ngOnInit(): void {
  }

  get products(): Product[] {
    return this.dataSource.getProducts("id", this.selectedCategory === "All" ? undefined : this.selectedCategory);
  }

  get categories():string[] {
    return ["All", ...this.dataSource.getCategories()];
  }

  handleCategorySelect(category: string) {
    this.selectedCategory = category;
  }

  handleAdd(data: { product: Product, quantity: number }) {
    this.dataSource.order.addProduct(data.product, data.quantity);
  }

  handleSubmit() {
    console.log("SUBMIT");
  }

}
```
```html
// product-list.component.html
<app-header [order]="dataSource.order" (submit)="handleSubmit()"></app-header>
<div class="container-fluid">
    <div class="row">
        <div class="col-3 p-2">
            <app-category-list class="d-grid gap-2"
                [selected]="selectedCategory" [categories]="categories"
                (selectCategory)="handleCategorySelect($event)">
            </app-category-list>
        </div>
        <div class="col-9 p-2">
            <app-product-item *ngFor="let p of products"
                [product]="p" (addToCart)="handleAdd($event)"></app-product-item>
        </div>
    </div>
</div>
```

### AppComponent 修改
```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  // title = 'angularapp';
  // names: string[] = ["John", "Alian", "James"];
}
```

```html
<app-product-list></app-product-list>
```

### AppModule 設定
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { ProductItemComponent } from './pages/product-item/product-item.component';
import { CategoryListComponent } from './pages/category-list/category-list.component';
import { HeaderComponent } from './pages/header/header.component';
import { ProductListComponent } from './pages/product-list/product-list.component';
import { FormsModule } from '@angular/forms';
import { DataModule } from './data/data.module';

@NgModule({
  declarations: [
    AppComponent,
    ProductItemComponent,
    CategoryListComponent,
    HeaderComponent,
    ProductListComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    FormsModule,
    DataModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### 檔案結構

![2022-08-22 17-12-48 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/185885258-47deb96a-ad0e-4808-a7ed-b4ffac79c788.png)


##　執行程式
```bash
$ npm start
```

![2022-08-22 17-41-36 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/185891476-65139eae-17ce-41af-bec8-2e5cd2925314.png)


![image](https://user-images.githubusercontent.com/21993717/185891784-1c365627-42e2-4a0f-a715-e47bbcee5a9d.png)


## 完成其他功能

### 加入訂單確認元件
`$ npx ng g c pages/orderDetails`

```typescript
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
import { DataSourceService } from 'src/app/data/data-source.service';
import { Order } from 'src/app/data/entities';

@Component({
  selector: 'app-order-details',
  templateUrl: './order-details.component.html',
  styleUrls: ['./order-details.component.scss']
})
export class OrderDetailsComponent implements OnInit {

  constructor(private dataSource: DataSourceService, private router: Router) { }

  ngOnInit(): void {
  }

  get order(): Order {
    return this.dataSource.order;
  }

  submit() {
    this.dataSource.storeOrder().subscribe(id =>
      this.router.navigateByUrl(`/summary/${id}`));
  }  
}
```

```html
<h3 class="text-center bg-primary text-white p-2">Order Summary</h3>
<div class="p-3">
    <table class="table table-sm table-stgriped">
        <thead>
            <tr>
                <th>Quantity</th>
                <th>Product</th>
                <th class="text-end">Price</th>
                <th class="text-end">Subtotal</th>
            </tr>
        </thead>
        <tbody>
            <tr *ngFor="let line of order.orderLines">
                <td>{{ line.quantity }}</td>
                <td>{{ line.product.name }}</td>
                <td class="text-end">{{ line.product.price.toFixed(2) }}</td>
                <td class="text-end">{{ line.total.toFixed(2) }}</td>
            </tr>
        </tbody>
        <tfoot>
            <tr>
                <th class="text-end" colSpan="3">Total:</th>
                <th class="text-end">
                    ${{ order.total.toFixed(2) }}
                </th>
            </tr>
        </tfoot>
    </table>
</div>
<div class="text-center">
    <button class="btn btn-secondary m-1" routerLink="/products">Back</button>
    <button class="btn btn-primary m-1" (click)="submit()">Submit Order</button>
</div>
```

### 加入訂購成功元件
`$ npx ng g c pages/summary`

```typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-summary',
  templateUrl: './summary.component.html',
  styleUrls: ['./summary.component.scss']
})
export class SummaryComponent implements OnInit {

  constructor(private activatedRoute: ActivatedRoute) { }

  ngOnInit(): void {
  }

  get id(): string {
    return this.activatedRoute.snapshot.params["id"];
  }
}
```

```html
<div class="m-2 text-center">
    <h2>Thanks!</h2>
    <p>Thanks for placing your order.</p>
    <p>Your order is #{{ id }}</p>
    <p>We'll ship your goods as soon as possible.</p>
    <button class="btn btn-primary" routerLink="/products">OK</button>
</div>
```

### 修改路由設定
```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { OrderDetailsComponent } from './pages/order-details/order-details.component';
import { ProductListComponent } from './pages/product-list/product-list.component';
import { SummaryComponent } from './pages/summary/summary.component';

const routes: Routes = ([
  { path: "products", component: ProductListComponent },
  { path: "order", component: OrderDetailsComponent },
  { path: "summary/:id", component: SummaryComponent },
  { path: "", redirectTo: "/products", pathMatch: "full" }
 ]);
 

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

```typescript {linenos=table,hl_lines=[15-16,39]}
// product-list.component.ts
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
import { DataSourceService } from 'src/app/data/data-source.service';
import { Order, Product } from 'src/app/data/entities';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.scss']
})
export class ProductListComponent implements OnInit {
  selectedCategory = "All";

  constructor(public dataSource: DataSourceService, private router: Router) { 
    this.dataSource.order = new Order;
  }

  ngOnInit(): void {
  }

  get products(): Product[] {
    return this.dataSource.getProducts("id", this.selectedCategory === "All" ? undefined : this.selectedCategory);
  }

  get categories():string[] {
    return ["All", ...this.dataSource.getCategories()];
  }

  handleCategorySelect(category: string) {
    this.selectedCategory = category;
  }

  handleAdd(data: { product: Product, quantity: number }) {
    this.dataSource.order.addProduct(data.product, data.quantity);
  }

  handleSubmit() {
    this.router.navigateByUrl("/order");
  }

}
```

```html
<!-- app.component.html -->
<router-outlet></router-outlet>
```

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
app.use("/", express.static("dist/angularapp"));

const router = jsonServer.router("data.json");
app.use(jsonServer.bodyParser);
app.use("/api", (req, res, next) => router(req, res, next));

const port = process.argv[3] || 4001;
app.listen(port, ()=> console.log(`Running on port ${port}`));
```

### 修改 url 路徑
修改 remoteDataSource.ts
```typescript
// const protocol = "http";
// const hostname= "localhost";
// const prot = 4600;
// const urls = {
//   products: `${protocol}://${hostname}:${prot}/products`,
//   orders: `${protocol}://${hostname}:${prot}/orders`
// }
const urls = {
  products: `/api/products`,
  orders: `/api/orders`
}
```

### 建置 Angular App
```bash
$ npx ng build

> angularapp@0.0.0 build
> ng build

✔ Browser application bundle generation complete.
✔ Copying assets complete.
⠋ Generating index html...1 rules skipped due to selector errors:
  legend+* -> Cannot read properties of undefined (reading 'type')
✔ Index html generation complete.

Initial Chunk Files           | Names         |  Raw Size | Estimated Transfer Size
main.ea031ef95dc394fc.js      | main          | 236.73 kB |                61.78 kB
styles.698134ce9b91eefb.css   | styles        | 187.70 kB |                19.36 kB
polyfills.d575d5a0c2fddf81.js | polyfills     |  33.07 kB |                10.63 kB
runtime.816becf396788283.js   | runtime       |   1.04 kB |               598 bytes

                              | Initial Total | 458.56 kB |                92.36 kB

Build at: 2022-08-22T10:34:24.214Z - Hash: 10534740714270dd - Time: 9530ms


$ ls -al dist/angularapp/
總用量 500
drwxrwxr-x 2 egs egs   4096  八  22 18:19 .
drwxrwxr-x 3 egs egs   4096  八  22 18:19 ..
-rw-rw-r-- 1 egs egs  14114  八  22 18:19 3rdpartylicenses.txt
-rw-rw-r-- 1 egs egs    948  八  22 18:19 favicon.ico
-rw-rw-r-- 1 egs egs   3043  八  22 18:19 index.html
-rw-rw-r-- 1 egs egs 242506  八  22 18:19 main.1386edd316823fa4.js
-rw-rw-r-- 1 egs egs  33867  八  22 18:19 polyfills.d575d5a0c2fddf81.js
-rw-rw-r-- 1 egs egs   1070  八  22 18:19 runtime.816becf396788283.js
```

### 執行
```bash
$ node server.js

Running on port 4001
```

![image](https://user-images.githubusercontent.com/21993717/185901552-fe23579d-d9a9-4dce-9038-2be1686772ba.png)



## 容器化應用程式

`$ touch deploy-package.json`

```json
{
    "name": "angularapp",
    "description": "Angular Web App",
    "repository": "",
    "license": "0BSD",
    "devDependencies": {
        "connect-history-api-fallback": "^2.0.0",
        "express": "^4.18.1",
        "json-server": "^0.17.0"    
    }
}
```

`$ touch Dockerfile`

```Dockerfile
FROM node:14.19.0

RUN mkdir -p /usr/src/angularapp
COPY dist /usr/src/angularapp/dist
COPY data.json /usr/src/angularapp/
COPY server.js /usr/src/angularapp/
COPY deploy-package.json /usr/src/angularapp/package.json

WORKDIR /usr/src/angularapp

RUN echo 'package-lock=false' >> .npmrc

RUN npm install

EXPOSE 4001

CMD ["node", "server.js"]
```

`$ touch .dockerignore`

```.dockerignore
node_modules
```

`$ docker build . -t angularapp -f Dockerfile`

`$ docker run -p 4001:4001 angularapp`


### 查看 docker 上的 訂單資料
`$ docker exec -it c46f23 bash`
`$ cat data.json`