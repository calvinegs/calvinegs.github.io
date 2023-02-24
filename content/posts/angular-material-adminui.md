---
title: "使用 Angular Material 建立一個簡易的 Admin Template"
date: 2023-02-12
description: "在這個範例中可以看到使用 Angular 搭配 angular material 所建立的簡易版的｀admin template｀"
tags: ["angular", "material", "admin template"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/Angular_Material_Admin_Template.git)*

# 使用 Angular Material 建立一個簡易的 Admin Template

Angular 是一個標準的 SPA App framework，今天要記錄的是使用 angular material 來建立一個 SPA App 的範本，除了介紹 Material 相關的功能外，完成的這個簡易型 admin template 剛好可以用來引申後續 angular 筆記範例中的㮒準介面(UI).

想要得到的結果：

![2023-02-12 12-43-16 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/218293377-7777e217-5c57-4a5e-9f78-e06ce878dc58.png)

## 檢視環境
在開發環境中必須有 NodeJs
```bash
$ node -v
v18.12.1
```

angular cli 則只需安裝在 Local 目錄中即可
ng version
```bash
$ ng version
Command 'ng' not found, but can be installed with:

sudo apt install ng-common
```


## 建立 Angular 專案

透過 `npm init @angular `語法即可在不必安裝 angular cli 前題下來建立一個 angular project。

下述語法中的 `<project Name>` 替換成實際的專案名稱（如：adminTemplate)

```bash
$ npm init @angular <project Name> -- --routing --style=scss && cd <project Name>

CREATE adminTest/README.md (1063 bytes)
CREATE adminTest/.editorconfig (274 bytes)
CREATE adminTest/.gitignore (548 bytes)
CREATE adminTest/angular.json (2934 bytes)
CREATE adminTest/package.json (1041 bytes)
CREATE adminTest/tsconfig.json (901 bytes)
CREATE adminTest/tsconfig.app.json (263 bytes)
CREATE adminTest/tsconfig.spec.json (273 bytes)
CREATE adminTest/.vscode/extensions.json (130 bytes)
CREATE adminTest/.vscode/launch.json (474 bytes)
CREATE adminTest/.vscode/tasks.json (938 bytes)
CREATE adminTest/src/favicon.ico (948 bytes)
CREATE adminTest/src/index.html (295 bytes)
CREATE adminTest/src/main.ts (214 bytes)
CREATE adminTest/src/styles.scss (80 bytes)
CREATE adminTest/src/assets/.gitkeep (0 bytes)
CREATE adminTest/src/app/app-routing.module.ts (245 bytes)
CREATE adminTest/src/app/app.module.ts (393 bytes)
CREATE adminTest/src/app/app.component.scss (0 bytes)
CREATE adminTest/src/app/app.component.html (23115 bytes)
CREATE adminTest/src/app/app.component.spec.ts (1082 bytes)
CREATE adminTest/src/app/app.component.ts (214 bytes)
✔ Packages installed successfully.
    Successfully initialized git.
```

建立成功後可使用 `npx ng version` 來檢視專案中使用的 angular cli 版本。

```bash
$ npx ng version


     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/
    

Angular CLI: 15.0.5
Node: 18.12.1
Package Manager: npm 8.19.2
OS: linux x64

Angular: 15.1.4
... animations, common, compiler, compiler-cli, core, forms
... platform-browser, platform-browser-dynamic, router

Package                         Version
---------------------------------------------------------
@angular-devkit/architect       0.1501.5
@angular-devkit/build-angular   15.1.5
@angular-devkit/core            15.1.5
@angular-devkit/schematics      15.0.5
@angular/cli                    15.0.5
@schematics/angular             15.0.5
rxjs                            7.5.7
typescript                      4.8.4
```


## 安裝 Angular Material

使用 `ng add @angular/material` 指令來為專案設置要使用 Material ui component 的相關環境。(安裝 Angular Material、Component Dev Kit (CDK) 和 Angular Animations 及引用字型...等)

