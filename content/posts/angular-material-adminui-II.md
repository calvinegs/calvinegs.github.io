---
title: "使用 Angular Material 建立一個簡易的 Admin Template II"
date: 2023-02-23
description: "在這個範例中將繼續完善之前筆記所建立的 Admin Template"
tags: ["angular", "material", "admin template"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/Angular_Material_Admin_Template.git)*

在本篇筆記中將繼續使用 Angular Material 中的其他 components 來加強 Admin Template。在此文中除了會使用 SnackBar component 來實做一個 Notifier ，同時也把之前介紹過的 Loading Spnner 功能也一併加入。

除此之外也會建置一個 Login Page，身份驗證是一個應用程式中不可缺少的功能，有了這個登入畫面，將會被應用到另一篇 angular Interceptor - JWT 的應用中（待續）。

有關 Admin Template 第一個版本的介紹請見 *[Loding Spinner](https://calvinegs.github.io/posts/angular-intercoptor-loadingspinner)*

# 加強 Admin Template 功能

預計實現的功能：

![2023-02-24 18-27-45 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221155878-aab52fc2-df85-4c72-affe-7a9354531fb1.png)

![image](https://user-images.githubusercontent.com/21993717/221156012-14e07a08-0c83-470b-ba25-b553660968b1.png)

![image](https://user-images.githubusercontent.com/21993717/221141232-318e21f5-0c3f-4484-ba6c-654d373c23fa.png)

![2023-02-24 18-23-28 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221154856-54df2839-ff07-4ea1-b2b6-f6db2f61af11.png)


![2023-02-24 18-25-08 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221155236-52735b52-9b9b-4407-ba99-3e7ddb530108.png)


## 使用 Angular Material SnackBar 為 Admin Template 加入 Notifier 功能。

使用以下步驟：
- 取得 Admin Template 第一個版本程式碼
- 實現基本版本 Notifier
- 實現客制化 Notifier 功能
- 使用 material 實現一個 Login Page

## 取得 Admin Template 第一個版本程式碼

首先要為 Admin Template 第一個版本加入 Notifier 功能，可以使用 ｀git clone｀指令取得特定版程式碼。以下指令是取得 Tag為 ver-1.0 版本的程式碼： `“git clone --depth 1 --branch v1.1 https://github.com/calvinegs/Angular_Material_Admin_Template.git”`。使用這個版本的程式碼繼續在上面加入本篇筆記的相關功能。


## 實現基本版本 Notifier

將使用 angular material snackBar component 來實現 Notifier 功能。

### 匯入 snackBar module

使用 material snackBar component前要先匯入 snackBarModule 到 share-material-module.ts 中，並把它也 export 出去。

```ts {linenos=table,hl_lines=[14,30]}
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';

import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { MatSnackBarModule } from '@angular/material/snack-bar'

@NgModule({
  declarations: [
    NavigationComponent
  ],
  imports: [
    CommonModule,
    FormsModule,
    LayoutModule,
    MatToolbarModule,
    MatButtonModule,
    MatSidenavModule,
    MatIconModule,
    MatListModule,
    MatSlideToggleModule,
    MatSnackBarModule,
    RouterModule
  ],
  exports: [
    NavigationComponent
  ]
})
export class ShareMaterialModule { }
```

### 建立 NotifierService

接著建立 notifier.service.ts

```bash
$ npx ng g s services/notifier
```

NotifierService 程式碼如下：

```ts {linenos=table,hl_lines=[10,12-18]}
// notifier.service.ts
import { Injectable } from '@angular/core';
import { MatSnackBar } from '@angular/material/snack-bar';

@Injectable({
  providedIn: 'root'
})
export class NotifierService {

  constructor( private snackBar: MatSnackBar) { }

  showNotification(displayMessage: string, buttonText: string) {
    this.snackBar.open(displayMessage, buttonText, {
      horizontalPosition: 'center', // snack Bar 顯示的水平位置
      verticalPosition: 'bottom',   // snack Bar 顯示的垂直位置
      panelClass: 'error'           // 自定顏色
    })
  }
}
```

### 客制化 css

在 NotifierService 中使用了一些客制化的 css，把 css 程式碼加入到 style.scss

```css
.error.mat-mdc-snack-bar-container{
  --mdc-snackbar-container-color: red;          // 背景
  --mdc-snackbar-supporting-text-color: white;  // 文字顏色 
  --mat-mdc-snack-bar-button-color: yellow;     // 按鈕文字顏
}
```

### 使用 NotifierService 來顯示 Notifier

在 flexBox component 中加入一個功能按鈕，並在按下按鈕後呼叫 NotefierService 中的 showNotification() 方法來顯示 Notifier 在螢幕下方。

首先在 flexBox component template 中加入以下程式碼。

```html
 <!-- flexbox.component.html -->
<p>flexbox works!</p>

<button mat-raised-button color="primary" (click)='showError()'>Show Error</button>
```

在上述程式中使用了 mat-raised-button directive 所以要在 share module 中匯入 MatButtonModule，並且也要 export 出來。

```ts {linenos=table,hl_lines=[10,26,36]}
// share-material.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';

import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { MatSnackBarModule } from '@angular/material/snack-bar'

@NgModule({
  declarations: [
    NavigationComponent
  ],
  imports: [
    CommonModule,
    FormsModule,
    LayoutModule,
    MatToolbarModule,
    MatButtonModule,
    MatSidenavModule,
    MatIconModule,
    MatListModule,
    MatSlideToggleModule,
    MatSnackBarModule,
    RouterModule
  ],
  exports: [
    NavigationComponent,
    MatButtonModule,
  ]
})
export class ShareMaterialModule { }

```
flexBox component class 的程式碼如下：
- 先在 constructor 中注入 Notifier Service
- 再建立一個 showError() method，並在這個方法內呼叫 Notifier Service 中的 showNotification() 方法。

```ts {linenos=table,hl_lines=[10,12-14]}
//  flexbox.component.ts
import { Component } from '@angular/core';
import { NotifierService } from 'src/app/services/notifier.service';

@Component({
  selector: 'app-flexbox',
  templateUrl: './flexbox.component.html',
  styleUrls: ['./flexbox.component.scss']
})
export class FlexboxComponent {
  constructor(private notifierService: NotifierService) {}

  showError() {
    this.notifierService.showNotification('資料讀取失敗！','關閉');
  }
}
```

### 執行程式後結果

![image](https://user-images.githubusercontent.com/21993717/220944867-fe73d421-879f-40e2-bd40-37b1557f36b0.png)


### 優化 NotifierService

優化的功能包含了：
- 支援不同類型的 Notifier (Success/Error)
- Notifier 預設五秒鐘會自動消失

修改 NotifierService 中 showNotification() method:

```ts {linenos=table,hl_lines=[2,4,7]}
// notifier.service.ts
  showNotification(displayMessage: string, buttonText: string, messageType: 'error' | 'success') {
    this.snackBar.open(displayMessage, buttonText, {
      duration: 5000,
      horizontalPosition: 'center',
      verticalPosition: 'bottom',
      panelClass: messageType
    })
  }
```

在 styles.scss 中再加入額外的 succs style 設定

```css
.success.mat-mdc-snack-bar-container{
  --mdc-snackbar-container-color: gray;
  --mdc-snackbar-supporting-text-color: yellow; 
  --mat-mdc-snack-bar-button-color: white;
}
```

修改 flexbox.component.html：

```html {linenos=tables,hl_lines=[3-6]}
<p>flexbox works!</p>

<div class="button-container">
    <button class="btn" mat-raised-button color="primary" (click)="showSnackBar('success')">success</button>
    <button class="btn" mat-raised-button color="accent" (click)="showSnackBar('error')">error</button>
</div>
```

修改 flexbox.component.scss:

```css
.container{
    display: flex;
}

.btn{
    margin: 5px;
}
```

修改 flexbox.component.ts

```ts {linenos=tables,hl_lines=[12-16]}
import { Component } from '@angular/core';
import { NotifierService } from 'src/app/services/notifier.service';

@Component({
  selector: 'app-flexbox',
  templateUrl: './flexbox.component.html',
  styleUrls: ['./flexbox.component.scss']
})
export class FlexboxComponent {
  constructor(private notifierService: NotifierService) {}

  showSnackBar(typeNotifier:'error'|'success') {
    if (typeNotifier === 'success')
      this.notifierService.showNotification('資料已順利讀取完成！','OK', typeNotifier);
    else
      this.notifierService.showNotification('資料讀取失敗！','關閉', typeNotifier);
  }
}
```

### 優化後的結果

優化後同一個 method showSnackBar() 已可以支援不同類型的 notifier ，並顯示出不同的 style:

![2023-02-24 10-49-45 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221080079-651f9cad-e1d5-45c9-bc83-31bca9a5824f.png)

![2023-02-24 10-51-42 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221080266-fc91c67f-9aea-4127-bd80-ffb6521cbc09.png)


## 實現客制化 Notifier 功能

如果你的 notifier 需要更豐富的 UI，MatSnackBar 也支援可自定使用者介紹的方式來顯示，下面來看看如何做到客制化 UI。

### 新建立一個 component

使用 ng cli 在 components 目錄下建立一個新元件

```bash
$ npx ng g c components/notifier
```

### NotifierServer 中新的 Method

在新元件中將會使用到 NotifierServer 中新的 method，如下：

```ts
// notifier.service.ts
// ...
  showCustomNotification(displayMessage: string, buttonText: string, messageType: 'error' | 'success') {
    this.snackBar.openFromComponent(NotifierComponent, {
      data: {
        message: displayMessage,
        buttonText: buttonText,
        messageType: messageType
      },
      duration: 5000,
      horizontalPosition: 'center',
      verticalPosition: 'bottom',
      panelClass: [messageType]
    })
  };
```


### 新 component 的程式碼

Notifier Component 程式內容：

```ts {linenos=tables}
// notifier.component.ts
import { Component, Inject } from '@angular/core';
import { MatSnackBarRef, MAT_SNACK_BAR_DATA } from '@angular/material/snack-bar';

@Component({
  selector: 'app-notifier',
  templateUrl: './notifier.component.html',
  styleUrls: ['./notifier.component.scss']
})
export class NotifierComponent {
  constructor(@Inject(MAT_SNACK_BAR_DATA) public data: any,
    public snackBarRef: MatSnackBarRef<NotifierComponent>) {

}
```

Notifier Component template 內容：

```html {linenos=tables}
<!-- notifier.component.heml -->
<div>
    {{data.messageType}}
</div>
<div class="content-style">
    {{data.message}}
</div>
<div class="button-style">
    <button mat-flat-button [ngClass]=data.messageType (click)="snackBarRef.dismiss()">
        {{data.buttonText}}
    </button>
</div>
```

Notifier Component style 內容：

```css {linenos=tables}
/* notifier.component.scss */
.success {
    background-color: gray;
}

.error {
    background-color: aqua;
}

.content-style{
    border: 1px solid white;
    border-radius: 4px;
    padding: 10px;
    margin-top: 10px;
    margin-bottom: 10px;
}

.button-style{
    text-align: center;
}
```

flexBox Component 程式內容：

```ts {linenos=tables}
// flexbox.component.ts
  showCustomSnackBar(typeNotifier: 'error'|'success') {
    if (typeNotifier === 'success')
      this.notifierService.showCustomNotification('資料已順利讀取完成！','OK', typeNotifier);
    else
      this.notifierService.showCustomNotification('資料讀取失敗！','關閉', typeNotifier);
  }
```

flexBox Component template 內程式內容：

```html {linenos=tables}
<!-- flexbox.component.html -->
<div class="button-container">
    <button class="btn" mat-raised-button color="primary" (click)="getData('success')">success</button>
    <button class="btn" mat-raised-button color="accent" (click)="getData('error')">error</button>
    <button class="btn" mat-raised-button color="primary" (click)="showCustomSnackBar('success')">Custom SnackBar</button>
    <button class="btn" mat-raised-button color="accent" (click)="showCustomSnackBar('error')">Custom SnackBar</button>
</div>
```

### 測試 客制化 Notifier 功能

![2023-02-24 12-35-20 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221092664-5658b0ea-df8b-4b9b-a756-3ebecf3bf1d6.png)


![image](https://user-images.githubusercontent.com/21993717/221092747-b5c58057-d49e-42fb-9a6e-7c83265415ab.png)


以上是如何使用 snackBar 來實現 Notifier 功能。接下來繼續來完善我們的 Admin Template


## 為 Admin Template 加入 Loading spinner

### 建立一個 Loading Service 

使用 Cli 建立一個 service

```bash
$ npx ng g s services/loading
```

service 程式碼如下：

```ts {linenos=tables,hl_lines=[]}
// loading.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, delay, Observable, of, switchMap } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class LoadingService {
  private _loading$ = new BehaviorSubject<boolean>(false);
  loading$: Observable<boolean> = this._loading$.pipe(
    switchMap(isLoading => {
      if (!isLoading) {
        return of(false);
      }
      return of(true).pipe(delay(1000));
    })
  )

  constructor() { }

  show() {
    this._loading$.next(true);
  }

  hide() {
    this._loading$.next(false);
  }
}
```

### 建立一個 interceptor

透過 Angular Cli 來建立一個 Service，這個 service 的功能是在發出每 http request 時會先顯示 loading spinner，並在 request 完成後關閉 loading spinner。

透過Angular cli 建立 interceptor

```bash
$ npx ng g interceptor interceptors/loadingSpin
```

interceptor 中的程式碼：

```ts {linenos=tables,hl_lines=[]}
// loading－spin.interceptor.ts
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
export class LoadingSpinInterceptor implements HttpInterceptor {

  constructor(private loadingServie : LoadingService) {}

  intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    this.loadingServie.show();

    return next.handle(request).pipe(
      finalize(()=> {
        this.loadingServie.hide();
      })
    );
  }
}
```

完成的 interceptor 必須在 app.module.ts 中先註冊

```ts {linenos=tables,hl_lines=[]}
// app.module.ts
// ...
  providers: [{
    provide: HTTP_INTERCEPTORS,
    useClass: LoadingSpinInterceptor,
    multi: true
  }],
  bootstrap: [AppComponent]
```

### 在 Admin Template 加入 Loading spinner

把 Material Spinner ui component 加入到 NavigationComponent 中，以下程式碼放在程式最前頭：

```html {linenos=tables,hl_lines=[]}
<!-- navigation.component.html -->
<ng-container *ngIf="loading$ | async">
  <div class="overlay"></div>
  <mat-spinner></mat-spinner>
</ng-container>
```
在 navigation.component.scss 加入以下 css 用來顯示 spinner，並鎖定畫面。

```css
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

並在 component class 中加入 loading$ 變數

```ts {linenos=table,hl_lines=[14,23]}
import { Component } from '@angular/core';
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';
import { Observable } from 'rxjs';
import { map, shareReplay } from 'rxjs/operators';
import { LoadingService } from '../services/loading.service';

@Component({
  selector: 'app-navigation',
  templateUrl: './navigation.component.html',
  styleUrls: ['./navigation.component.scss']
})
export class NavigationComponent {
  isDarkTheme = false;
  loading$ = this._loadingService.loading$;

  isHandset$: Observable<boolean> = this.breakpointObserver.observe(Breakpoints.Handset)
    .pipe(
      map(result => result.matches),
      shareReplay()
    );

  constructor(private breakpointObserver: BreakpointObserver，
    private _loadingService: LoadingService) {}

  ngOnInit() {
    this.isDarkTheme = localStorage.getItem('theme') === "Dark" ? true : false;
  }
  
  storeThemeSelection() {
    localStorage.setItem('theme', this.isDarkTheme ? "Dark" : "Light");
  }
}
```

### 在 ShareMaterialModule 中要匯入/出 Progress Spinner Module

```ts {linenos=table,hl_lines=[15,38]}
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';

import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { MatSnackBarModule } from '@angular/material/snack-bar'
import { MatProgressSpinnerModule } from '@angular/material/progress-spinner'

@NgModule({
  declarations: [
    NavigationComponent
  ],
  imports: [
    CommonModule,
    FormsModule,
    LayoutModule,
    MatToolbarModule,
    MatButtonModule,
    MatSidenavModule,
    MatIconModule,
    MatListModule,
    MatSlideToggleModule,
    MatSnackBarModule,
    MatProgressSpinnerModule,
    RouterModule
  ],
  exports: [
    NavigationComponent,
    MatButtonModule,
    MatProgressSpinnerModule
  ]
})
export class ShareMaterialModule { }
```

### 建立一個 發送 http request 來取得遠端資料的 Service 

使用  cli 建立一個具有發送 http request 的 service
```bash {linenos=tables,hl_lines=[]}
```

程式碼：
```ts {linenos=tables,hl_lines=[]}
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

### 建立一個測試 Spinner 功能的元件

使 cli 來建立一個可測試 Spinner 的元件：
```bash 
$ npx ng g c components/getData
```

```html
<!-- getData.Component.html -->
<p>get-data works!</p>
<button mat-raised-button color="primary" (click)="getData()">取得資料</button>
```

```ts
// getData.Component.html
import { Component } from '@angular/core';
import { GetDataService } from 'src/app/services/get-data.service';

@Component({
  selector: 'app-get-data',
  templateUrl: './get-data.component.html',
  styleUrls: ['./get-data.component.scss']
})
export class GetDataComponent {
  data: unknown;

  constructor(private _getData: GetDataService) {}

  getData() {
    this._getData.getData().subscribe( data => {
      this.data = data;
    })
  }
}
```

為 GetDataComponent 建立對應的 route
```ts
// app.module.ts
  const routes: Routes = [
    { path: 'flexbox', component: FlexboxComponent },
    { path: 'getdata', component: GetDataComponent }
  ];
```
  ### 測試 Spinner 功能

為測試 Spinner 功能，我們要讓網路連線速度下降。可以透過使用 chrome 開發者工具中的 network 功能中的：Disable cash & throttling 來進行模擬。

執行程式開啟瀏覽器後先按 F12 來開啟開發者工具，切換到 Network 功能區，先勾選“Disable cache"選項

![image](https://user-images.githubusercontent.com/21993717/221138116-bbab4cf8-f999-4ba1-bb37-a81afffe6bbb.png)

接著下拉 Throttling 選項，點選最下面的 "Add"

![image](https://user-images.githubusercontent.com/21993717/221138861-5d591a33-082d-49e8-b738-dbdd5e45f1a6.png)

再點選 “Add custom profile"

![image](https://user-images.githubusercontent.com/21993717/221139517-a24c8620-ae0c-4b7c-8b01-65c89f8f6bd9.png)

建立一個 20Mbps 的連線設定檔

![image](https://user-images.githubusercontent.com/21993717/221140613-4c27976a-0965-454d-80c9-2882b8232093.png)

測試結果：

![image](https://user-images.githubusercontent.com/21993717/221141232-318e21f5-0c3f-4484-ba6c-654d373c23fa.png)

最後我們還可以在 LoadingSpinInterceptor 中加入 Notifier 來顯示資料已讀取完成的訊息。

```ts {linenos=tables,hl_lines=[7]}
  intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    this.loadingServie.show();

    return next.handle(request).pipe(
      finalize(()=> {
        this.loadingServie.hide();
        this.notifier.showCustomNotification('資料讀成完成！', 'ok', 'success')
      })
    );
  }
```

## 使用 material 實現一個 Login page

現在我們 Admin Template 所具備的功能已越來越完整，在本篇筆記最後，要再使用 Material 相關 UI 來建立一個系統登入的功能畫面。

使用 angular cli 來新增個 login component 的框架

```bash
$ npx ng g c components/login
```

### 在 ShareMaterialModule 匯入相關的 Material Module

先在 ShareMaterialModule 匯入相關的 Material Module: MatIconModule、MatCardModule、MatInputModule等

```ts {linenos=tables,hl_lines=[12,17,18,"41-43"]}
// share-material.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';

import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { MatSnackBarModule } from '@angular/material/snack-bar'
import { MatProgressSpinnerModule } from '@angular/material/progress-spinner';
import { MatCardModule } from '@angular/material/card';
import { MatInputModule } from '@angular/material/input';
@NgModule({
  declarations: [
    NavigationComponent
  ],
  imports: [
    CommonModule,
    FormsModule,
    LayoutModule,
    MatToolbarModule,
    MatButtonModule,
    MatSidenavModule,
    MatIconModule,
    MatListModule,
    MatSlideToggleModule,
    MatSnackBarModule,
    MatProgressSpinnerModule,
    RouterModule
  ],
  exports: [
    NavigationComponent,
    MatButtonModule,
    MatProgressSpinnerModule,
    MatIconModule,
    MatCardModule,
    MatInputModule
  ]
})
export class ShareMaterialModule { }
```

### Component Class 中的程式碼

LoginComponent Class 程式碼中使用到 Reactive Forms，記得要在 app.module.ts 中匯入 ReactiveFormsModule。

```ts {linenos=tables,hl_lines=[]}
// login.component.ts
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent {
  hide: boolean = false;

  constructor(private fb: FormBuilder) {
  }

  ngOnInit() {
  }

  loginForm: FormGroup = this.fb.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(6)]]
  })


  onLogin() {
    if (!this.loginForm.valid) {
      return;
    }
    console.log(this.loginForm.value);
  }
}
```

### login Template

```html {linenos=tables,hl_lines=[]}
<!-- login.components.html -->
<mat-card>
  <mat-card-content>
    <div class="header">
      <P>Sign Into Your Account </P>
    </div>
    <form (ngSubmit)="onLogin()" name="loginForm" [formGroup]="loginForm">
      <div class="emailInput">
        <mat-form-field class="full-width" appearance="outline">
          <mat-label>Email</mat-label>
          <input
            formControlName="email"
            matInput
            placeholder="Enter email address" required
          />
          <mat-error *ngIf="!loginForm.controls['email'].valid">
              Email is required
            </mat-error>
        </mat-form-field>
      </div>

      <div>
        <span>
          <a class="text-link" class="aLink" routerLink="/auth/forgot-password">Forgot Password?</a>
        </span>
        <mat-form-field class="full-width" appearance="outline">
          <mat-label>Password</mat-label>
          <input formControlName="password" matInput [type]=" hide ? 'password' : 'text'" required />
          <button  mat-icon-button matSuffix (click)="hide = !hide" [attr.aria-label]="'Hide Password'"
          [attr.aria-pressed]="hide">
          <mat-icon>
              {{hide ? 'visibility_off' : 'visibility'}}
          </mat-icon>
      </button>
      <mat-error *ngIf="!loginForm.controls['password'].valid">
          Password is required
        </mat-error>
      </mat-form-field>
      </div>
      <button mat-flat-button color="primary">Login</button>
    </form>

    <div class="button-row">
      <p>Create New Account</p>
    </div>
  </mat-card-content>
</mat-card>
```

### 使用 CSS 來美化 Login Form 外觀

```css {linenos=tables,hl_lines=[]}
/* login.components.scss */
mat-card {
    max-width: 600px;
    margin: 2em auto;
    text-align: center;
    max-height: 600px;
}

.header {
    text-align: center;

}

.full-width {
    width: 80%;
}

.button-row {
    padding-top: 5px;
}

.button-row a {
    margin-right: 8px;
    text-align: center;
}

.forget-password {
    padding-left: 0px;
}

.emailInput {
    padding-top: 10px;
}

.contentBody {
    padding: 60px 1rem;
    background: #1b6ca8;
    display: block;
}

.aLink {
    float: right;
    padding-right: 60px;
    text-decoration: none;
}
```

### 在 AppModule 中匯入使用到的 Module

```ts {linenos=tables,hl_lines=[3,12]}
// app.module.ts
// ...
import { ReactiveFormsModule } from '@angular/forms';
// ...

imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule,
    ShareMaterialModule,
    HttpClientModule,
    ReactiveFormsModule
  ],
  // ...
```

### 設定 login form route

在 AppRoutingModule 中加入 Route 的設定

```ts {linenos=tables,hl_lines=[11]}
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { FlexboxComponent } from './components/flexbox/flexbox.component';
import { GetDataComponent } from './components/get-data/get-data.component';
import { LoginComponent } from './components/login/login.component';

const routes: Routes = [
  { path: 'flexbox', component: FlexboxComponent },
  { path: 'getdata', component: GetDataComponent },
  { path: 'login', component: LoginComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

```

### 測試

直接在瀏覽器 URL 輸入： http://localhost:4200/login，會見到如下圖

![image](https://user-images.githubusercontent.com/21993717/221147564-5eb3866c-195c-4389-b9d2-8f24d155e19e.png)

雖然 Login Form 已可以順利的呈現，但可以看出來有一個問題：它嵌套在 Navigator 中，感覺是不是有點怪。接下來我們就來調整一下，讓 Login Form “獨立”出來，不要嵌套在 Navigator 中。

首先先調整一下 Route 的設定:

```ts{linenos=tables,hl_lines=["10-25"]}
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { FlexboxComponent } from './components/flexbox/flexbox.component';
import { GetDataComponent } from './components/get-data/get-data.component';
import { LoginComponent } from './components/login/login.component';
import { NavigationComponent } from './navigation/navigation.component';


const routes: Routes = [
  {
    path: '', 
    component: NavigationComponent,
    children: [
      { path: '', redirectTo: 'flexbox', pathMatch: 'full'},
      { path: 'flexbox', component: FlexboxComponent },
      { path: 'getdata', component: GetDataComponent },
    ]
  },
  {
      path: 'login',
      component: LoginComponent,
      // canActivate: [NonAuthGuard]
  },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

再來修改 app.component.html

```html
<router-outlet></router-outlet>
```

結果:

![2023-02-24 18-23-28 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221154856-54df2839-ff07-4ea1-b2b6-f6db2f61af11.png)

![2023-02-24 18-25-08 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/221155236-52735b52-9b9b-4407-ba99-3e7ddb530108.png)


完工!!
