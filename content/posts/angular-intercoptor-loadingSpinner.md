---
title: "Angular Intercoptor 使用案例一：Loading Spinner"
date: 2023-01-31
draft: false
description: "使用 Interceptor 來完成當程式讀取後端資料時於畫面上顯示 “讀取中...“ 動畫效果的等待畫面，讓使用者有良好的使用體驗"
tags: ["angular", "webapi", "interceptor", "httpclient", "css", "rxjs"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular-interceptor-loadingSpinner.git)*

除非你和我一樣是個‘老傢伙’，使用過 Angular ver 4.3 以前的版本，否則你一定會遇到它 - interceptor(攔截器)。接下來將用幾篇筆記把各種使用 interceptor 情境的範例給記錄下來。

Angular Interceptor 可以用來作什麼用途呢
 - 修改 HTTP Headers
 - 修改 HTTP request body
 - 設定 authentication/authorization token　（身份認證）
 - 模擬 backend api
 - 修改 HTTP response
 - 處理 HTTP 錯誤  (HTTP Error Handlin)
 - 顯示 Loading Spinner
 - 格式化 JSON Responses
 - 日誌記錄 


首先先來看看如何使用 Interceptor 來完成一個當你在讀取後端資料時可在畫面上顯示一個“正在讀取中...“的動畫效果，這可以讓使用者有良好的使用體驗。

筆記最後要達的效果是，在按下“取得資料”按鈕時，由程式向後端 API 讀取資料，而在讀取完成前，畫面中的所有 UI (如：按鈕）都是呈現無作用的狀況，直到資料回傳完畢。同時如果“向後端 API 讀取資料”的作業時間小於一秒鐘，則不顯示 “讀取中..." 這個動畫效果。

![image](https://user-images.githubusercontent.com/21993717/215711964-9771e53a-3dc6-4cc5-9d23-4ae3e225152b.png)


## 建立 Angular 專案架構

使用 npm init @angular 語法來建立 Angular project

```bash {linenos=table,hl_lines=[1],linenostart=1}
$ npm init @angular loadingSpin -- --routing --style=scss
$ cd loadingSpin
$ code .
```

安裝 material component 

```bash {linenos=table,hl_lines=[1]}
$ npx ng add @angular/material

ℹ Using package manager: npm
✔ Found compatible package version: @angular/material@15.1.2.
✔ Package information loaded.

The package @angular/material@15.1.2 will be installed and executed.
Would you like to proceed? Yes
✔ Packages successfully installed.
? Choose a prebuilt theme name, or "custom" for a custom theme: Indigo/Pink        [ Preview: 
https://material.angular.io?theme=indigo-pink ]
? Set up global Angular Material typography styles? No
? Include the Angular animations module? Include and enable animations
UPDATE package.json (1108 bytes)
✔ Packages installed successfully.
UPDATE src/app/app.module.ts (502 bytes)
UPDATE angular.json (3077 bytes)
UPDATE src/index.html (556 bytes)
UPDATE src/styles.scss (181 bytes)
```

## 使用 @angular/material component 來建立 UI

在使用 material component 之前，先在 app.module 中 import 要使用的 UI component，如: Toolbar、Button、Progress Spinner等。

```ts {hl_lines=["9-11","20-22"]}
# app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatProgressSpinnerModule } from '@angular/material/progress-spinner';
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule,
    MatToolbarModule,
    MatButtonModule,
    MatProgressSpinnerModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

在 app.component.html 中，建立一個表頭、二個按鈕、及放入 material spinner（用來顯示正在處理中的動畫圖示） 組件。

```html
# app.component.html
<mat-toolbar color="primary">
  使用 RxJS and HttpInterceptor 來實現 "資料讀取中..." 動畫效果的功能
</mat-toolbar>
<div class="content">
  <button mat-raised-button (click)="showSpinner()">顯示</button>
  <button mat-raised-button color="warn" (click)="hideSpinner()">隐蔵</button>
</div>  

<mat-spinner *ngIf="loading"></mat-spinner>
```

在 app.component.scss 中設定了畫面上按鈕排版位置。

```css
# app.component.scss
.content {
    padding: 16px;
}

button {
    margin: 8px;
}
```

在 app.component.ts component class 中宣告了一個 property: loading，用來控制畫面上 Spinner 的顯示與否。

```ts
# app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  loading = false;

  showSpinner() {
    this.loading = true;
  }

  hideSpinner() {
    this.loading = false;
  }

}
```

## 測試 mat-spinner 顯示效果

![2023-01-30 22-00-51 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/215626226-f9966a4b-7acb-4369-b6be-1e58664a3e0d.png)

測試後，微調了一下 spinner 在畫面中的位置。

app.component.scss 調整如下：

```css
# ...
# 加入以下設定：調整顯示的位置
mat-spinner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 5;
}
```

調整後效果如下：

![2023-01-30 22-02-31 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/215627819-ba916314-a2e6-41b0-9d46-80b85e150a55.png)


## 使用 HTTPClient 讀取後端資料

建立一個呼叫後端 Api 的 service

```bash
$ npx ng g s services/getData
```

```ts {linenos=table,hl_lines=[2,10,13]}
# services/get-data.service.ts
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class GetDataService {

  constructor(private http: HttpClient) { }

  getData() {
    return this.http.get('https://jsonplaceholder.typicode.com/posts');
  }
}
```

使用到了 HttpClient，所以要記得在 AppModule 中匯入 HttpClientModule

```ts {linenos=table,hl_lines=[13,25]}
# app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatProgressSpinnerModule } from '@angular/material/progress-spinner';

