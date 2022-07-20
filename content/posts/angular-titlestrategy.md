---
title: "使用 Angular 14 中內建的 TitleStrategy 功能來自定瀏覽器的頁面標題"
date: 2022-07-20
description: "使用 Angular 建構應用程式時，為了增加使用良好體驗，常會做的一件事情是在每次成功導航後去更新瀏覽器的頁面標題。以往這個動作必須手工自行去撰寫程式，現在在 Angular 14 中已內建這樣功能 - TitleStrategy。在本篇筆記中將紀錄如何使用這個功能並自定客制化瀏覽器的頁面標題"
tags: ["angular", "browser page title", "title strategy", "bootstrap"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular-titlestrategy.git)*

## 需求情境

希望我們的應用程式在每次成功導航後都能自動新瀏覽器的頁面標題。

當使用者按下“功能列的`HOME`後，程式除了導覽至 Home Page外，瀏覽器的頁面標題顯示的是: "CRUD App - Home"

![image](https://user-images.githubusercontent.com/21993717/179889687-37c5fc81-e51b-4dbe-94bf-2f1a1624edd4.png)

當使用者按下“功能列的`登入`按鈕後，程式除了導覽至 登入畫面　外，瀏覽器的頁面標題顯示的是: "CRUD App - Login"

![image](https://user-images.githubusercontent.com/21993717/179890328-a266816e-408f-49bf-9399-d870a496d083.png)


## 檢視 Angular/CLI

```bash
$ ng version
     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/
    

Angular CLI: 14.0.5
Node: 16.14.0
Package Manager: npm 8.3.1 
OS: linux x64

Angular: undefined
... 

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.1400.5
@angular-devkit/core         14.0.5
@angular-devkit/schematics   14.0.5
@angular/cli                 14.0.5
@schematics/angular          14.0.5
```

## 建立新專案
```bash
$ npx ng new angular-titlestrategy
? Would you like to add Angular routing? Yes
? Which stylesheet format would you like to use? SCSS   [ 
https://sass-lang.com/documentation/syntax#scss                ]
CREATE angular-titlestrategy/README.md (1074 bytes)
CREATE angular-titlestrategy/.editorconfig (274 bytes)
CREATE angular-titlestrategy/.gitignore (548 bytes)
CREATE angular-titlestrategy/angular.json (3171 bytes)
CREATE angular-titlestrategy/package.json (1052 bytes)
CREATE angular-titlestrategy/tsconfig.json (863 bytes)
CREATE angular-titlestrategy/.browserslistrc (600 bytes)
CREATE angular-titlestrategy/karma.conf.js (1438 bytes)
CREATE angular-titlestrategy/tsconfig.app.json (287 bytes)
CREATE angular-titlestrategy/tsconfig.spec.json (333 bytes)
CREATE angular-titlestrategy/.vscode/extensions.json (130 bytes)
CREATE angular-titlestrategy/.vscode/launch.json (474 bytes)
CREATE angular-titlestrategy/.vscode/tasks.json (938 bytes)
CREATE angular-titlestrategy/src/favicon.ico (948 bytes)
CREATE angular-titlestrategy/src/index.html (306 bytes)
CREATE angular-titlestrategy/src/main.ts (372 bytes)
CREATE angular-titlestrategy/src/polyfills.ts (2338 bytes)
CREATE angular-titlestrategy/src/styles.scss (80 bytes)
CREATE angular-titlestrategy/src/test.ts (749 bytes)
CREATE angular-titlestrategy/src/assets/.gitkeep (0 bytes)
CREATE angular-titlestrategy/src/environments/environment.prod.ts (51 bytes)
CREATE angular-titlestrategy/src/environments/environment.ts (658 bytes)
CREATE angular-titlestrategy/src/app/app-routing.module.ts (245 bytes)
CREATE angular-titlestrategy/src/app/app.module.ts (393 bytes)
CREATE angular-titlestrategy/src/app/app.component.scss (0 bytes)
CREATE angular-titlestrategy/src/app/app.component.html (23364 bytes)
CREATE angular-titlestrategy/src/app/app.component.spec.ts (1118 bytes)
CREATE angular-titlestrategy/src/app/app.component.ts (226 bytes)
✔ Packages installed successfully.
    Successfully initialized git.

$ cd angular-titlestrategy

$ cat package.json 
{
  "name": "angular-titlestrategy",
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
    "@angular/animations": "^14.0.0",
    "@angular/common": "^14.0.0",
    "@angular/compiler": "^14.0.0",
    "@angular/core": "^14.0.0",
    "@angular/forms": "^14.0.0",
    "@angular/platform-browser": "^14.0.0",
    "@angular/platform-browser-dynamic": "^14.0.0",
    "@angular/router": "^14.0.0",
    "rxjs": "~7.5.0",
    "tslib": "^2.3.0",
    "zone.js": "~0.11.4"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "^14.0.5",
    "@angular/cli": "~14.0.5",
    "@angular/compiler-cli": "^14.0.0",
    "@types/jasmine": "~4.0.0",
    "jasmine-core": "~4.1.0",
    "karma": "~6.3.0",
    "karma-chrome-launcher": "~3.1.0",
    "karma-coverage": "~2.2.0",
    "karma-jasmine": "~5.0.0",
    "karma-jasmine-html-reporter": "~1.7.0",
    "typescript": "~4.7.2"
  }
}
```

## 設置專案

### 建立相關元件 與 服務
```bash
$ ng g c pages/login
$ ng g c pages/register
$ ng g c pages/profile
$ ng g c pages/home
$ ng g c pages/userdata
$ ng g c pages/order

```

### 使用 Bootstrap

開啟 src/index.html 在 <head> 加入 link tag
- 使用 bootstrap 5 如第 9 行匯入語法

```html {linenos=table,hl_lines=[9]}
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>AngularTitlestrategy</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

## 路由設置
透過路由的設置，程式才會知道當使用者在瀏覧器特定的 URI 時，程式應該去執那支相對應的程式。路由模組檔案（app-routin.module.ts）內容如下：

```ts {linenos=table,hl_lines=[10-18]}
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { OrderComponent } from './pages/order/order.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';
import { UserdataComponent } from './pages/userdata/userdata.component';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  { path: 'profile', component: ProfileComponent },
  { path: 'userdata', component: UserdataComponent},
  { path: 'order', component: OrderComponent },
  { path: '', redirectTo: 'home', pathMatch: 'full'}
];


@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

```

## App Component 修改

App 元件的程式碼
```ts
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  
}
```

App 元件的 HTML
```html
<!-- app.component.html -->
<div id="app">
  <nav class="navbar navbar-expand navbar-dark bg-dark">
    <div class="container-fluid">
      <a href="#" class="navbar-brand">Angular14</a>
      <ul class="navbar-nav me-auto" routerLinkActive="active">
        <li class="nav-item">
          <a href="/home" class="nav-link" routerLink="home">首頁</a>
        </li>
        <li class="nav-item">
          <a href="/userdata" class="nav-link" routerLink="userdata">使用者管理</a>
        </li>
        <li class="nav-item">
          <a href="/order" class="nav-link" routerLink="order">訂單</a>
        </li>
      </ul>

      <ul class="navbar-nav ms-auto">
        <li class="nav-item">
          <a href="/register" class="nav-link" routerLink="register">註冊</a>
        </li>
        <li class="nav-item">
          <a href="/login" class="nav-link" routerLink="login">登入</a>
        </li>
      </ul>
    </div>
  </nav>

  <div class="container">
    <router-outlet></router-outlet>
  </div>
