---
title: "Angular 14 範例: 透過 Web API 完成 CRUD 的應用程式"
date: 2022-07-11
description: "在這個範例中可以看到使用 Angular Reactive Form 搭配 後端 Web API 來完成 Todo 的新增、修改、刪除、查詢功能"
tags: ["angular", "reactive form", "directive", "rxjs", "crud"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/Angular-WebAPI-CRUD.git)*

本文主要的主題是要呈現如何完成具有 "新增、修改、刪除、查詢“ Todo List 功能的 Angular Application。同時搭配使用者角色/權限控管來呈現不同 UI 功能。

在最後的章節會介紹到如何使用 Angular Directive 來簡位程式避免程式碼重覆。

為完整性考量，還是記錄如何一步步從無到有使用 Angular 14 相關功能來建立一個使用者資料註冊、登入的表單功能（連結到後端 node+MySQL Web API )，在這些表單中按下送出時會自動檢核使用者所輸入的資料是否合乎程式中所設定的檢核邏輯，並顯示合適的訊息反應給使用者。其中將會使用到下列技術:

- Angular CLI 14
- Bootstrap 5 (UI Framework)
- Angular 
    - Reactive Form
    - Custome Validator
    - Interceptor
    - JWT
    - Session Storage
    - Router
    - Services
    - Guard & Router Data
    - RxJs
    - Custome Directive

