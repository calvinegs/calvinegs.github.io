---
title: "在 Angular 使用 Material CDK 來實作拖曳功能的使用者介面"
date: 2023-10-02
description: "本篇筆記是來記錄如何使用 Material CDK 來實現拖曳功能"
tags: ["angular", "Material CDK", "drag and drop"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular-dragdrop.git)*

在網頁上實作可拖曳的使用者介面可大符增加應用程式使用操作的友善性。

透過拖放，使用者可以使用滑鼠或觸控板點擊檔案並將其拖曳到可放置元素（放置區域），然後透過釋放滑鼠按鈕來「放置」檔案。這使您可以相對輕鬆地建立非常直觀的用戶體驗。 

要在 angular app 上實現這樣的功能除了可以透過第三方的 UI Component (如：igniteui、ngx-drag-drop)的支援外，最方便的解決方案是使用 Angular 內建的功能 Material CDK 來實作。

Angular Drag and Drop CDK（元件開發套件）提供自由拖曳、清單內排序、清單之間移動項目、動畫、觸摸裝置、自訂拖曳手把(handle)等的支援。

在下面的實作過程將會使用到下列技術:

- Angular cli 16
- Bootstrap 5 (UI Framework)
- Angular 16
    - Router
    - Standalone component
- Angular Material CDK
    - CdkDrag
    - CdkDragHandle
    - CdkDropList
    - CdkDropListGroup
    - moveItemInArray

# 建立一個 Angular 應用程式

## 使用 angular cli 建立應用程式

將採 standalone 架構來建置這個應用程式，使用以下指令如下

```bash
$ npm init @angular dragDropApp -- --routing --style scss --standalone && cd dragDropApp
Need to install the following packages:
  @angular/create@16.2.4
Ok to proceed? (y) 
Standalone application structure is new and not yet supported by many existing 'ng add' and 'ng update' integrations with community libraries.
CREATE dragDropApp/README.md (1065 bytes)
CREATE dragDropApp/.editorconfig (274 bytes)
CREATE dragDropApp/.gitignore (548 bytes)
CREATE dragDropApp/angular.json (3136 bytes)
CREATE dragDropApp/package.json (1044 bytes)
CREATE dragDropApp/tsconfig.json (901 bytes)
CREATE dragDropApp/tsconfig.app.json (263 bytes)
CREATE dragDropApp/tsconfig.spec.json (273 bytes)
CREATE dragDropApp/.vscode/extensions.json (130 bytes)
CREATE dragDropApp/.vscode/launch.json (470 bytes)
CREATE dragDropApp/.vscode/tasks.json (938 bytes)
CREATE dragDropApp/src/main.ts (250 bytes)
CREATE dragDropApp/src/favicon.ico (948 bytes)
CREATE dragDropApp/src/index.html (297 bytes)
CREATE dragDropApp/src/styles.scss (80 bytes)
CREATE dragDropApp/src/app/app.component.scss (0 bytes)
CREATE dragDropApp/src/app/app.component.html (23115 bytes)
CREATE dragDropApp/src/app/app.component.spec.ts (903 bytes)
CREATE dragDropApp/src/app/app.component.ts (373 bytes)
CREATE dragDropApp/src/app/app.config.ts (227 bytes)
CREATE dragDropApp/src/app/app.routes.ts (77 bytes)
CREATE dragDropApp/src/assets/.gitkeep (0 bytes)
✔ Packages installed successfully.
    Successfully initialized git.
```

## 測試新産生的 angular app

```bash
$ npm start

> drag-drop-app@0.0.0 start
> ng serve

✔ Browser application bundle generation complete.

Initial Chunk Files   | Names         |  Raw Size
vendor.js             | vendor        |   2.35 MB | 
polyfills.js          | polyfills     | 333.17 kB | 
styles.css, styles.js | styles        | 230.91 kB | 
main.js               | main          |  46.88 kB | 
runtime.js            | runtime       |   6.52 kB | 

                      | Initial Total |   2.95 MB

Build at: 2023-10-02T02:25:15.897Z - Hash: 077008d4030a80bf - Time: 7817ms

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **


✔ Compiled successfully.
```

