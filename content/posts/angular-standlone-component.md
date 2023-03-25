---
title: "How to using Angular Standalone Component"
date: 2023-03-13
description: "本篇筆記將要紀錄如何使 Angular 15 Standlone component"
tags: ["angular", "Standalone"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular-standalone.git)*



# 如何在 Angular 中使用 Standalone Components

本篇將要紀錄如何使 Angular 15 環境中使用 Standlone component。從版本 14 起就開始支援 Standalone component，Angular 15則正式的引入了獨立組件作為構建無模塊應用程序的簡單方法。獨立組件的好處之一是它們使新開發人員更容易學習和使用 Angular，因為它們可以不用使用到 NgModule。

Angular 從第二個版本起，@NgModule就已是一個重要的核必功能，透過 @NgModule 為一個個單位來組織我們的應用程式，包含整個應用程式的啟動，所以每個 angular 應用程式都至少要有一個模組（也稱為根模組），由此可見它的重要性。說它是 Angular 的特色之一一點也不為過。但近年來這個特色功能卻也成為其他類似架構的攻擊目標，為了使系統支援更多樣化，Angular 15 正式引入了 Standalone 的各式運用。

在本篇筆記中會提及如何建立及使用一個 Standalone component、如何在一個 Standalone component中去使用另一個 Standalone 子元件、如何在原有模式中(有 @NgModule)中載入 Standalone component，到最後如何在一個專案中完全不使用到 @NgModule 功能。

## 建立新專案

透過 npm init @angular 語法來建立一個 angular project。

```bash
$ npm init @angular standAloneComponent -- --routing --style=scss && cd standaloneComponent
```

## 建立 Standalone Component

```bash
$ npx ng g c components/containerLayout --standalone --inline-style --inline-template --flat
```

在由 cli 産生的程式碼架構中(container-layout.component.ts)，可以看到 meta data 中多了 `standalone: true` 與 `imports: [CommonModule]` m二個屬性設定資料。imports 中預設加入了 CommonModule，你也可以依需求自行加入其他模組，如：HttpClientModule，formsModule..等。

```ts {linenos=table,hl_lines=[6]}
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-container-layout',
  standalone: true,
  imports: [CommonModule],
  template: `
    <p>
      container-layout works!
    </p>
  `,
  styles: [
  ]
})
export class ContainerLayoutComponent {

}
```

同時打開 app.module.ts 會發現 ng cli 不會再把 standalone component 自動加入到 NgModule 模塊中了。


### 在 NgModule 中註冊  Standalone Component

首先，來看看如何在 NgMoudle 中註冊一個 standalone component。

打開 app.module.ts 在 imports 宣告處加入前述新增的 standalone component - ContainerLayoutComponent。 (特別注意不是加在 declarations處，而是放在 imports 裡)

```ts {linenos=table,hl_lines=[6,15]}
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { ContainerLayoutComponent } from './components/container-layout.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    ContainerLayoutComponent
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### 使用 Standalone Component

修改 app.component.html 內容如下:

```html
<app-container-layout></app-container-layout>
```

使用 `npm start` 啟動程式發現已經可以正確的使用 standalone component 了。


## 建立 Standalone 子元件

接下來要來看看如何在 standalone component 中使用 standalone 子元件。

再新建立一個 名為 logo 的 standalone 子元件

```bash
$ npx ng g c components/logo --standalone --inline-style --inline-template --flat
```

我們要在這個子元件中顯示一個預設由網路上捉取到的一個 logo 圖示，所以
將建立好的 LogoComponent Component Class 內容修改如下：

```ts {linenos=table,hl_lines=[1,9,"11-15",15]}
import { Component, Input } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-logo',
  standalone: true,
  imports: [CommonModule],
  template: `
    <img [src]="logoName">
  `,
  styles: [`
    img {
      padding:  1rem;
    }
  `]
})
export class LogoComponent {
  @Input() logoName = 'https://global-uploads.webflow.com/5e157547d6f791d34ea4e2bf/6087f7f9e5ecc6623f3ecbbe_logo-example2.svg'
}
```

## 在 Standalone 元件中使用 Standalone 子元件

將建立好的子元件放置在 Container Layout 元件中，使用子元件方式一樣只須在 imports 中引用子元件即可，使用起來相當方便。

```ts {linenos=table,hl_lines=[3,"11-16",“19-25”]}
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { LogoComponent } from './logo.component';