> 註1: 若你已經查閱過 *[使用 Angular 14 建立 Reactive Form 表單驗證範例](https://calvinegs.github.io/posts/angular-auth-jwt/)* 這篇筆記了，那可以直接跳到 "完成 Todo 相關 Component 的功能" 這個章節。
>
> 註2: 若你要更瞭解後端 Web API 可參考此篇 *[使用 Node.js + express + MySQL 建立一個後端服務 REST API](https://calvinegs.github.io/posts/nodejs-restapi-mysql/)* 筆記。

## 安裝 Angular/CLI
檢視目前環境為 node v16.14.0， global 安裝的是 Angular CLI 13.3.6
```bash
$ node --version
v16.14.0

$ ng version

     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/
    

Angular CLI: 13.3.6
Node: 16.14.0
Package Manager: npm 8.3.1
OS: linux x64

Angular: 
... 

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.1303.6 (cli-only)
@angular-devkit/core         13.3.6 (cli-only)
@angular-devkit/schematics   13.3.6 (cli-only)
@schematics/angular          13.3.6 (cli-only)
```

### 在 Local 安裝 Angular CLI ver.14
```bash
$ mkdir angular14 && cd angular14

$ npm install @angular/cli@14

added 219 packages, and audited 220 packages in 6s

25 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

$ ls -al
總用量 176
drwxrwxr-x   3 egs egs   4096  六  16 11:10 .
drwxrwxr-x  10 egs egs   4096  六  16 11:10 ..
drwxrwxr-x 191 egs egs   4096  六  16 11:10 node_modules
-rw-rw-r--   1 egs egs     58  六  16 11:10 package.json
-rw-rw-r--   1 egs egs 163807  六  16 11:10 package-lock.json

$ cat package.json
{
  "dependencies": {
    "@angular/cli": "^14.0.2"
  }
}

$ npx ng version

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
$ npx ng new angular-webapi-crud
? Would you like to add Angular routing? Yes
? Which stylesheet format would you like to use? SCSS   [ 
https://sass-lang.com/documentation/syntax#scss                ]
CREATE angular-webapi-crud/README.md (1071 bytes)
CREATE angular-webapi-crud/.editorconfig (274 bytes)
CREATE angular-webapi-crud/.gitignore (548 bytes)
CREATE angular-webapi-crud/angular.json (3161 bytes)
CREATE angular-webapi-crud/package.json (1050 bytes)
CREATE angular-webapi-crud/tsconfig.json (863 bytes)
CREATE angular-webapi-crud/.browserslistrc (600 bytes)
CREATE angular-webapi-crud/karma.conf.js (1436 bytes)
CREATE angular-webapi-crud/tsconfig.app.json (287 bytes)
CREATE angular-webapi-crud/tsconfig.spec.json (333 bytes)
CREATE angular-webapi-crud/.vscode/extensions.json (130 bytes)
CREATE angular-webapi-crud/.vscode/launch.json (474 bytes)
CREATE angular-webapi-crud/.vscode/tasks.json (938 bytes)
CREATE angular-webapi-crud/src/favicon.ico (948 bytes)
CREATE angular-webapi-crud/src/index.html (303 bytes)
CREATE angular-webapi-crud/src/main.ts (372 bytes)
CREATE angular-webapi-crud/src/polyfills.ts (2338 bytes)
CREATE angular-webapi-crud/src/styles.scss (80 bytes)
CREATE angular-webapi-crud/src/test.ts (749 bytes)
CREATE angular-webapi-crud/src/assets/.gitkeep (0 bytes)
CREATE angular-webapi-crud/src/environments/environment.prod.ts (51 bytes)
CREATE angular-webapi-crud/src/environments/environment.ts (658 bytes)
CREATE angular-webapi-crud/src/app/app-routing.module.ts (245 bytes)
CREATE angular-webapi-crud/src/app/app.module.ts (393 bytes)
CREATE angular-webapi-crud/src/app/app.component.scss (0 bytes)
CREATE angular-webapi-crud/src/app/app.component.html (23364 bytes)
CREATE angular-webapi-crud/src/app/app.component.spec.ts (1112 bytes)
CREATE angular-webapi-crud/src/app/app.component.ts (224 bytes)
✔ Packages installed successfully.
    Successfully initialized git.

$ cd angular-webapi-curd

$ cat package.json 
{
  "name": "angular-webapi-crud",
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

執行由 CLI 産生的預設程式架構
```bash
$ npm start

> angular-webapi-crud@0.0.0 start
> ng serve

✔ Browser application bundle generation complete.

Initial Chunk Files   | Names         |  Raw Size
vendor.js             | vendor        |   2.02 MB | 
polyfills.js          | polyfills     | 315.29 kB | 
styles.css, styles.js | styles        | 207.83 kB | 
main.js               | main          |  50.14 kB | 
runtime.js            | runtime       |   6.54 kB | 

                      | Initial Total |   2.58 MB

Build at: 2022-07-11T09:47:38.380Z - Hash: 1a9fa50dab468973 - Time: 10661ms

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **


✔ Compiled successfully.
```
開啟瀏覽器，輸入 http://localhost:4200/

![2022-06-15 11-26-34](https://user-images.githubusercontent.com/21993717/173730523-94d3c19e-0982-42e1-878a-438f53d558b6.png)


```bash
$ ls -al
總用量 868
drwxrwxr-x   7 egs egs   4096  六  15 11:24 .
drwxrwxr-x   4 egs egs   4096  六  15 11:21 ..
drwxrwxr-x   3 egs egs   4096  六  15 11:24 .angular
-rw-rw-r--   1 egs egs   3146  六  15 11:21 angular.json
-rw-rw-r--   1 egs egs    600  六  15 11:21 .browserslistrc
-rw-rw-r--   1 egs egs    274  六  15 11:21 .editorconfig
drwxrwxr-x   8 egs egs   4096  六  15 11:22 .git
-rw-rw-r--   1 egs egs    548  六  15 11:21 .gitignore
-rw-rw-r--   1 egs egs   1433  六  15 11:21 karma.conf.js
drwxrwxr-x 596 egs egs  20480  六  15 11:24 node_modules
-rw-rw-r--   1 egs egs   1047  六  15 11:21 package.json
-rw-rw-r--   1 egs egs 800093  六  15 11:22 package-lock.json
-rw-rw-r--   1 egs egs   1068  六  15 11:21 README.md
drwxrwxr-x   5 egs egs   4096  六  15 11:21 src
-rw-rw-r--   1 egs egs    287  六  15 11:21 tsconfig.app.json
-rw-rw-r--   1 egs egs    863  六  15 11:21 tsconfig.json
-rw-rw-r--   1 egs egs    333  六  15 11:21 tsconfig.spec.json
drwxrwxr-x   2 egs egs   4096  六  15 11:21 .vscode
```

## 設置專案

### 建立相關元件 與 服務
```bash
$ ng g s shared/services/auth # 新增 auth service
$ ng g s shared/services/token-storage # 新增 token-storage service
$ ng g s shared/services/todo # 新增 todo service
$ ng g class shared/models/todo --type=model

$ ng g interceptor shared/interceptor/auth　# 新增 auth interceptor

$ ng g c pages/login
$ ng g c pages/register
$ ng g c pages/profile
$ ng g c pages/home
$ ng g c pages/add-todo
$ ng g c pages/todo-detail
$ ng g c pages/todo-list

$ ng g guard shared/guard/auth --implements="CanActivate" # 新增 auth guard
$ ng g guard shared/guard/role --implements="CanActivate" # 新增 role guard
```

### 檔案結構

![image](https://user-images.githubusercontent.com/21993717/178239605-efc1c996-5d35-4e7b-bd7b-cba7c1db6571.png)



## 定義 Ｍodel Class
主要會用到的 Model Class 是 Todo。shared/models/todo.model.ts
```ts
export class Todo {
  id?: any;
  title?: string;
  description?: string;
  status?: boolean;
}
```

## 撰寫 Data Services 程式

打開 vscode
```bash
$ code .　# 打開 vscode
```

### 將程式中會使用的參數值放在 appSettings.ts 中
在開始新增 Services 程式前，我們可以考慮將固定的參數值放在 appSettings.ts 中，來增加一些彈性。在 shared目錄下新增一個　helpers 的子目錄，並新增一支 appSettings.ts 程式，內容如下。設置一個靜態常數 API_URL，並把它 export 出來，讓外面的程式可以引用它。
```ts
export class AppSettings {
    static API_URL = 'http://localhost:5000/api/';
}
```

### 註冊與登入使用的服務程式 auth.service.ts
註冊與登入功能須搭配後端 API，可參考　*[使用 Node.js + express + MariaDB 建立一個後端服務 REST API](https://calvinegs.github.io/posts/nodejs-restapi-mariadb/)*

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { AppSettings } from '../helpers/appSettings';

const API_URL = AppSettings.API_URL + 'auth/';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  constructor( private http: HttpClient) { }

  login(username: string, password: string): Observable<any> {
    return this.http.post(API_URL + 'signin', {
      username,
      password
    })
  }
  
  register(username: string, email:string, password: string): Observable<any> {
    return this.http.post(API_URL + 'signup', {
      username,
      email,
      password
    })
  }
}
```

### Token 相關的服務程式 token-storage.service.ts
在本範例中，我們會把由 API 回來回來的 Token 儲存在 local 瀏覽器中的 Session Local Storage 中。
```ts
import { Injectable } from '@angular/core';

const TOKEN_KEY = 'auth-token';
const USER_KEY = 'auth-user';

@Injectable({
  providedIn: 'root'
})
export class TokenStorageService {

  constructor() { }

  signOut(): void {
    window.sessionStorage.clear();
  }

  public saveToken(token: string): void {
    sessionStorage.removeItem(TOKEN_KEY);
    sessionStorage.setItem(TOKEN_KEY, token);
  }
  
  public getToken(): string | null {
    return sessionStorage.getItem(TOKEN_KEY);
  }
  
  public saveUser(user: any): void {
    sessionStorage.removeItem(USER_KEY);
    sessionStorage.setItem(USER_KEY, JSON.stringify(user));
  }
  
  public getUser(): any {
    const user = sessionStorage.getItem(USER_KEY);
    if (user) {
      return JSON.parse(user);
    }
    return user;
  }
}
```

### Todo 相關的服務程式 todo.service.ts
todo service 是藉由 後端的 Web API 來完成 Todo 資料的新增、修改、刪除、查詢等資料庫功能。

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Todo } from '../models/todo.model';
import { AppSettings } from '../helpers/appSettings';
const API_URL = AppSettings.API_URL + 'todos/';

@Injectable({
  providedIn: 'root'
})
export class TodoService {
  constructor(private http: HttpClient) { }
  getAll(): Observable<Todo[]> {
    return this.http.get<Todo[]>(API_URL);
  }
  get(id: any): Observable<Todo> {
    return this.http.get(`${API_URL}/${id}`);
  }
  create(data: any): Observable<any> {
    return this.http.post(API_URL, data);
  }
  update(id: any, data: any): Observable<any> {
    return this.http.put(`${API_URL}/${id}`, data);
  }
  delete(id: any): Observable<any> {
    return this.http.delete(`${API_URL}/${id}`);
  }
  deleteAll(): Observable<any> {
    return this.http.delete(API_URL);
  }
  findByTitle(title: any): Observable<Todo[]> {
    return this.http.get<Todo[]>(`${API_URL}?title=${title}`);
  }
}
```

## 撰寫 HTTP Interceptor 程式
HTTP Interceptor 可以讓我們很容易的在每一個 request Header 中加入我們登入成功後由 API 回傳回來的｀合法Token`。在這個程式中唯一要注意的是，不應更改原始的 request，而是應該透過 clone 功能來産生一個新的 request 再將 token 寫入這個新 request 的 Header。

```ts
// ./app/shared/interceptor/auth.interceptor.ts
import { Injectable } from '@angular/core';
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor,
  HTTP_INTERCEPTORS
} from '@angular/common/http';
import { Observable } from 'rxjs';
import { TokenStorageService } from '../services/token-storage.service';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {

  constructor(private tokenService: TokenStorageService) {}

  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    let authRequest = request;
    const token = this.tokenService.getToken();
    if (token != null) {
      authRequest = request.clone({
        setHeaders: {
          Authorization: `Bearer ${token}`
        }
      });
    }
    return next.handle(authRequest);
  }
}

export const authInterceptorProviders = [{
  provide: HTTP_INTERCEPTORS,
  useClass: AuthInterceptor,
  multi: true
}]
```

### 將 HTTP Interceptor 加入到 app module 的 providers
上述完成的 Interceptor 要記得`注入`到 app module providers 設定中（第13、32行）。
同時，在前述的 services 中我們使用到了 HttpClient，所以也必須匯入 HttpClient Module（第14、30行）。
```ts {linenos=table,hl_lines=[13,14,30,32]}
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { LoginComponent } from './pages/login/login.component';
import { RegisterComponent } from './pages/register/register.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { HomeComponent } from './pages/home/home.component';
import { AddTodoComponent } from './pages/add-todo/add-todo.component';
import { TodoDetailComponent } from './pages/todo-detail/todo-detail.component';
import { TodoListComponent } from './pages/todo-list/todo-list.component';
import { FormsModule } from '@angular/forms';
import { authInterceptorProviders } from './shared/interceptor/auth.interceptor';

@NgModule({
  declarations: [
    AppComponent,
    LoginComponent,
    RegisterComponent,
    ProfileComponent,
    HomeComponent,
    AddTodoComponent,
    TodoDetailComponent,
    TodoListComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    FormsModule,
    HttpClientModule
  ],
  providers: [authInterceptorProviders],
  bootstrap: [AppComponent]
})
xport class AppModule { }

```

## 新增 components
接著要來建立 components，在此我們使的是 Reactive Form 並使用 bootstrap CSS framework來裝飾我們的 component ，所必須先進行相關設定

### 匯入 ReactiveFormModule 模組

開啟 src/app/app.module.ts 並由 @angular/forms 匯入 ReactiveFormModule 

```ts {linenos=table,hl_lines=[3,17]}
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { ReactiveFormsModule } from '@angular/forms';
import { HttpClientModule } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
//...
@NgModule({
  declarations: [
    AppComponent
    //...
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    ReactiveFormsModule,
    HttpClientModule
  ],
  providers: [authInterceptorProviders],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### 使用 Bootstrap

開啟 src/index.html 在 <head> 加入 link tag
- 使用 bootstrap 5 如第 9 行匯入語法

```html {linenos=table,hl_lines=[9]}
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>AngularAuthJwt</title>
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

### 新增 Register Component
註冊元件的程式碼

```ts
// register.component.ts
import { Component, OnInit } from '@angular/core';
import { AbstractControl, FormBuilder, FormControl, FormGroup, Validators } from '@angular/forms';
import Validation from 'src/app/shared/helpers/validation';
import { AuthService } from 'src/app/shared/services/auth.service';

@Component({
  selector: 'app-register',
  templateUrl: './register.component.html',
  styleUrls: ['./register.component.scss']
})
export class RegisterComponent implements OnInit {
  form: FormGroup = new FormGroup({
    username: new FormControl(''),
    email: new FormControl(''),
    password: new FormControl(''),
    confirmPassword: new FormControl('')
  });
  submitted = false;
  isSuccessful = false;;
  isSignUpFailed = false;
  errorMessage = '';

  constructor(private formBuilder: FormBuilder,
    private authService: AuthService) { }

  ngOnInit(): void {
    this.form = this.formBuilder.group(
      {
        username: [
          '',
          [
            Validators.required,
            Validators.minLength(4),
            Validators.maxLength(20)
          ]
        ],
        email: ['', [Validators.required, Validators.email]],
        password: [
          '',
          [
            Validators.required,
            Validators.minLength(6),
            Validators.maxLength(40)
          ]
        ],
        confirmPassword: ['',Validators.required],
      },
      {
        validators: [Validation.match('password', 'confirmPassword')]
      }
    )
  }

  get f(): { [key: string]: AbstractControl } {
    return this.form.controls;
  }

  onSubmit(): void {
    this.submitted = true;
    if (this.form.invalid) {
      return;
    }
    const { username, email, password } = this.form.getRawValue();
    this.authService.register(username!, email!, password!).subscribe({
      next: (data) => {
        console.log(data);
        this.isSuccessful = true;
        this.isSignUpFailed = false;
      },
      error: (err) => {
        this.isSuccessful = false;
        this.isSignUpFailed = true;
      }
    });
  }

  onReset(): void {
    this.submitted = false;
    this.isSignUpFailed = false;
    this.form.reset();
  }
}
```

在 Register Component 中我們使用了一個 Custom Validator，它用來比對二次輸入的密碼是否一致，程式碼如下：

```ts
// validation.ts
import { AbstractControl, ValidatorFn } from "@angular/forms";

export default class Validation {
    static match(controlName: string, checkControlName: string): ValidatorFn {
        return (controls: AbstractControl) => {
            const control = controls.get(controlName);
            const checkControl = controls.get(checkControlName);
            if (checkControl?.errors && !checkControl.errors['matching']) {
                return null;
            }
            if (control?.value !== checkControl?.value) {
                controls.get(checkControlName)?.setErrors({ matching: true });
                return { match: true };
            } else {
                return null;
            }
        }
    }
}
```

註冊元件的 HTML
```html
<!-- register.component.html -->
<div class="container-fluid register-form">
  <form needs-validation [formGroup]="form" (ngSubmit)="onSubmit()">
    <div class="note">
      <h1> 使用者資料註冊 </h1>
    </div>

    <div class="mb-4">
      <label for="username" class="form-label">Username</label>
      <input type="text" autofocus formControlName="username" id="username" class="form-control" [ngClass]="{ 'is-invalid': submitted && f['username'].errors,
                     'is-valid': submitted && !f['username'].errors }" />
      <div *ngIf="submitted && f['username'].errors" class="invalid-feedback">
        <div *ngIf="f['username'].errors['required']">Username 為必填</div>
        <div *ngIf="f['username'].errors['minlength']">
          Username 必須至少為六個字元
        </div>
        <div *ngIf="f['username'].errors['maxlength']">
          Username 必須至多為二十個字元
        </div>
      </div>
      <div class="valid-feedback">
        填寫正確
      </div>
    </div>

    <div class="mb-4">
      <label for="email" class="form-label">Email</label>
      <input type="text" formControlName="email" id="email" class="form-control" [ngClass]="{ 'is-invalid': submitted && f['email'].errors,
                     'is-valid': submitted && !f['email'].errors }" />
      <div *ngIf="submitted && f['email'].errors" class="invalid-feedback">
        <div *ngIf="f['email'].errors['required']">Email 為必填</div>
        <div *ngIf="f['email'].errors['email']">Email 格式不符</div>
      </div>
      <div class="valid-feedback">
        填寫正確
      </div>
    </div>

    <div class="mb-4">
      <label for="password" class="form-label">Password</label>
      <input type="password" formControlName="password" id="password" class="form-control" [ngClass]="{ 'is-invalid': submitted && f['password'].errors,
                     'is-valid': submitted && !f['password'].errors }" />
      <div *ngIf="submitted && f['password'].errors" class="invalid-feedback">
        <div *ngIf="f['password'].errors['required']">Password 為必填</div>
        <div *ngIf="f['password'].errors['minlength']">
          Password 必須至少為六個字元
        </div>
        <div *ngIf="f['password'].errors['maxlength']">
          Password 必須至多為四十個字元
        </div>
      </div>
      <div class="valid-feedback">
        填寫正確
      </div>
    </div>

    <div class="mb-4">
      <label for="confirmPassword" class="form-label">Confirm Password</label>
      <input type="password" formControlName="confirmPassword" id="confirmPassword" class="form-control" [ngClass]="{ 'is-invalid': submitted && f['confirmPassword'].errors,
                     'is-valid': submitted && !f['confirmPassword'].errors }" />
      <div *ngIf="submitted && f['confirmPassword'].errors" class="invalid-feedback">
        <div *ngIf="f['confirmPassword'].errors['required']">Confirm Password 為必填</div>
        <div *ngIf="f['confirmPassword'].errors['matching']">
          Confirm Password 不符
        </div>
      </div>
      <div class="valid-feedback">
        填寫正確
      </div>
    </div>

    <div class="mb-4">
      <button type="submit" class="btn btn-primary">送出</button>
      <button type="button" (click)="onReset()" class="btn btn-warning float-end">
        重置
      </button>
    </div>
    <div class="alert alert-warning" *ngIf="isSignUpFailed">
      註冊失敗!<br />{{ errorMessage }}
    </div>
    <div class="alert alert-success" *ngIf="isSuccessful">
      註冊成功！
    </div>
  </form>
</div>
```
註冊元件的 CSS
```css
/* register.component.scss */
.register-form {
    max-width: 350px;
    margin: auto;
  }
```

![image](https://user-images.githubusercontent.com/21993717/178406386-86b9fc78-aa8b-49fc-a70c-1c62d0b22b5f.png)


### 新增 Login Component
登入元件的程式碼
```ts
// login.component.ts
import { Component, OnInit } from '@angular/core';
import { AbstractControl, FormBuilder, FormControl, FormGroup, Validators } from '@angular/forms';
import { AuthService } from 'src/app/shared/services/auth.service';
import { TokenStorageService } from 'src/app/shared/services/token-storage.service';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {
  form: FormGroup = new FormGroup({
    username: new FormControl(''),
    password: new FormControl('')    
  });
  isLoggedIn = false;
  isLoginFailed = false;
  submitted = false;
  errorMessage = '';
  roles: string[] = [];

  constructor(private formBuilder: FormBuilder,
    private authService: AuthService,
    private tokenStorage: TokenStorageService) { }

  ngOnInit(): void {
    if (this.tokenStorage.getToken()) {
      this.isLoggedIn = true;
      this.roles = this.tokenStorage.getUser().roles;
    }
    this.form = this.formBuilder.group(
      {
        username: ['',[Validators.required]],
        password: ['',[Validators.required]]
      }
    )
  }

  get f(): { [key: string]: AbstractControl } {
    return this.form.controls;
  }

  onSubmit(): void {
    this.submitted = true;
    if (this.form.invalid) {
      return;
    }
    const { username, password } = this.form.getRawValue();

    this.authService.login(username, password).subscribe({
      next: data => {
        this.tokenStorage.saveToken(data.accessToken);
        this.tokenStorage.saveUser(data);

        this.isLoggedIn = true;
        this.roles = this.tokenStorage.getUser().roles;
        this.roloadPage();
      },
      error: err => {
        this.errorMessage = err.error.message;
        this.isLoginFailed = true;
      }
    })
  }

  roloadPage(): void {
    this.submitted = false;
    location.reload();
  }

  onReset(): void {
    this.submitted = false;
    this.isLoginFailed = false;
    this.form.reset();
  }
}
```
登入元件的 HTML
```html
<!-- login.component.html -->
<div class="container-fluid register-form">
    <form 
      *ngIf="!isLoggedIn"
      needs-validation [formGroup]="form" (ngSubmit)="onSubmit()">
      <div class="note">  
        <h1> 使用者登入 </h1>  
      </div>  
      
      <div class="mb-4">
        <label for="username" class="form-label">Username</label>
        <input type="text" autofocus formControlName="username" id="username" class="form-control"
        [ngClass]="{ 'is-invalid': submitted && f['username'].errors,
                     'is-valid': submitted && !f['username'].errors }" 
        />
        <div *ngIf="submitted && f['username'].errors" class="invalid-feedback">
          <div *ngIf="f['username'].errors['required']">Username 為必填</div>
        </div>
        <div class="valid-feedback">
          填寫正確
        </div>
      </div>
  
      <div class="mb-4">
        <label for="password" class="form-label">Password</label>
        <input type="password" formControlName="password" id="password" class="form-control"
        [ngClass]="{ 'is-invalid': submitted && f['password'].errors,
                     'is-valid': submitted && !f['password'].errors }" 
        />
        <div *ngIf="submitted && f['password'].errors" class="invalid-feedback">
          <div *ngIf="f['password'].errors['required']">Password 為必填</div>
        </div>
        <div class="valid-feedback">
          填寫正確
        </div>
      </div>

      <div class="mb-4">
        <button type="submit" class="btn btn-primary">送出</button>
        <button type="button" (click)="onReset()"
        class="btn btn-warning float-end">
          重置
        </button>
      </div>
      <div class="alert alert-warning" *ngIf="isLoginFailed">
        Signin failed!<br />{{ errorMessage }}
    </div>
    </form>
    <div class="alert alert-success" *ngIf="isLoggedIn">
      登入者具備的角色 {{ roles }}.
    </div>
  </div>
```

登入元件的 CSS
```css
/* login.component.scss */
.login-form {
    max-width: 350px;
    margin: auto;
  }
```

![image](https://user-images.githubusercontent.com/21993717/178406456-9e90b742-dfa5-4e53-9da3-3402bd319324.png)


### 新增 Profile Component

個人檔案的程式碼

```ts
// profile.component.ts
import { Component, OnInit } from '@angular/core';
import { TokenStorageService } from 'src/app/shared/services/token-storage.service';

@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.scss']
})
export class ProfileComponent implements OnInit {
  currentUser: any;

  constructor(private token: TokenStorageService) { }

  ngOnInit(): void {
    this.currentUser = this.token.getUser();
  }
}
```

個人檔案的 HTML

```html
<!-- profile.component.html -->
<div class="container" *ngIf="currentUser; else loggedOut">
    <header class="jumbotron">
        <h3><strong>{{ currentUser.username }}</strong> 個人訊息</h3>
    </header>
    <p><strong>Token:</strong>
        {{ currentUser.accessToken.substring(0, 20)}} ...
        {{ currentUser.accessToken.substr( currentUser.accessToken.length - 20)}}
    </p>
    <p><strong>電子信箱:</strong>
        {{ currentUser.email }}
    </p>
    <strong>參與的角色:</strong>
    <ul>
        <li *ngFor="let role of currentUser.roles">
            {{ role }}
        </li>
    </ul>
</div>

<ng-template #loggedOut>
    請先登入
</ng-template>
```

![image](https://user-images.githubusercontent.com/21993717/178406549-a411becf-a2f7-4e45-a856-3a0afcc75558.png)

## 路由設置
透過路由的設置，程式才會知道當使用者在瀏覧器特定的 URI 時，程式應該去執那支相對應的程式。路由模組檔案（app-routin.module.ts）內容如下：

```ts {linenos=table,hl_lines=[11-20]}
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AddTodoComponent } from './pages/add-todo/add-todo.component';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';
import { TodoDetailComponent } from './pages/todo-detail/todo-detail.component';
import { TodoListComponent } from './pages/todo-list/todo-list.component';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  { path: 'profile', component: ProfileComponent },
  { path: 'add-todo', component: AddTodoComponent },
  { path: 'todo-detail', component: TodoDetailComponent },
  { path: 'todo-list', component: TodoListComponent },
  { path: '', redirectTo: 'home', pathMatch: 'full' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## App Module 的設置
```ts
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { LoginComponent } from './pages/login/login.component';
import { RegisterComponent } from './pages/register/register.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { HomeComponent } from './pages/home/home.component';
import { AddTodoComponent } from './pages/add-todo/add-todo.component';
import { TodoDetailComponent } from './pages/todo-detail/todo-detail.component';
import { TodoListComponent } from './pages/todo-list/todo-list.component';
import { authInterceptorProviders } from './shared/interceptor/auth.interceptor';
import { HttpClientModule } from '@angular/common/http';
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  declarations: [
    AppComponent,
    LoginComponent,
    RegisterComponent,
    ProfileComponent,
    HomeComponent,
    AddTodoComponent,
    TodoDetailComponent,
    TodoListComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    ReactiveFormsModule,
    HttpClientModule
  ],
  providers: [authInterceptorProviders],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## App Component 修改
```ts
// app.component.ts
import { Component } from '@angular/core';
import { TokenStorageService } from './shared/services/token-storage.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  private roles: string[] = []; //使用者具備的角色
  isLoggedIn = false; //用來記錄是否已登入系統的變數
  showAdminFunction = false;  //記錄是否有｀管理者｀角色的變數
  username?: string;

  constructor(private tokenStorageService: TokenStorageService) { }

  ngOnInit(): void {
    // 若在 session storage 中有有效的 token 則表示已有正常的登入系統
    this.isLoggedIn = !!this.tokenStorageService.getToken();

    if(this.isLoggedIn) {
      const user = this.tokenStorageService.getUser();
      this.roles = user.roles;
      this.showAdminFunction = this.roles.includes("ADMIN");
      this.username = user.username;
    }
  }

  logout(): void {
    this.tokenStorageService.signOut(); //清除 session storage
    location.reload();
  }
}
```

```html
<!-- app.component.html -->
<div class="app">
  <nav class="navbar navbar-expand navbar-dark bg-dark">
    <div class="container-fluid">
      <a href="#" class="navbar-brand">Angular curd example</a>
      <ul class="navbar-nav me-auto" routerLinkActive="active">
        <li class="nav-item">
          <a href="/home" class="nav-link" routerLink="home">首頁</a>
        </li>
        <li class="nav-item">
          <a href="/todo-list" class="nav-link" *ngIf="isLoggedIn" routerLink="todo-list">Todo</a>
        </li>        
        <li class="nav-item" *ngIf="showAdminFunction">
          <a href="/add-todo" class="nav-link" routerLink="add-todo">新增</a>
        </li>
      </ul>
      <ul class="navbar-nav ms-auto" *ngIf="!isLoggedIn">
        <li class="nav-item">
          <a href="/register" class="nav-link" routerLink="register">註冊</a>
        </li>
        <li class="nav-item">
          <a href="/login" class="nav-link" routerLink="login">登入</a>
        </li>
      </ul>

      <ul class="navbar-nav ms-auto" *ngIf="isLoggedIn">
        <li class="nav-item">
          <a href="/profile" class="nav-link" routerLink="profile">{{ username }}</a>
        </li>
        <li class="nav-item">
          <a href class="nav-link" (click)="logout()">登出</a>
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
1. Home Page

首頁為預設的 Page，同時它也是一供公開的功能，沒有使用權限的限定，所以即使你未登入，也可以瀏覽此網頁。

![image](https://user-images.githubusercontent.com/21993717/178406883-01e088b6-1d37-47cc-be7e-09406295666e.png)


2. 一般使用者登入後的畫面

以已註冊的使用者登入系統後，可以發現主頁面上的功能選項多出了 “Todo” 與 "tomchen" 兩項

![image](https://user-images.githubusercontent.com/21993717/178407057-d5c9f83c-28c6-423f-94d2-e9eb25b1f58f.png)

其中 ｀tomchen｀ 指的是現在已登入者的 username (登入帳號)，同時點進這功能會顯示使用者的明細資料。
![image](https://user-images.githubusercontent.com/21993717/178407485-e7f88788-1d8d-4590-80f1-392d24076330.png)

而｀Todo｀這個功能項目也是隨著使用者登入成功後才顯示出來的。


3. 管理角色使用者登入後的畫面

下圖是以｀管理者｀角色登入後所顯示的畫面。而因為這個使用者具備有管理者角色（admin)，所以功能選項同時出現了 ｀Add｀這個項目。
![image](https://user-images.githubusercontent.com/21993717/178407771-1b2ca047-7059-47c2-b39f-add7b467d691.png)

## Route Guard (路由保護器)

由上述的測試，發現一個問題，當使用一般 User 帳號登時時，雖然在畫面上方的功能選項中沒有出現 "新增" 的功能選項，但你卻可以直接在瀏覽器的網址列輸入 `http://localhost:4200/add-todo` 來執行 Todo 的功能。這個路由所對應到的程式是可以被執行的。

![image](https://user-images.githubusercontent.com/21993717/178439109-f9541482-3e96-4b32-bc33-a81f0e51b6b9.png)

還有一個更嚴重的情境，在未登入的狀態下，你也可以直接在瀏覽器的網址列輸入 `http://localhost:4200/add-todo` 來執行 Todo 的功能。

![image](https://user-images.githubusercontent.com/21993717/178443507-5c4026eb-62ec-4224-b7d1-37aed9a29612.png)

由而看起來，我們的權限控制只做了半套。那麼應該要如何來解決這樣的問題呢？

### 帳號認證攔截器

首先介紹的　路由保護器(攔截器)　是一種 local 端保護機制。透過 Route Guard 的設置可以在使用者端就能效管理各個 route 的執行權限的控管。

authGuard 這個路由保護器的功能就是來攔截沒有正常登入系統的使用者不得執行特定的路由。在程式中透過 tokenService.getToken() 功能來讀取 token，若 session storeage 中沒有合法 token，表示目前還沒有正確的登入系統，並將程式指向登入功能。

```ts
// auth.guard.ts
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';
import { TokenStorageService } from '../services/token-storage.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(private router: Router,
    private tokenService: TokenStorageService) {}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
      if (this.tokenService.getToken()) return true;
      this.router.navigate(['/login']);
      return true;
  }  
}
```

app-routing.module.ts 中的路由設定，將 AuthGurad 加入到要保護的 路由設定　中。
如下程式第18行`{ path: 'add-todo', canActivate:[AuthGuard], component: AddTodoComponent },`，其中的　`canActivate:[AuthGuard]` 就是幫這個路由設定加入了 ‘AuthGuard’ 這個攔截器來起到保護這個路由的作用。

```ts {linenos=table,hl_lines=[11,18-20]}
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AddTodoComponent } from './pages/add-todo/add-todo.component';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';
import { TodoDetailComponent } from './pages/todo-detail/todo-detail.component';
import { TodoListComponent } from './pages/todo-list/todo-list.component';
import { AuthGuard } from './shared/guard/auth.guard';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  { path: 'profile', component: ProfileComponent },
  { path: 'add-todo', canActivate:[AuthGuard], component: AddTodoComponent },
  { path: 'todo-detail', canActivate:[AuthGuard], component: TodoDetailComponent },
  { path: 'todo-list', canActivate:[AuthGuard], component: TodoListComponent },
  { path: '', redirectTo: 'home', pathMatch: 'full' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

完成上述的程式後，我們再來測試一下“未登入而直接在瀏覽器的網址列輸入 `http://localhost:4200/add-todo` 這個情境。你會發現輸入 `http://localhost:4200/add-todo`再按下 enter 鍵後，系統會檢核出尚未登入而自動轉址到`使用者登入`功能中。


### 角色授權攔截器

接下來要介紹roleGuard 這個路由保護器的功能就是來攔截現行的使用者是否具備有特定的角色，若沒有指定的角色不得執行特定的路由。並顯示目前使有者所具備的角色有那些。

```ts
// role.guard.ts
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';
import { TokenStorageService } from '../services/token-storage.service';

@Injectable({
  providedIn: 'root'
})
export class RoleGuard implements CanActivate {
  constructor(private router: Router, private tokenService: TokenStorageService) {};
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

      const roles = this.tokenService.getUser().roles;

      const rolesPara = route.data["roles"] as Array<string>;
      let searchRole = 'ADMIN';
      if (rolesPara) {
        searchRole = rolesPara[0].toUpperCase();
      }
      
      if (roles) {
        const rolesArray = roles as Array<string>
        if (rolesArray.indexOf(searchRole) != -1) return true;
      };

      this.router.navigate(['/login']);
      return true;
  }
}
```
將 RoleGurad 加入到要保護的 路由設定　中。
```ts {linenos=table,hl_lines=[12,19]}
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AddTodoComponent } from './pages/add-todo/add-todo.component';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';
import { TodoDetailComponent } from './pages/todo-detail/todo-detail.component';
import { TodoListComponent } from './pages/todo-list/todo-list.component';
import { AuthGuard } from './shared/guard/auth.guard';
import { RoleGuard } from './shared/guard/role.guard';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  { path: 'profile', component: ProfileComponent },
  { path: 'add-todo', canActivate:[AuthGuard, RoleGuard], component: AddTodoComponent },
  { path: 'todo-detail', canActivate:[AuthGuard], component: TodoDetailComponent },
  { path: 'todo-list', canActivate:[AuthGuard], component: TodoListComponent },
  { path: '', redirectTo: 'home', pathMatch: 'full' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```
完成上述功能後，就只有具備有“ADMIN”角色者才能執行“add-todo”功能了。

## 完成 Todo 相關 Component 的功能
程式至止算是完成了“基礎“的部份，接下來要回歸本篇筆記的主題 - Todo list 的資料維護。包含有“新增 Todo Item"、“顯示 Todo Item 清單”、"Todo 明細"等 Component。

### 新增 Todo Item 的 Component

新增 Todo Item 時透過　TodoService.create() function來完成。

```ts
// add-todo.component.ts
import { Component, OnInit, ValueProvider } from '@angular/core';
import { AbstractControl, FormBuilder, FormControl, FormGroup, Validators } from '@angular/forms';
import { Router } from '@angular/router';
import { Todo } from 'src/app/shared/models/todo.model';
import { TodoService } from 'src/app/shared/services/todo.service';

@Component({
  selector: 'app-add-todo',
  templateUrl: './add-todo.component.html',
  styleUrls: ['./add-todo.component.scss']
})
export class AddTodoComponent implements OnInit {
  form: FormGroup = new FormGroup({
    title: new FormControl(''),
    description: new FormControl('')
  });

  submitted = false;  // 送出否
  isAddFailed = false;  // 新增有誤否
  isAdded = false;  // 新增成功否
  errorMessage = "";

  constructor(private formBuilder: FormBuilder,
    private router: Router,
    private todoService: TodoService) { }

  ngOnInit(): void {
    this.form = this.formBuilder.group(
      {
        title: ['',[Validators.required]],
        description: ['',[Validators.required]]
      }
    )}

  onSubmit(): void {
    this.submitted = true;
    if (this.form.invalid) {
      return;
    }
    const data = this.form.getRawValue();
    this.todoService.create(data)
      .subscribe({
        next: (res) => {
          this.isAdded = true;
          this.router.navigate(['/todolist']);
        },
        error: (e) => {
          this.errorMessage = e.message
          this.isAddFailed = true;
        }
      });
  }

  get f(): { [key: string]: AbstractControl } {
    return this.form.controls;
  }

  onReset(): void {
    this.submitted = false;
    this.isAddFailed = false;
    this.form.reset();
  }
}
```

```html
<!-- add-todo.component.html -->
<div class="container-fluid submit-form">
    <form *ngIf="!isAdded" needs-validation [formGroup]="form" (ngSubmit)="onSubmit()">
        <div class="note">
            <h2> 新增 Todo </h2>
        </div>

        <div class="mb-4">
            <label for="title" class="form-label">Title</label>
            <input type="text" autofocus formControlName="title" id="title" class="form-control" [ngClass]="{ 'is-invalid': submitted && f['title'].errors,
                     'is-valid': submitted && !f['title'].errors }" />
            <div *ngIf="submitted && f['title'].errors" class="invalid-feedback">
                <div *ngIf="f['title'].errors['required']">Title 為必填</div>
            </div>
            <div class="valid-feedback">
                填寫正確
            </div>
        </div>

        <div class="mb-4">
            <label for="description" class="form-label">Description</label>
            <input type="description" formControlName="description" id="description" class="form-control" [ngClass]="{ 'is-invalid': submitted && f['description'].errors,
                     'is-valid': submitted && !f['description'].errors }" />
            <div *ngIf="submitted && f['description'].errors" class="invalid-feedback">
                <div *ngIf="f['description'].errors['required']">Description 為必填</div>
            </div>
            <div class="valid-feedback">
                填寫正確
            </div>
        </div>

        <div class="mb-4">
            <button type="submit" class="btn btn-primary">送出</button>
            <button type="button" (click)="onReset()" class="btn btn-warning float-end">
                重置
            </button>
        </div>
        <div class="alert alert-warning" *ngIf="isAddFailed">
            新增有誤!<br />{{ errorMessage }}
        </div>
    </form>
    <div class="alert alert-success" *ngIf="isAdded">
        新增成功!
    </div>
</div>
```

```css
/* add-todo.scss */
.submit-form {
    max-width: 400px;
    margin: auto;
  }
```

### Todo Item 清單 的 Component
```ts
// todo-list.component.ts
import { Component, OnInit } from '@angular/core';
import { VirtualTimeScheduler } from 'rxjs';
import { Todo } from 'src/app/shared/models/todo.model';
import { TodoService } from 'src/app/shared/services/todo.service';

@Component({
  selector: 'app-todo-list',
  templateUrl: './todo-list.component.html',
  styleUrls: ['./todo-list.component.scss']
})
export class TodoListComponent implements OnInit {
  todos?: Todo[];
  currentTodo: Todo = {};
  currentIndex = -1;
  title='';

  constructor(private todoService: TodoService) { }

  ngOnInit(): void {
    this.retrieveTodos();
  }

  // 取得所有的 Todo Lists
  retrieveTodos(): void {
    this.todoService.getAll()
      .subscribe({
        next: (data) => {
          this.todos = data;
        },
        error: (e) => console.log(e)
      });
  }

  // 重新取得所有的 Todo Lists
  refreshList(): void {
    this.retrieveTodos();
    this.currentTodo = {};
    this.currentIndex = -1
  }

  // 設定現行 Todo
  setActiveTodo(todo: Todo, index: number): void {
    this.currentTodo = todo;
    this.currentIndex = index;
  }

  // 移除所有 Todo Lists
  removeAllTodos(): void {
    this.todoService.deleteAll()
      .subscribe({
        next: (res) => {
          this.refreshList();
        },
        error: (e) => console.log(e)
      });
  }

  // 以關鍵字查詢標題欄以取得 Todo Lists
  searchTitle(): void {
    this.currentTodo = {};
    this.currentIndex = -1;
    this.todoService.findByTitle(this.title)
      .subscribe({
        next: (data) => {
          this.todos = data;
          console.log(data);
        },
        error: (e) => console.log(e)
      });
  }
}
```

```html
<!-- todo-list.component.html -->
<div class="list row">
    <h2>Todo 資料維護</h2>
    <!-- 以關鍵字查詢標題欄以取得 Todo Lists -->
    <div class="col-md-8">
        <div class="input-group mb-3">
            <input type="text" class="form-control" placeholder="Search by title" [(ngModel)]="title" />
            <div class="input-group-append">
                <button class="btn btn-outline-secondary" type="button" (click)="searchTitle()">
                    搜尋
                </button>
            </div>
        </div>
    </div>
    <!-- 顯示符合的 Todo 清單 -->
    <div class="col-md-6">
        <h4>Todo 清單</h4>
        <ul class="list-group">
            <li class="list-group-item" *ngFor="let todo of todos; let i = index" [class.active]="i == currentIndex"
                (click)="setActiveTodo(todo, i)">
                {{ todo.title }}
            </li>
        </ul>
        <!-- 刪除所有 Todo -->
        <button class="m-3 btn btn-sm btn-danger" (click)="removeAllTodos()">
            刪除所有
        </button>
    </div>
    <!-- 將"Todo 明細"子元件篏入到 Todo List 元件中 -->
    <div class="col-md-6">
        <app-todo-detail [viewMode]="false" [currentTodo]="currentTodo" (refreshProcess)="refreshList()"></app-todo-detail>
    </div>
</div>
```

```css
/* todo-list.component.scss */
.list {
    text-align: left;
    max-width: 750px;
    margin: auto;
  }
```

### Todo Item 明細 的 Component
```ts
// todo-detail.component.ts
import { Component, EventEmitter, Input, OnInit, Output } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { Todo } from 'src/app/shared/models/todo.model';
import { TodoService } from 'src/app/shared/services/todo.service';

@Component({
  selector: 'app-todo-detail',
  templateUrl: './todo-detail.component.html',
  styleUrls: ['./todo-detail.component.scss']
})
export class TodoDetailComponent implements OnInit {
  @Input() viewMode = false;
  @Input() currentTodo: Todo = {
    title: '',
    description: '',
    status: false,
  }
  @Output() refreshProcess = new EventEmitter()

  message = '';
  showBackto = 0;
  constructor(private todoService: TodoService,
    private route: ActivatedRoute,
    private router: Router) { }

  ngOnInit(): void {
    if (!this.viewMode) {
      this.message = '';
      this.getTodo(this.route.snapshot.params["id"]);
    }
    this.route.queryParams.subscribe(params => {
      this.showBackto = params['showbackto'];
    });
  }
  
  // 取得 Todo 明細內容
  getTodo(id: string): void {
    this.todoService.get(id)
      .subscribe({
        next: (data) => {
          this.currentTodo = data;
          // console.log(data);
        },
        error: (e) => console.log(e)
      });
  }

  // 更新 Todo 狀態
  updateStatus(status: boolean): void {
    const data = {
      title: this.currentTodo.title,
      description: this.currentTodo.description,
      status: status,
    };
    this.message = '';
    this.todoService.update(this.currentTodo.id, data)
      .subscribe({
        next: (res) => {
          console.log(res);
          this.currentTodo.status = status;
          this.message = res.message ? res.message : '狀態更新成功！';
        },
        error: (e) => console.log(e)
      });
  }

  // 修改 Todo 明細
  updateTodo(): void {
    this.message = '';
    this.todoService.update(this.currentTodo.id, this.currentTodo)
      .subscribe({
        next: (res) => {
          console.log(res);
          this.message = res.message ? res.message : 'todo 更新成功！';
        },
        error: (e) => console.log(e)
      });
  }

  // 刪除 Todo 明細
  deleteTodo(): void {
    this.todoService.delete(this.currentTodo.id)
      .subscribe({
        next: (res) => {
          console.log(res);
          this.router.navigate(['/todolist']);
          this.refreshProcess.emit();
        },
        error: (e) => console.log(e)
      });
  }

  backtoTodo(): void {
    this.router.navigate(['/todolist']);
  }
}
```

```html
<!-- doto-detail.component.html -->
<div *ngIf="viewMode; else editable">
    <!-- 顯示 Todo 明細內容 -->
    <div *ngIf="currentTodo.id">
      <h4>Todo</h4>
      <div>
        <label><strong>Title:</strong></label> {{ currentTodo.title }}
      </div>
      <div>
        <label><strong>Description:</strong></label>
        {{ currentTodo.description }}
      </div>
      <div>
        <label><strong>Status:</strong></label>
        {{ currentTodo.status ? "Done" : "Pending" }}
      </div>
      <!-- 切換至編輯畫面 -->
      <a [routerLink]="['/tododetail/' + currentTodo.id]" [queryParams]="{showbackto: 1}">
        Edit
      </a>
    </div>
    <div *ngIf="!currentTodo">
      <br />
      <p>請點選 Todo...</p>
    </div>
  </div>
  <!-- Todo 明細編輯畫面 -->
  <ng-template #editable>
    <div *ngIf="currentTodo.id" class="edit-form">
      <h4>Todo</h4>
      <form>
        <div class="mb-4">
          <label for="title" class="form-label">Title</label>
          <input
            type="text"
            class="form-control"
            id="title"
            [(ngModel)]="currentTodo.title"
            name="title"
          />
        </div>
        <div class="mb-4">
          <label for="description" class="form-label">Description</label>
          <input
            type="text"
            class="form-control"
            id="description"
            [(ngModel)]="currentTodo.description"
            name="description"
          />
        </div>
        <div class="mb-4">
          <label><strong>Status:</strong></label>
          {{ currentTodo.status ? "Done" : "Pending" }}
        </div>
      </form>
      <!-- 將狀態更新成 Pending -->
      <button
        class="btn btn-primary"
        *ngIf="currentTodo.status"
        (click)="updateStatus(false)">
        Pending
      </button>
      <!-- 將狀態更新成 `完成` -->
      <button
        *ngIf="!currentTodo.status"
        class="btn btn-primary"
        (click)="updateStatus(true)">
        Done
      </button>
      <!-- 刪除 Todo  -->
      <button class="btn btn-danger" (click)="deleteTodo()">
        Delete
      </button>
      <!-- 更新明細內容 -->
      <button
        type="submit"
        class="btn btn-warning"
        (click)="updateTodo()">
        Update
      </button>
      <button
        *ngIf="showBackto == 1"
        class="btn btn-primary float-end"
        (click)="backtoTodo()">
        Back
      </button>
      <!-- 顯示操作後訊息 -->
      <p>{{ message }}</p>
    </div>
    <div *ngIf="!currentTodo.id">
      <br />
      <p>Cannot access this Todo...</p>
    </div>
  </ng-template>
```

```css
/* todo-detail.component.scss */
.edit-form {
    max-width: 400px;
    margin: auto;
  }
```
## 執行 Todo 功能測試

### 新增 Todo Item

新增 Todo 的功能畫面

![image](https://user-images.githubusercontent.com/21993717/178966335-f0166f01-9b0f-4e3a-89b2-0401383403d6.png)

輸入資料後按下 Enter 

![image](https://user-images.githubusercontent.com/21993717/179132961-6c87ddb8-6595-4a29-b79b-fc699a6680e7.png)

自動跳到 Todo 維護功能中

![image](https://user-images.githubusercontent.com/21993717/179132996-f39a4935-a5b6-497d-a86e-6c7cd479601b.png)

資料檢核，若資料欄位未輸入時送出前會有警語出現

![image](https://user-images.githubusercontent.com/21993717/179132775-44bd464d-ea5b-4282-b146-30d6082d8e29.png)

### Todo 資料維護

![image](https://user-images.githubusercontent.com/21993717/178967556-5fdb0387-ce33-4327-81d7-fdb8ac7b6fda.png)

 1. Title 欄位的關鍵字查詢
 2. Todo 清單顯示區，可透過滑鼠點選來顯示詳細資料（顯示在 3. Todo 詳細區）
 3. Todo 詳細區，除顯示Todo 詳細欄位資訊外，也提供了維護的相關按鈕，如：Done/Pending 按鈕是用來切換`狀態欄`資料、Delete 按鈕用來刪除現行的這筆 Todo、Update按鈕則是更新資料用。
 4. ｀刪除所有｀按鈕會清除所有 Todo Item


## Custom Directive

在前述的程式中，我們在不同角色使用登入時會自動控制畫面上方的功能列，只有具有Admin角色的使用者登入時才會出現。若這個需求也出現在 Todo 維護功能中，譬如： `刪除所有` 以及 `Delete` 兩個功能，也希望只有具有Admin角色的使用者才能操作。

![image](https://user-images.githubusercontent.com/21993717/178965936-35b5a23b-72c5-4a9e-b90b-b34af3537427.png)


![image](https://user-images.githubusercontent.com/21993717/178966102-80dc5d06-984b-4f18-b64a-557acb76dfff.png)

控制`新增`功能的程式碼是在 app.component.ts 以及 app.component.html 中
![image](https://user-images.githubusercontent.com/21993717/179139034-d61f42e5-cc5f-4774-bbaf-b71b2276f936.png)

![image](https://user-images.githubusercontent.com/21993717/179140218-2782a112-11fa-4ecd-8c5d-9338fed01caa.png)

很顯然的必須把這些程式碼複制貼上到 TodoList 及 TodoDetail 二個元件中。當然我們會思考如何避免程式重覆及程式公用的問題。上述的需求可利用 Angular Directive 的功能來完美解決。

首先一樣先透過 ng Cli 來建立程式框架
```bash
$ ng g d shared/directive/if
```
再打開 if.directive.ts 輸入以下程式碼：

```ts
import { Directive, TemplateRef, ViewContainerRef } from '@angular/core';
import { TokenStorageService } from '../services/token-storage.service';

@Directive({
  selector: '[csdIf]'
})
export class IfDirective {
  private roles: string[] = []; //使用者具備的角色
  constructor(private templateRef: TemplateRef<unknown>,
    private vcr: ViewContainerRef,
    private tokenStorageService: TokenStorageService) { }

  ngOnInit(): void {
    this.displayTemplate();
  }

  private displayTemplate() {
    this.vcr.clear();

    const user = this.tokenStorageService.getUser();
    this.roles = user.roles;

    if (this.roles.includes("ADMIN")) {
      this.vcr.createEmbeddedView(this.templateRef);
    } else {
      this.vcr.clear();
    }
  }
}
```
將這個 `自定的 Directive` 宣告在 app.module.ts 中
![image](https://user-images.githubusercontent.com/21993717/179142322-b98bbd06-6643-4a2d-8cc1-9ffcfb48ec19.png)

完成後，分別
1. 將 app.components.ts 原來的程式碼移除
2. 將 app.component.html 中的 `*ngIf="showAdminFunction"` 改成 `*csdIf`
3. 將 todo-list.component.html 中的`刪除所有` button 加入 `*csdIf`　屬性
4. 將 todo-list.component.html 中的`Delete` button 加入 `*csdIf`　屬性


![image](https://user-images.githubusercontent.com/21993717/179142787-a1773950-aae2-46d9-8444-9a134b99ee06.png)

![image](https://user-images.githubusercontent.com/21993717/179143131-3357ab22-4b2f-4be8-a96e-2bc41f710e08.png)

![image](https://user-images.githubusercontent.com/21993717/179143664-c5264b43-3176-448b-a283-e2247e5858ac.png)

修改完成後，再執行一次，發現在三個要被控制的地方都已順利的被控制完成了。同時程式也變得更加簡潔了。

![image](https://user-images.githubusercontent.com/21993717/179143872-153e6e11-546d-439d-b479-9559ee2ff1af.png)

![image](https://user-images.githubusercontent.com/21993717/179144149-12dc2ef6-1017-4345-906f-b2251b308134.png)