開啟瀏覽器，連到 http://localhost:4200 

![2023-10-02 10-29-40](https://user-images.githubusercontent.com/21993717/271870963-7bdaf703-75d7-416b-8a3a-75599d1ab9b9.png)

## 檢視 angular 版本

```bash
$ npx ng version


     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/
    

Angular CLI: 16.2.4
Node: 18.17.1
Package Manager: npm 9.6.7
OS: linux x64

Angular: 16.2.7
... animations, common, compiler, compiler-cli, core, forms
... platform-browser, platform-browser-dynamic, router

Package                         Version
---------------------------------------------------------
@angular-devkit/architect       0.1602.4
@angular-devkit/build-angular   16.2.4
@angular-devkit/core            16.2.4
@angular-devkit/schematics      16.2.4
@angular/cli                    16.2.4
@schematics/angular             16.2.4
rxjs                            7.8.1
typescript                      5.1.6
zone.js                         0.13.3
```

# 安裝 Bootstrap 5

應程程式 UI 會使用到 bootstrap，所以先安裝 Bootstrap 5，使用方式有二種

## 使用 Bootstrap 方法一　

在 index.html 中引入 bootstrap link

```html

 <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
```

## 使用 bootstrap 方法二

透過 npm 安裝方式將 bootstrap 安置在 local 中

### install bootstrap 到 local

```bash
$ npm install bootstrap --save

added 2 packages, and audited 965 packages in 3s

115 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

安裝程式後可查看 package.json 套件設定檔　dependencies 區段中會已記錄有 "bootstrap": "^5.3.2" 這個設定值

### 修改 src/styles.scss

使用 vscode 開啟此專案目錄，並打開 styles.scss，加入以下程式碼 (即引入 bootstrap.css到本專案中)

```css
@import "~bootstrap/dist/css/bootstrap.css";
```

# 開始建置應用程式

## 修改 App Component

先將 App Component 修改成擁有一個 Toolbar，由這個 Toolbar 可以持呼叫每一個功能元件。

### 修改 app.component.html

在這個 view 中使用到了 bootstrap 的功能來裝飾整個 UI。同時也用到了 Rout 相關的 directives。主要的用意是透過 routerLink 提供的功能可以用來切換不同的功能(元件)頁面，同時透過 routerLinkActive="active" 來強調被觸發的 route path。

```html
<div class="app">
  <nav class="navbar navbar-expand navbar-dark bg-dark">
    <div class="container-fluid">
      <a href="#" class="navbar-brand">drag and drop 使用範例</a>
      <ul class="nav nav-pills">
        <li class="nav-item">
          <a class="nav-link" routerLinkActive="active" [routerLink]="['home']">首頁</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" routerLinkActive="active" [routerLink]="['dragable']">Dragable</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" routerLinkActive="active" routerLink="reordable">Reorderable List</a>
        </li>
        <li class="nav-item" routerLinkActive="active">
          <a class="nav-link" routerLinkActive="active" routerLink="transferData" >Transferring</a>
        </li>
      </ul>
      <ul class="navbar-nav ms-auto">
      </ul>
    </div>
  </nav>
  <div class="container">
      <router-outlet></router-outlet>
  </div>
</div>
```

### 修改 app.component.ts

由於在 html 中使用到 Router 的相關 directive 所以在 class 中記得要 import 相關模組。

```ts {linenos=table,hl_lines=[2,7]}
import { Component } from '@angular/core';
import { RouterLink, RouterLinkActive, RouterOutlet } from '@angular/router';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RouterLinkActive, RouterLink],
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'dragDropApp';
}
```

查看修改後的結果：

![image](https://user-images.githubusercontent.com/21993717/271882896-ec00a156-7ef2-47bb-a013-4ad54d7a3ffb.png)


## 建立各具特別功能的元件雛型

透過 angular cli 來新增以下功能的元件：
- home 元件 (預設)
- 基本拖曳功能的元件
- 支援拖曳排序功能的元件
- 支援拖曳功能來交換資料的元件

```bash
$ npx ng g c pages/home --standalone
$ npx ng g c pages/dragable --standalone
$ npx ng g c pages/reordering-list --standalone
$ npx ng g c pages/transfer-data-lists --standalone
```

完成新增雛型後，整個專案目前的檔案結構如下：

![image](https://user-images.githubusercontent.com/21993717/271884877-e401f737-c3b1-4f42-a1ef-5b001ffa5654.png)


## 完成 Router 相關的配置

開啟 app.router.ts，並完成相關 router 的設定

```ts
import { Routes } from '@angular/router';
import { HomeComponent } from './pages/home/home.component';
import { DragableComponent } from './pages/dragable/dragable.component';
import { ReorderingListComponent } from './pages/reordering-list/reordering-list.component';
import { TransferDataListsComponent } from './pages/transfer-data-lists/transfer-data-lists.component';