</div>
```

## 執行程式

結行結果：

導覽至`首頁`時
![image](https://user-images.githubusercontent.com/21993717/179895045-4f551f98-aced-4345-9c72-b27baaba56d6.png)

導覽至`使用者資料`功能時
![image](https://user-images.githubusercontent.com/21993717/179895140-14b63900-02f7-40f2-a059-f5ea3dcc43d3.png)

導覽至`訂單`功能時
![image](https://user-images.githubusercontent.com/21993717/179895320-1cdf6df0-61ce-4667-b158-4353fdc46a10.png)

會發現瀏覽器的頁面標題都維護是"AngularTitlestrategy"。這個標題的設定值是在 index.html　中

![image](https://user-images.githubusercontent.com/21993717/179895596-58ff456f-a637-4c90-9f73-89294bdeaaf6.png)


## 使用 Angular 14 新增的功能 - TitleStrategy

Title　Strategy是 Angular 14內建的功能，只須在 Routes 設定值中加入 title 屬性值即可自動更新瀏覽器的頁面標題

![image](https://user-images.githubusercontent.com/21993717/179895932-8da01aac-5774-4242-97e0-567588f8adf3.png)

當導覽至`Home`時，瀏覽器的頁面標題自動更新為`首頁`
![image](https://user-images.githubusercontent.com/21993717/179896358-0f305e21-1396-4b0e-8d56-45066a5780dd.png)

當導覽至`使用者資料`時，瀏覽器的頁面標題自動更新為`使用者資料`
![image](https://user-images.githubusercontent.com/21993717/179896539-60bc8493-641f-4852-b9ac-aceceb98781a.png)

當導覽至`訂單`時，瀏覽器的頁面標題自動更新為`訂單`
![image](https://user-images.githubusercontent.com/21993717/179896682-48197c0b-e50a-44fb-b64c-43859237f50a.png)


## 客制化標題

### 新增一個繼承自 TitleStrategy 的 Class

這個 class 會覆寫掉預設的 Title Strategy

```ts
@Injectable()
export class TemplatePageTitleStrategy extends TitleStrategy {
  constructor(private readonly title: Title) {
    super();
  }