@Component({
  selector: 'app-container-layout',
  standalone: true,
  imports: [CommonModule, LogoComponent],
  template: `
    <app-logo></app-logo>
    <div class="container">
        <ng-content></ng-content>
    </div>
    <footer>2023</footer>
  `,
  styles: [`
    .container {
      display: flex;
      background-color: #f5f5f5;
      height: 50vh;
      flex-direction: column;
      padding: 3rem;
    }
  `
  ]
})
export class ContainerLayoutComponent {

}
```

執行的結果：

![2023-03-19 15-47-55 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/226161297-767482f2-8a83-4dd3-8d66-c14b8e970dfb.png)



## 在 standalone component 如何使用`外部模組`

接著來看看如何在 standalone 元件中使用外部模組的功能。

### 在 standalone component 中使用 ReactiveFormModule

新增一個 register standalone 元件，並在此元件中使用 angular 的 Reactive Form Module，用它來實作一個 可進行註冊的表單程式。

```bash
$ npx ng g c components/register --standalone --inline-style --inline-template --flat
```

在 register.component.ts 中使用 FormGroup, FormControl 來宣告一個有 name 及 email 的註冊表單。

```ts {linenos=table,hl_lines=[3,9,"10-17"]}
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormControl, FormGroup, ReactiveFormsModule } from '@angular/forms';