```bash
$ npx ng add @angular/material

ℹ Using package manager: npm
✔ Found compatible package version: @angular/material@15.1.4.
✔ Package information loaded.

The package @angular/material@15.1.4 will be installed and executed.
Would you like to proceed? Yes
✔ Packages successfully installed.
? Choose a prebuilt theme name, or "custom" for a custom theme: Custom
? Set up global Angular Material typography styles? Yes
? Include the Angular animations module? Include and enable animations
UPDATE package.json (1111 bytes)
✔ Packages installed successfully.
CREATE src/custom-theme.scss (1479 bytes)
UPDATE src/app/app.module.ts (502 bytes)
UPDATE angular.json (2819 bytes)
UPDATE src/index.html (581 bytes)
UPDATE src/styles.css (181 bytes)
```

加入 git 新版本
```bash
$ git add . && git commit -m "Add Material to project"
```

## 建立一個專屬的 module file

為使專案管理方便，先建立一個 material 專屬的 module file.

```bash
$ npx ng g m ./shared/shareMaterial --flat
```

由上述 angular cli 所産生的 share-material.module.ts 檔案

```ts {linenos=table}
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';



@NgModule({
  declarations: [],
  imports: [
    CommonModule
  ]
})
export class ShareMaterialModuleModule { }
```

## 使用 material 所附加的 schematics 來建立 admin template ui

下述語法中的參數 `--module ./shared/share-material`，為的是將新産生的 material navigation compoent 産生在 share-material module 中。

```bash
$ npx ng g @angular/material:navigation navigation --module ./shared/share-material
```

註：除上述的 @angular/material:navigation 外，還有：
- address-form
- dashboard
- table
- tree

使用 @angular/material:navigation schematics 除了産生 navigation component 外，也會自動在 module file (share-material.module.ts) 中自動引中必需的 module

```ts {linenos=table}
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { FormsModule } from '@angular/forms';



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
    MatSlideToggleModule
  ],
  exports: [
    NavigationComponent
  ]
})
export class ShareMaterialModule { }
```

## 把 ShareMaterialModule 匯入到 app.module.ts 檔案中