import { HttpClientModule } from '@angular/common/http';
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule,
    MatToolbarModule,
    MatButtonModule,
    MatProgressSpinnerModule,
    HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

在 Template (app.component.html) 中加入一個“取得資料”的按鈕。

```html {linenos=table,hl_lines=[7]}
<mat-toolbar color="primary">
  使用 RxJS and HttpInterceptor 來實現 "資料讀取中..." 動畫效果的功能
</mat-toolbar>
<div class="content">
  <button mat-raised-button (click)="showSpinner()">顯示</button>
  <button mat-raised-button color="warn" (click)="hideSpinner()">隐蔵</button>
  <button mat-raised-button color="primary" (click)="getData()">取得資料</button>
</div>  

<mat-spinner *ngIf="loading"></mat-spinner>
```

在 component class 中呼叫 Service 來向後端 API 讀取資料。

```ts {linenos=table,hl_lines=[11,13,"22-28"]}
import { Component } from '@angular/core';
import { GetDataService } from './services/get-data.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  loading = false;
  data: unknown;

  constructor(private _getData: GetDataService){ }
  showSpinner() {
    this.loading = true;
  }

  hideSpinner() {
    this.loading = false;
  }

  getData() {
    this.loading = true;
    this._getData.getData().subscribe(data => {
      this.loading = false;
      this.data = data;
    });
  }
}
```

完成以上程式後，在按下“取得資料“按鈕執行讀取後端 API 時畫面已可以正確的顯示資料讀取中的動畫圖示了。