@Component({
  selector: 'app-register',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  template: `
    <form [formGroup]="registerForm" (ngSubmit)="sendForm()">
      <label for="first-name">Name: </label>
      <input id="first-name" type="text" formControlName="name">

      <label for="email">Email</label>
      <input id="email" type="email" formControlName="email">
      <button type="submit">Send</button>
    </form>
  `,
  styles: [`
  :host {
    flex: 1;
    padding: 4rem;
    display:flex;
    align-items: center;
    background: #fff;
  }

  form {
    display: flex;
    flex-direction: column;
  }

  input {
    margin-bottom: 1rem;
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    min-width: 300px;
  }

  label {
    margin-bottom: 0.5rem;
  }

  button {
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    background: #fff;
    cursor: pointer;
    width: 140px
  }

  button:active {
    background: #efe;
  }

  button:hover {
    background: #88aae1
  }
  `
  ]
}
export class RegisterComponent {
  registerForm = new FormGroup({
    name: new FormControl(''),
    email: new FormControl(''),
  });

  sendForm() {
    console.log(this.registerForm.valid);
  }
}
```

### 在 standalone component 中使用 RouterModule

新增一個名為 navigation 的 standalone 元件，並在此元件中使用 RouterModule，用它來實作如何 瀏覽各個系統功能頁面。

```bash
$ npx ng g c components/navigation --standalone --inline-style --inline-template --flat
```

在 navigation.component.ts 中使用 router 相關功能來切換各個功能頁面（有 Home、Other Domain、About等）。

```ts {linenos=table,hl_lines=[3,8,"10-20"]}
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';

@Component({
  selector: 'app-navigation',
  standalone: true,
  imports: [CommonModule, RouterModule],
  template: `
  <ul class="navigation">
    <li>
      <a routerLink="/home" routerLinkActive="active">Home</a>
    </li>
    <li>
      <a routerLink="/domains" routerLinkActive="active">Others Domains</a>
    </li>
    <li>
  <a routerLink="/about" routerLinkActive="active">About</a>
    </li>
  </ul>  `,
  styles: [`
    ul.navigation {

    }
    li {
        display: inline;
        margin: 0 10px;
    }

    li:hover {
        margin: 0 10px;
        cursor: pointer;
    }

    .active {
      font-weight: bold;
    }

  `]
})
export class NavigationComponent {

}
```

## 將各子元件組合至 ContainerLayoutComponent 中

```ts {linenos=table,hl_lines=[3,4,"11-16"]}
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { LogoComponent } from './logo.component';
import { NavigationComponent } from './navigation.component';

@Component({
  selector: 'app-container-layout',
  standalone: true,
  imports: [CommonModule, LogoComponent, NavigationComponent],
  template: `
    <app-logo></app-logo>
    <app-navigation></app-navigation>
    <div class="container">
        <ng-content></ng-content>
    </div>
    <footer>2023</footer>
  `,
  styles: [`
    .container {
      display: flex;
      background-color: #f5f5f5;
      height: 20vh;
      flex-direction: column;
      padding: 3rem;
    }
  `
  ]
})
export class ContainerLayoutComponent {

}
```

程式至此，執行結果如下：

![2023-03-19 16-22-20 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/226162710-39e3513c-472e-4d5a-b823-e42274bc12a2.png)

## 建立子頁面（功能）

在前述的 NavigationComponent 中我們已經預先設好三個 routerLink 分別要去載入 Home、Other Domain、About等三個子功能（頁面），所以接下來使用 angular cli 來新增這三個子功能。

```bash
$ npx ng g c pages/home --standalone --inline-style --inline-template --flat

$ npx ng g c pages/domain --standalone --inline-style --inline-template --flat

$ npx ng g c pages/about --standalone --inline-style --inline-template --flat
```

分別在這三個功能置入一些內容：

### about component

```ts {linenos=table,hl_lines=[]}
```

### other domain component

```ts {linenos=table,hl_lines=[]}
// domain.conponent.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-domain',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h2>Other domains</h2>

    <ul>
      <li>www.aprendetesting.com</li>
      <li>www.aprende-singlespa.com</li>
    </ul>
  `,
  styles: [
  ]
})
export class DomainComponent {

}
```

### home component

在 HomeComponent 中我們要展現如何使用子元件 - RegisterCompoent，因此記得在 imports 中匯入 RegisterCompoent。並在 template 中使用它。

```ts {linenos=table,hl_lines=[]}
// home.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RegisterComponent } from '../components/register.component';

@Component({
  selector: 'app-home',
  standalone: true,
  imports: [CommonModule, RegisterComponent],
  template: `
  <div class="sections">
    <div class="welcome">
      <h1>Yes, {{domainName}} available for sale</h1>
      <p>For instantly purchase. please make a paypal request</p>
      <button>Buy now for {{price | currency}}</button>
    </div>
    <app-register></app-register>
  </div>  `,
  styles: [
  ]
})
export class HomeComponent {
  domainName = "www.aprendetypescript.com";
  price = 100;
}
```

### 完成路由設定

```ts {linenos=table,hl_lines=[]}
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AboutComponent } from './pages/about.component';
import { DomainComponent } from './pages/domain.component';
import { HomeComponent } from './pages/home.component';

const routes: Routes = [
  {
    path: '',
    redirectTo: 'home',
    pathMatch: 'full'
  },
  {
    path: 'home',
    component: HomeComponent,
  },
  {
    path: 'domains',
    component: DomainComponent
  },
  {
    path: 'about',
    component: AboutComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

最後在 app.component.html 中加回 router-outlet，好讓 App 有一個可以呈現功能的地方。

```html
<!-- app.component.html -->
<app-container-layout>
    <router-outlet></router-outlet>
</app-container-layout>
```

### 查看完成後的結果

在 Home Page 中可以看到 standalone compoent 再嵌入 standalone 子元件的效果


![image](https://user-images.githubusercontent.com/21993717/227708321-8490d869-28d7-4e60-931c-cba37fb6f6c8.png)


![image](https://user-images.githubusercontent.com/21993717/227708404-9295ac26-62f0-4cb8-97f2-9a164058b501.png)

![image](https://user-images.githubusercontent.com/21993717/227708423-1c358ffc-00cf-4f43-bdc7-6cc6dc123b28.png)



## 延遲載入 Standalone component (lazy load Standalone component)

筆記至此，我們已看到了 Standalone 的各式使用情境。接下來再來看看如何延遲載入 Standalone component。

```ts {linenos=table,hl_lines=[12,17,22]}
// app-routingmmodule.ts
// ...
const routes: Routes = [
  {
    path: '',
    redirectTo: 'home',
    pathMatch: 'full'
  },
  {
    path: 'home',
    // component: HomeComponent,
    loadComponent: () => import('./pages/home.component').then(m => m.HomeComponent)
  },
  {
    path: 'domains',
    // component: DomainComponent
    loadComponent: () => import('./pages/domain.component').then(m => m.DomainComponent)
  },
  {
    path: 'about',
    // component: AboutComponent
    loadComponent: () => import('./pages/about.component').then(m => m.AboutComponent)
  }
];
// ...
```


## 使用 Standalone 元件來啟動程式

如何不再需要 ngModule，直接使用 Standalone 元件來啟動程式呢?

### 刪除 app.module.ts & app-routing.module.ts 兩個 @NgModule 

在移除 AppModule(app.module.ts) 之前先來看看其中的程式碼：
- 在程式第4和第14行匯入了路由的相關設定 (這部份的程式將會移入到 main.ts 中)
- 在程式第5和第10及第18行匯入了 AppComponent 及 設定 AppComponent 為程式啟動的元件 (這部份的程式也將會一併移入到 main.ts 中)
- 在程式第6行匯入了 AppComponent 內會使用到的子元件 ContainerLayoutComponent (這部份的程式將被實現在 AppComponent 中)


```ts {linenos=table,hl_lines=[4,5,6,10,14,15,18]}
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { ContainerLayoutComponent } from './components/container-layout.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    ContainerLayoutComponent
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

而在 AppRoutingModule 中的 routes 設定也會一併由 main.ts 的修改程式碼來取代


### 修改 app.component.ts

將 AppComponent 修改內容有：
- 程式第7行：加入 standalone: true 將 AppComponent 變成是一個 Standalone component
- 程式第3和8行：匯入 ComtainerLayoutComponent 子元件
- 程式第8行：匯入 RouterModule 模組是因為 AppComponent template中使用到了 <router-outlet> 這個 Directive

```ts {linenos=table,hl_lines=[3,7,8]}
import { Component } from '@angular/core';
import { RouterModule } from '@angular/router';
import { ContainerLayoutComponent } from './components/container-layout.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterModule, ContainerLayoutComponent],
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'standAloneComponent';
}

```

### 修改 main.ts

移除 Import platformBrowserDynamic 的指令

```ts
import { AppModule } from './app/app.module';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
```

移除 platformBrowserDynamic().bootstrapModule 指令

```ts
platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.error(err));
```

新加入的程式碼除了新的 bootstrap 方式，就是 routes 的相關設定：
- 程式的第1、3、5行：採取由 AppComponent 來啟動程式。
- 程式第2行及第6行之後就是有關 routes 的設定（也就是原有的 AppRoutingModule @NgMoudle 的功能）

```ts {linenos=table,hl_lines=[]}
import { bootstrapApplication } from "@angular/platform-browser";
import { provideRouter } from "@angular/router";
import { AppComponent } from "./app/app.component";

bootstrapApplication(AppComponent, {
  providers:[
      provideRouter([
        {
          path: '',
          redirectTo: 'home',
          pathMatch: 'full'
        },
        {
          path: 'home',
          loadComponent: () => import('./app/pages/home.component').then(m => m.HomeComponent)
        },
        {
          path: 'domains',
          loadComponent: () => import('./app/pages/domain.component').then(m => m.DomainComponent)
        },
        {
          path: 'about',
          loadComponent: () => import('./app/pages/about.component').then(m => m.AboutComponent)
        }
      ])
  ]
}).catch(err => console.error(err));
```

以上調整完成後，就可以安心的把 AppModule ＆ AppRoutingModule 這倆支 @NgModule 程式給移除掉。也就是說到此刻，你的應用程式已是一個 Module-Less Application了。