為了在專案其他地方(如：AppComponent中）使用 NavidationComponent，不要忘記在 app.module.ts 中引入 ShareMaterialModule module。

```ts {linenos=table,hl_lines=[8,17]}
# app.module.ts 
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { ShareMaterialModule } from './shared/share-material.module';

@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule,
    ShareMaterialModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

## 把 NavigationComponent export 出去

同時為了在 AppComponent 中使用 NavigationComponent，也別忘了把 NavidationComponent export 出去。

share-material.module.ts:

```ts {linenos=table,hl_lines=[8,25]}
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';

@NgModule({
  declarations: [
    NavigationComponent
  ],
  imports: [
    CommonModule,
    LayoutModule,
    MatToolbarModule,
    MatButtonModule,
    MatSidenavModule,
    MatIconModule,
    MatListModule,
  ],
  exports: [
    NavigationComponent
  ]
})
export class ShareMaterialModule { }
```

## 修改 app.component.html 

將內容刪除,並將 app-navigation selector 放進 app.component.html
```html
<app-navigation></app-navigation>
```

## 執行測試

使用 `$ npm start` 來查看一下結果。

![image](https://user-images.githubusercontent.com/21993717/218250294-4e6f7bf5-9e68-4c28-8fee-6720e90d8eae.png)


## 新增 Dark Theme (暗黑主題)

到目前為止，已經産生了一個陽春版的 admin template 了，接下來來豐富一下這個樣本的其他功能-新增一個｀暗黑主題｀。

### 在 styles.scss 新增 Dark theme 相關設定

```css {linenos=table}
/* ... */
// for Dark Mode
$adminTemplate-primary-dark: mat.define-palette(mat.$purple-palette);
$adminTemplate-accent-dark: mat.define-palette(mat.$green-palette, A200, A100, A400);
$adminTemplate-warn-dark: mat.define-palette(mat.$red-palette);

$adminTemplate-theme-dark: mat.define-dark-theme((
  color: (
    primary: $adminTemplate-primary-dark,
    accent: $adminTemplate-accent-dark,
    warn: $adminTemplate-warn-dark,
  )
));

.dark-theme-mode {
  @include mat.all-component-themes($adminTemplate-theme-dark);
}
```

完成 CSS 中加入 Dark Theme 的設置後，先在 navigation.component.ts 中新增一個變數: isDarkTheme 用來記錄是否切換到 Dark 主題。


### navigation.component.html 中的相關設置

並在 navigation.component.html 中透過 ngClass 語法加入設置好的 dark theme。

```html
<mat-sidenav-container class="sidenav-container mat-app-background" [ngClass]="{'dark-theme-mode':isDarkTheme}">
```

接著繼續在 html 中加入一個可切換的開關 component: mat-slide-toggle 並透過 ngModel 綁定到 isDarkTheme 變數
 
```html
	<mat-slide-toggle [(ngModel)]="isDarkTheme"></mat-slide-toggle>
```

### share-material.module.ts 要匯入相關 module

要使用 mat-slide-toggle component 必須在 module file 中 import MatSlideToggleModule

```ts
# share-material.module.ts
<!-- ... -->
import { MatSlideToggleModule } from '@angular/material/slide-toggle';

<!-- ... -->

imports:[
	...
	MatSlideToggleModule
],
// ...
```

由於使用到了 [(ngModel)]，所以必須在 module file 中 import FormsModule

```ts
# share-material.module.ts
// ...
import { FormsModule } from '@angular/forms';

// ...

imports:[
	// ...
	FormsModule
],
// ...
```

完成後的 navigation.component.html
```html {linenos=table}
<mat-sidenav-container class="sidenav-container mat-app-background" [ngClass]="{'dark-theme-mode':isDarkTheme}">
  <mat-sidenav #drawer class="sidenav" fixedInViewport
      [attr.role]="(isHandset$ | async) ? 'dialog' : 'navigation'"
      [mode]="(isHandset$ | async) ? 'over' : 'side'"
      [opened]="(isHandset$ | async) === false">
    <mat-toolbar>Menu</mat-toolbar>
    <mat-nav-list>
      <a mat-list-item href="#">Link 1</a>
      <a mat-list-item href="#">Link 2</a>
      <a mat-list-item href="#">Link 3</a>
    </mat-nav-list>
  </mat-sidenav>
  <mat-sidenav-content>
    <mat-toolbar color="primary">
      <button
        type="button"
        aria-label="Toggle sidenav"
        mat-icon-button
        (click)="drawer.toggle()"
        *ngIf="isHandset$ | async">
        <mat-icon aria-label="Side nav toggle icon">menu</mat-icon>
      </button>
      <span>adminTest</span>
      <mat-slide-toggle [(ngModel)]="isDarkTheme"></mat-slide-toggle>
    </mat-toolbar>
    <!-- Add Content Here -->
  </mat-sidenav-content>
</mat-sidenav-container>
```

完成後的 navigation.component.ts
```ts {linenos=table}
import { Component } from '@angular/core';
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';
import { Observable } from 'rxjs';
import { map, shareReplay } from 'rxjs/operators';

@Component({
  selector: 'app-navigation',
  templateUrl: './navigation.component.html',
  styleUrls: ['./navigation.component.scss']
})
export class NavigationComponent {
  isDarkTheme= false;
  
  isHandset$: Observable<boolean> = this.breakpointObserver.observe(Breakpoints.Handset)
    .pipe(
      map(result => result.matches),
      shareReplay()
    );

  constructor(private breakpointObserver: BreakpointObserver) {}

}
```

完成後的 share-material.module.ts 
```ts {linenos=table}
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { FormsModule } from '@angular/forms';



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
    MatSlideToggleModule
  ],
  exports: [
    NavigationComponent
  ]
})
export class ShareMaterialModule { }
```

### 測試結果

執行 $ npm start 來看一下結果

![image](https://user-images.githubusercontent.com/21993717/218253626-3d2b5906-0c3b-4621-8796-df2bd485ea14.png)

![image](https://user-images.githubusercontent.com/21993717/218253646-5fce145f-c48d-4664-8c7a-d05e9dfb6242.png)

程式已經可以透過`開關`來切換不同的主題。

### 優化 UI

接著，將 UI 再優化一下，把開關移至畫面右側，並於開關前面顯示一個圖示，分別表示套用了不同的主題。

```html {linenos=table}
      <span class="ext-space"></span>
      <div *ngIf="this.isDarkTheme; else LightTheme">
        <mat-icon>bedtime</mat-icon>
      </div>
      <ng-template #LightTheme>
        <div *ngIf="!this.isDarkTheme">
          <mat-icon>wb_sunny</mat-icon>
        </div>
      </ng-template>
      <mat-slide-toggle [(ngModel)]="isDarkTheme">
      </mat-slide-toggle>