export const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'dragable', component: DragableComponent },
  { path: 'reordable', component: ReorderingListComponent },
  { path: 'transferData', component: TransferDataListsComponent },
  { path: '', redirectTo: 'home', pathMatch: 'full' }
];
```

在完成 router 配置後，使用 npm start 來啟動程式，查看目前程式執行結果：

![image](https://user-images.githubusercontent.com/21993717/271885292-c45104ac-e23c-4429-a675-f3918251d463.png)


接著完成各式拖曳功能展示的元件中的相關程式內容：

## 安裝　Material CDK

所有的拖曳功能是由 Material CDK 所支援，所以我們要先安裝它

```bash
$ npm install --save @angular/cdk

added 2 packages, and audited 967 packages in 6s

115 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

![image](https://user-images.githubusercontent.com/21993717/272223730-8dc52c93-9f71-4546-aae8-ff2a0ce3098f.png)


## 完成 新增一個基本拖曳功能的元件的功能

### dragable.component.ts

```ts {linenos=table,hl_lines=[2,7]}
import { Component } from '@angular/core';
import { CdkDrag } from '@angular/cdk/drag-drop';

@Component({
  selector: 'app-dragable',
  standalone: true,
  imports: [CdkDrag],
  templateUrl: './dragable.component.html',
  styleUrls: ['./dragable.component.scss']
})
export class DragableComponent {

}
```

### dragable.component.html

只要在 div tag 中加入 cdkDrag directive 就可以

```html
<div class="example-box" cdkDrag>
  Drag me around
</div>
```

### dragable.component.scss

幫 div tag 加上長寬與邊線。並於 div 拖曳時顯示陰影。

```scss
.example-box {
  width: 200px;
  height: 200px;
  border: solid 1px #ccc;
  color: rgba(0, 0, 0, 0.87);
  cursor: move;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  background: #fff;
  border-radius: 4px;
  position: relative;
  z-index: 1;
  transition: box-shadow 200ms cubic-bezier(0, 0, 0.2, 1);
  box-shadow: 0 3px 1px -2px rgba(0, 0, 0, 0.2),
              0 2px 2px 0 rgba(0, 0, 0, 0.14),
              0 1px 5px 0 rgba(0, 0, 0, 0.12);
}

.example-box:active {
  box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2),
              0 8px 10px 1px rgba(0, 0, 0, 0.14),
              0 3px 14px 2px rgba(0, 0, 0, 0.12);
}
```

![image](https://user-images.githubusercontent.com/21993717/272230918-a3fd4d83-b804-434a-8823-bb9224d473cd.png)


## 新增一個支援拖曳排序功能的元件的功能

### reordering-list.component.ts

```ts
import { Component } from '@angular/core';
import { CdkDragDrop, CdkDropList, CdkDrag, moveItemInArray } from '@angular/cdk/drag-drop';
import { NgFor } from '@angular/common';

@Component({
  selector: 'app-reordering-list',
  standalone: true,
  imports: [CdkDropList, NgFor, CdkDrag],
  templateUrl: './reordering-list.component.html',
  styleUrls: ['./reordering-list.component.scss']
})
export class ReorderingListComponent {
  movies = [
    'Episode I - The Phantom Menace',
    'Episode II - Attack of the Clones',
    'Episode III - Revenge of the Sith',
    'Episode IV - A New Hope',
    'Episode V - The Empire Strikes Back',
    'Episode VI - Return of the Jedi',
    'Episode VII - The Force Awakens',
    'Episode VIII - The Last Jedi',
    'Episode IX – The Rise of Skywalker',
  ];

  drop(event: CdkDragDrop<string[]>) {
    moveItemInArray(this.movies, event.previousIndex, event.currentIndex);
  }
}

```
### reordering-list.component.html

```html
<div cdkDropList class="example-list" (cdkDropListDropped)="drop($event)">
  <div class="example-box" *ngFor="let movie of movies" cdkDrag>{{movie}}</div>
</div>
```

### reordering-list.component.scss

```scss
.example-list {
  width: 500px;
  max-width: 100%;
  border: solid 1px #ccc;
  min-height: 60px;
  display: block;
  background: white;
  border-radius: 4px;
  overflow: hidden;
}

.example-box {
  padding: 20px 10px;
  border-bottom: solid 1px #ccc;
  color: rgba(0, 0, 0, 0.87);
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  box-sizing: border-box;
  cursor: move;
  background: white;
  font-size: 14px;
}

.cdk-drag-preview {
  box-sizing: border-box;
  border-radius: 4px;
  box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2),
              0 8px 10px 1px rgba(0, 0, 0, 0.14),
              0 3px 14px 2px rgba(0, 0, 0, 0.12);
}

.cdk-drag-placeholder {
  opacity: 0;
}

.cdk-drag-animating {
  transition: transform 250ms cubic-bezier(0, 0, 0.2, 1);
}

.example-box:last-child {
  border: none;
}

.example-list.cdk-drop-list-dragging .example-box:not(.cdk-drag-placeholder) {
  transition: transform 250ms cubic-bezier(0, 0, 0.2, 1);
}
```

![image](https://user-images.githubusercontent.com/21993717/272280733-aaba7510-2c0c-40f0-8739-2032915ced30.png)

註：若要實作“自定的 Place holder“，可以使用 CdkDragPlaceholder 指令來達成：
  - 先 import CdkDragPlaceholder
  - 在 template 中加入　```<div class="custom-placeholder" *cdkDragPlaceholder></div>```
  - 在 css 中加入 custom-placeholder 這個 class 的相關設定。

## 新增一個支援拖曳功能來交換資料的元件的功能

### transfer-data-lists.component.ts

```ts
import { Component } from '@angular/core';
import {NgFor} from '@angular/common';
import {
  CdkDragDrop,
  CdkDrag,
  CdkDropList,
  CdkDropListGroup,
  moveItemInArray,
  transferArrayItem,
} from '@angular/cdk/drag-drop';

@Component({
  selector: 'app-transfer-data-lists',
  standalone: true,
  imports: [CdkDropListGroup, CdkDropList, NgFor, CdkDrag],
  templateUrl: './transfer-data-lists.component.html',
  styleUrls: ['./transfer-data-lists.component.scss']
})
export class TransferDataListsComponent {
  todo = ['Get to work', 'Pick up groceries', 'Go home', 'Fall asleep'];

  done = ['Get up', 'Brush teeth', 'Take a shower', 'Check e-mail', 'Walk dog'];

  drop(event: CdkDragDrop<string[]>) {
    if (event.previousContainer === event.container) {
      moveItemInArray(event.container.data, event.previousIndex, event.currentIndex);
    } else {
      transferArrayItem(
        event.previousContainer.data,
        event.container.data,
        event.previousIndex,
        event.currentIndex,
      );
    }
  }
}
```

註1.:若需要支援“可拖放重複項目”可參考以下程式碼：

```ts
drop(event: CdkDragDrop<string[]>) {
    // if (event.previousContainer === event.container) {
    //   moveItemInArray(event.container.data, event.previousIndex, event.currentIndex);
    // } else {
    //   transferArrayItem(
    //     event.previousContainer.data,
    //     event.container.data,
    //     event.previousIndex,
    //     event.currentIndex,
    //   );
    // }
    if (event.previousContainer === event.container) {
      moveItemInArray(event.container.data, event.previousIndex, event.currentIndex);
    } else {
        let idx=event.container.data.indexOf(event.previousContainer.data[event.previousIndex]);
        if(idx != -1){
          return;
      }
        copyArrayItem(event.previousContainer.data,
                        event.container.data,
                        event.previousIndex,
                        event.currentIndex);
    }
  }
  ```


### transfer-data-lists.component.html

```html
<div cdkDropListGroup>
  <div class="example-container">
    <h2>To do</h2>

    <div
      cdkDropList
      [cdkDropListData]="todo"
      class="example-list"
      (cdkDropListDropped)="drop($event)">
      <div class="example-box" *ngFor="let item of todo" cdkDrag>{{item}}</div>
    </div>
  </div>

  <div class="example-container">
    <h2>Done</h2>

    <div
      cdkDropList
      [cdkDropListData]="done"
      class="example-list"
      (cdkDropListDropped)="drop($event)">
      <div class="example-box" *ngFor="let item of done" cdkDrag>{{item}}</div>
    </div>
  </div>
</div>
```

註2.：若要使得某一個 List 中的 Item 無法支援拖曳自身來重新排序可加入 cdkDropListSortingDisabled directive。一旦加入了這個指令，那麼這個 List 中的 Item 只支援被拖曳到其他 List 中。

### transfer-data-lists.component.scss

```scss
.example-container {
  width: 400px;
  max-width: 100%;
  margin: 0 25px 25px 0;
  display: inline-block;
  vertical-align: top;
}

.example-list {
  border: solid 1px #ccc;
  min-height: 60px;
  background: white;
  border-radius: 4px;
  overflow: hidden;
  display: block;
}

.example-box {
  padding: 20px 10px;
  border-bottom: solid 1px #ccc;
  color: rgba(0, 0, 0, 0.87);
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  box-sizing: border-box;
  cursor: move;
  background: white;
  font-size: 14px;
}

.cdk-drag-preview {
  box-sizing: border-box;
  border-radius: 4px;
  box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2),
              0 8px 10px 1px rgba(0, 0, 0, 0.14),
              0 3px 14px 2px rgba(0, 0, 0, 0.12);
}

.cdk-drag-placeholder {
  opacity: 0;
}

.cdk-drag-animating {
  transition: transform 250ms cubic-bezier(0, 0, 0.2, 1);
}

.example-box:last-child {
  border: none;
}

.example-list.cdk-drop-list-dragging .example-box:not(.cdk-drag-placeholder) {
  transition: transform 250ms cubic-bezier(0, 0, 0.2, 1);
}
```

![image](https://user-images.githubusercontent.com/21993717/272281103-1c33d608-e8c1-4839-897f-2f708d403741.png)


註3.：若要拖曳過程想使用被拖曳項目內容來決定是否可以到加入到目標清單中，可使用 Predicate 功能：
- 在被拖曳項目中加入 cdkDragData property，用來將被拖曳項目值被㩦出到目標清單中
- 目標清單中加入　cdkDropListEnterPredicate 並指定判斷的 function
- 在 class 中實現 function 的判斷邏輯

```html {linenos=table,hl_lines=[10,22]}
<div cdkDropListGroup>
  <div class="example-container">
    <h2>To do</h2>

    <div
      cdkDropList
      [cdkDropListData]="todo"
      class="example-list"
      (cdkDropListDropped)="drop($event)">
      <div class="example-box" *ngFor="let item of todo" [cdkDragData]="item" cdkDrag>{{item}}</div>
    </div>
  </div>

  <div class="example-container">
    <h2>Done</h2>

    <div
      cdkDropList
      [cdkDropListData]="done"
      class="example-list"
      (cdkDropListDropped)="drop($event)"
      [cdkDropListEnterPredicate]="conditionPredicate">

      <div class="example-box" *ngFor="let item of done" cdkDrag>{{item}}</div>
    </div>
  </div>
</div>
```

```ts
...
  conditionPredicate(item: CdkDrag<string>) {
    return item.data  === 'Get to work';
  }
...
```