  override updateTitle(routerState: RouterStateSnapshot) {
    const title = this.buildTitle(routerState);
    if (title !== undefined) {
      this.title.setTitle(`My App - ${title}`); // 透過 setTitle 來覆寫預設的 Title 值
    }
  }
}
```

完整的 app-routing.module.ts
```ts {linenos=table,hl_lines=[9]}
import { Injectable, NgModule } from '@angular/core';
import { Title } from '@angular/platform-browser';
import { RouterModule, RouterStateSnapshot, Routes, TitleStrategy } from '@angular/router';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { OrderComponent } from './pages/order/order.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';
import { UserdataComponent } from './pages/userdata/userdata.component';

const routes: Routes = [
  { path: 'home', component: HomeComponent, title: '首頁' },
  { path: 'login', component: LoginComponent, title: '登入' },
  { path: 'register', component: RegisterComponent, title: '註冊' },
  { path: 'profile', component: ProfileComponent, title: 'Profile' },
  { path: 'userdata', component: UserdataComponent, title: '使用者資料' },
  { path: 'order', component: OrderComponent, title: '訂單' },
  { path: '', redirectTo: 'home', pathMatch: 'full'}
];

@Injectable()
export class TemplatePageTitleStrategy extends TitleStrategy {
  constructor(private readonly title: Title) {
    super();
  }

  override updateTitle(routerState: RouterStateSnapshot) {
    const title = this.buildTitle(routerState);
    if (title !== undefined) {
      this.title.setTitle(`My App - ${title}`);
    }
  }
}

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
  providers: [
    { provide: TitleStrategy, useClass: TemplatePageTitleStrategy }
  ]
})
export class AppRoutingModule { }
```
### 執行結果

![image](https://user-images.githubusercontent.com/21993717/179898292-0ed4b398-45ae-41bd-ac57-c955f2f30bca.png)

![image](https://user-images.githubusercontent.com/21993717/179898362-e4070324-1cd7-492f-aa51-076231df8275.png)

![image](https://user-images.githubusercontent.com/21993717/179898403-a049440b-6388-49dc-83a1-9fda888267b0.png)

真感謝 Angular 14 將此需求變成內建功能。