```

在 navigation.component.scss 加入下列的設定，為的是將 slide-toggle 元件顯示在畫面右側

```css
/* ... */
.ext-space {
  flex: 1 0 auto;
}
```

看結果發現畫面已符合預期：

![image](https://user-images.githubusercontent.com/21993717/218253897-f19021d3-58a5-418f-a708-7d99063809b0.png)


## 將開關切換的狀態記錄下來

當在瀏覽器上按下 F5 時會發現套用 Dark Theme 不會被程式記著，開關又回復到原有的狀態。繼續調整程式把切換結果記錄到 localstorage 中。

```html
<mat-slide-toggle [(ngModel)]="isDarkTheme" (change)="storeThemeSelection()">
```

在 navigation.component.ts 加入相關程式碼

```ts {linenos=table,hl_lines=["22-28"]}
import { Component } from '@angular/core';
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';
import { Observable } from 'rxjs';
import { map, shareReplay } from 'rxjs/operators';

@Component({
  selector: 'app-navigation',
  templateUrl: './navigation.component.html',
  styleUrls: ['./navigation.component.scss']
})
export class NavigationComponent {
  isDarkTheme = false;

  isHandset$: Observable<boolean> = this.breakpointObserver.observe(Breakpoints.Handset)
    .pipe(
      map(result => result.matches),
      shareReplay()
    );

  constructor(private breakpointObserver: BreakpointObserver) {}

  ngOnInit() {
    this.isDarkTheme = localStorage.getItem('theme') === "Dark" ? true : false;
  }
  
  storeThemeSelection() {
    localStorage.setItem('theme', this.isDarkTheme ? "Dark" : "Light");
  }
}
```

再次測試發現開關切換的結果會被記錄下來了。admin template 至此已算完成。下面來看看如何使用這個樣本範例。


## 使用 admin template

### 建立新組件 Components

使用 angular cli 來産生一個新元件：`$ npx ng g c components/flexbox`


### 設置 routes

在 app-routing.module.ts 中加入 routes 相關設定

```ts
const routes: Routes = [{
  path: 'flexbox', component: FlexboxComponent
}];
```

### 在 html 中加入 router-outlet 

在 navigation.component.html 中加入 router-outlet

```html {linenos=table}
<mat-sidenav-container class="sidenav-container mat-app-background" [ngClass]="{'dark-theme-mode':isDarkTheme}">
  <mat-sidenav #drawer class="sidenav" fixedInViewport
      [attr.role]="(isHandset$ | async) ? 'dialog' : 'navigation'"
      [mode]="(isHandset$ | async) ? 'over' : 'side'"
      [opened]="(isHandset$ | async) === false">
    <mat-toolbar>Menu</mat-toolbar>
    <mat-nav-list>
      <a mat-list-item href="#">Link 1</a>
      <a mat-list-item href="#">Link 2</a>
      <a mat-list-item href="#">Link 3</a>
    </mat-nav-list>
  </mat-sidenav>
  <mat-sidenav-content>
    <mat-toolbar color="primary">
      <button
        type="button"
        aria-label="Toggle sidenav"
        mat-icon-button
        (click)="drawer.toggle()"
        *ngIf="isHandset$ | async">
        <mat-icon aria-label="Side nav toggle icon">menu</mat-icon>
      </button>
      <span>adminTest</span>
      <span class="ext-space"></span>
      <div *ngIf="this.isDarkTheme; else LightTheme">
        <mat-icon>bedtime</mat-icon>
      </div>
      <ng-template #LightTheme>
        <div *ngIf="!this.isDarkTheme">
          <mat-icon>wb_sunny</mat-icon>
        </div>
      </ng-template>
      <mat-slide-toggle [(ngModel)]="isDarkTheme" labelPosition="before" >
      </mat-slide-toggle>    
    </mat-toolbar>
    <!-- Add Content Here -->
    <router-outlet></router-outlet>
  </mat-sidenav-content>