![image](https://user-images.githubusercontent.com/21993717/215631441-858391b6-43ef-4df7-b569-4023f27c1d5f.png)


## 使用 Interceptor 來優化程式

接下來要透過 Angular Interceptor 來優化程式。上述程式碼在每支與後端API有互動的程式中都會重複出現，可以透過將相同邏輯統一放置在 Interceptor 中來優化整個系統。

建立一個 Interceptor

```bash {linenos=table,hl_lines=[1]}
$ npx ng g interceptor interceptors/httpLoadingSpin

CREATE src/app/interceptors/http-loading-spin.interceptor.spec.ts (472 bytes)
CREATE src/app/interceptors/http-loading-spin.interceptor.ts (420 bytes)
```
將 '狀態' 資訊放在另一支 Service 程式中

```bash {linenos=table,hl_lines=[1]}
$ npx ng g s services/loading

CREATE src/app/services/loading.service.spec.ts (362 bytes)
CREATE src/app/services/loading.service.ts (136 bytes)
```

loading.service.ts 程式內容如下：

```ts
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class LoadingService {
  private _loading$ = new BehaviorSubject<boolean>(false);
  loading$ = this._loading$.asObservable();

  constructor() { }

  show() {
    this._loading$.next(true);
  }

  hide() {
    this._loading$.next(false);
  }
}
```

在前面新建立的 Interceptor 程式修改如下：

```ts {linenos=table,hl_lines=[9,14,17,21]}
import { Injectable } from '@angular/core';
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor
} from '@angular/common/http';
import { finalize, Observable } from 'rxjs';
import { LoadingService } from '../services/loading.service';

@Injectable()
export class HttpLoadingSpinInterceptor implements HttpInterceptor {

  constructor(private loadingService: LoadingService) {}

  intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    this.loadingService.show();

    return next.handle(request).pipe(
      finalize(() => {
        this.loadingService.hide();
      })
    );
  }
}
```

並且記得要在 app.moudle.ts 註冊這個新建立的 interceptor
```ts
...
providers: [{
    provide: HTTP_INTERCEPTORS,
    useClass: HttpLoadingSpinInterceptor,
    multi: true
  }],
...
```

app.component.ts 這個 component class 程式改成使用新建立的 LoadingService

```ts {linenos=table,hl_lines=[3,12,16,20,25]}
import { Component } from '@angular/core';
import { GetDataService } from './services/get-data.service';
import { LoadingService } from './services/loading.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  // loading = false;
  loading$ = this.loadingService.loading$;
  data: unknown;

  constructor(private _getData: GetDataService
    , private loadingService: LoadingService){ }
  
  showSpinner() {
    // this.loading = true;
    this.loadingService.show();
  }

  hideSpinner() {
    // this.loading = false;
    this.loadingService.hide();
  }

  getData() {
    // this.loading = true;
    this._getData.getData().subscribe(data => {
      // this.loading = false;
      this.data = data;
    });
  }
}
```

template（app.component.html）中的 ngIf 判斷條件改使用 loading$，並搭配使用 async pipe

```html
<mat-toolbar color="primary">
  使用 RxJS and HttpInterceptor 來實現 "資料讀取中..." 動畫效果的功能
</mat-toolbar>
<div class="content">
  <button mat-raised-button (click)="showSpinner()">顯示</button>
  <button mat-raised-button color="warn" (click)="hideSpinner()">隐蔵</button>
  <button mat-raised-button color="primary" (click)="getData()">取得資料</button>
</div>  

<mat-spinner *ngIf="loading$ | async"></mat-spinner>
```

以上是透過 interceptor 方式來優化程式。


## 透過使用 RXJS Operator 再對程式進行優化

最後，將再進行更新一步的優化：
- 在按下“取得資料”按鈕後與取得資料前（即資料回傳完畢前）畫面上的所有按鈕（UI）都保持 disable 狀況，直到資料回傳完畢
- 如果“取得資料”的作業時間很短（如：小於等於1秒鐘），將不顯示“讀取中..."這個動畫效果

### 需求一
上述需求一可透過 CSS 來達成，首先將 app.component.html mat-spinner 程式碼調整如下

```html
<!-- <mat-spinner *ngIf="loading$ | async"></mat-spinner> -->
<ng-container *ngIf="loading$ | async">
    <div class="overlay"></div>
    <mat-spinner></mat-spinner>
</ng-container>
```

其中的 overlay div 是用來遮敝在整個晝面上，來達到讀取完成前畫面是無回應的要求。

```css
.content {
    padding: 16px;
}

button {
    margin: 8px;
}

mat-spinner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 5;
}

.overlay {
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    position: absolute;
    z-index: 2;
    backdrop-filter: blur(2px);
}
```

使用後的效果如下圖：

![image](https://user-images.githubusercontent.com/21993717/215712330-654d853d-097f-45de-8a43-1ef8d0da26f0.png)
### 需求二

第二個需求可以在 LoadingService 使用相關的 RXJS Operator 來達成：
```ts
...

  // loading$ = this._loading$.asObservable();
  loading$: Observable<boolean> = this._loading$.pipe(
    switchMap(isLoading => {
      if (!isLoading) {
        return of(false);
      }
      return of(true).pipe(delay(1000));
    })
  )
  
  ...
```