</mat-sidenav-container>
```

### 匯入 RouterModule Module
由於在 html 中加入 router-outlet，所以必須在 share-material.module.ts 中匯入 RouterModule Module

```ts {linenos=table}
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { NavigationComponent } from '../navigation/navigation.component';
import { LayoutModule } from '@angular/cdk/layout';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatIconModule } from '@angular/material/icon';
import { MatListModule } from '@angular/material/list';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';



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
    RouterModule
  ],
  exports: [
    NavigationComponent
  ]
})
export class ShareMaterialModule { }
```


測試 routes 是否設定正確, 直接在瀏覽器輸入 localhost:4200/flexbox

![image](https://user-images.githubusercontent.com/21993717/218293259-7d09115a-db8a-4253-95ae-ef9f0d7eafce.png)

結果顯示 routes 設置無誤，接下去來完成 route-link 的設置。

### route-link 設置

在 navigation.component.html file 中加入 route-link 到 list 上: `<a mat-list-item routerLink="/flexbox">flex box</a>`

```html {linenos=table}
<mat-sidenav-container class="sidenav-container mat-app-background" [ngClass]="{'dark-theme-mode':isDarkTheme}">
  <mat-sidenav #drawer class="sidenav" fixedInViewport
      [attr.role]="(isHandset$ | async) ? 'dialog' : 'navigation'"
      [mode]="(isHandset$ | async) ? 'over' : 'side'"
      [opened]="(isHandset$ | async) === false">
    <mat-toolbar>功能列</mat-toolbar>
    <mat-nav-list>
      <a mat-list-item routerLink="/flexbox">flex box</a>
      <a mat-list-item href="#">Link 2</a>
      <a mat-list-item href="#">Link 3</a>
    </mat-nav-list>
  </mat-sidenav>
  <mat-sidenav-content>
    <mat-toolbar color="primary">
      <button
        type="button"
        aria-label="Toggle sidenav"
        mat-icon-button
        (click)="drawer.toggle()"
        *ngIf="isHandset$ | async">
        <mat-icon aria-label="Side nav toggle icon">menu</mat-icon>
      </button>
      <span>簡易的 admin template</span>
      <span class="ext-space"></span>
      <div *ngIf="this.isDarkTheme; else LightTheme">
        <mat-icon>bedtime</mat-icon>
      </div>
      <ng-template #LightTheme>
        <div *ngIf="!this.isDarkTheme">
          <mat-icon>wb_sunny</mat-icon>
        </div>
      </ng-template>
      <mat-slide-toggle [(ngModel)]="isDarkTheme"  (change)="storeThemeSelection()">
      </mat-slide-toggle>    
    </mat-toolbar>
    <!-- Add Content Here -->
    <router-outlet></router-outlet>
  </mat-sidenav-content>
</mat-sidenav-container>

```

![2023-02-12 12-43-16 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/218293377-7777e217-5c57-4a5e-9f78-e06ce878dc58.png)

