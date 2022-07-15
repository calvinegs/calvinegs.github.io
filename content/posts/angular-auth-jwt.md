---
title: "使用 Angular 14 建立 Reactive Form 表單驗證範例"
date: 2022-06-15
description: "在這個範例中可以看到使用 Angular Reactive Form 搭配 Bootstrip UI Framework 所建立的｀使用者資料註冊｀功能，同時實現一個簡易版的｀自定驗證器｀(custome validator)"
tags: ["angular", "reactive form", "custome validator", "bootstrap"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular14-auth-jwt.git)*


本文記錄如何一步步從無到有使用 Angular 14 Reactive Form 表單驗證 與 Bootstrap 5 建立 一個使用者資料註冊、登入的表單功能連結到後端 API (node+postgresSQL)，在這些表單中按下送出時會自動檢核使用者所輸入的資料是否合乎程式中所設定的檢核邏輯，並顯示合適的訊息反應給使用者。其中將會使用到下列技術:

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
    

Angular CLI: 14.0.1
Node: 16.14.0
Package Manager: npm 8.3.1 
OS: linux x64

Angular: undefined
... 

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.1400.1
@angular-devkit/core         14.0.1
@angular-devkit/schematics   14.0.1
@angular/cli                 14.0.1
@schematics/angular          14.0.1
```

## 建立新專案
```bash
$ npx ng new angular-auth-jwt
? Would you like to add Angular routing? Yes
? Which stylesheet format would you like to use? SCSS   [ 
https://sass-lang.com/documentation/syntax#scss                ]
CREATE angular-auth-jwt/README.md (1068 bytes)
CREATE angular-auth-jwt/.editorconfig (274 bytes)
CREATE angular-auth-jwt/.gitignore (548 bytes)
CREATE angular-auth-jwt/angular.json (3146 bytes)
CREATE angular-auth-jwt/package.json (1047 bytes)
CREATE angular-auth-jwt/tsconfig.json (863 bytes)
CREATE angular-auth-jwt/.browserslistrc (600 bytes)
CREATE angular-auth-jwt/karma.conf.js (1433 bytes)
CREATE angular-auth-jwt/tsconfig.app.json (287 bytes)
CREATE angular-auth-jwt/tsconfig.spec.json (333 bytes)
CREATE angular-auth-jwt/.vscode/extensions.json (130 bytes)
CREATE angular-auth-jwt/.vscode/launch.json (474 bytes)
CREATE angular-auth-jwt/.vscode/tasks.json (938 bytes)
CREATE angular-auth-jwt/src/favicon.ico (948 bytes)
CREATE angular-auth-jwt/src/index.html (300 bytes)
CREATE angular-auth-jwt/src/main.ts (372 bytes)
CREATE angular-auth-jwt/src/polyfills.ts (2338 bytes)
CREATE angular-auth-jwt/src/styles.scss (80 bytes)
CREATE angular-auth-jwt/src/test.ts (749 bytes)
CREATE angular-auth-jwt/src/assets/.gitkeep (0 bytes)
CREATE angular-auth-jwt/src/environments/environment.prod.ts (51 bytes)
CREATE angular-auth-jwt/src/environments/environment.ts (658 bytes)
CREATE angular-auth-jwt/src/app/app-routing.module.ts (245 bytes)
CREATE angular-auth-jwt/src/app/app.module.ts (393 bytes)
CREATE angular-auth-jwt/src/app/app.component.scss (0 bytes)
CREATE angular-auth-jwt/src/app/app.component.html (23364 bytes)
CREATE angular-auth-jwt/src/app/app.component.spec.ts (1103 bytes)
CREATE angular-auth-jwt/src/app/app.component.ts (221 bytes)
✔ Packages installed successfully.
    Successfully initialized git.

$ cd angular-auth-jwt

$ cat package.json 
{
  "name": "angular-auth-jwt",
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
    "@angular-devkit/build-angular": "^14.0.1",
    "@angular/cli": "~14.0.1",
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

✔ Browser application bundle generation complete.

Initial Chunk Files   | Names         |  Raw Size
vendor.js             | vendor        |   2.02 MB | 
polyfills.js          | polyfills     | 313.40 kB | 
styles.css, styles.js | styles        | 207.83 kB | 
main.js               | main          |  50.14 kB | 
runtime.js            | runtime       |   6.53 kB | 

                      | Initial Total |   2.58 MB

Build at: 2022-06-15T03:24:52.638Z - Hash: abf680be2edec496 - Time: 9646ms

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
$ ng g s shared/services/user # 新增 user service

$ ng g interceptor shared/interceptor/auth　# 新增 auth interceptor

$ ng g c pages/login
$ ng g c pages/register
$ ng g c pages/profile
$ ng g c pages/home
$ ng g c pages/board-user
$ ng g c pages/board-moderator
$ ng g c pages/board-admin

$ ng g guard shared/guard/auth --implements="CanActivate" # 新增 auth guard
$ ng g guard shared/guard/role --implements="CanActivate" # 新增 role guard
```

### 檔案結構

![2022-06-15 11-45-21](https://user-images.githubusercontent.com/21993717/173732655-0f9a5d4c-dee3-4954-bba5-9b8fb2719c7d.png)


## 撰寫 Services 程式

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
註冊與登入功能須搭配後端 API，可參考　*[使用 Node.js + express + postgres 建立一個後端服務 REST API](https://calvinegs.github.io/posts/nodejs-restapi-postgres/)*

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

### User 相關的服務程式 user.service.ts
user service 是藉由 API 來展示如何透過權限管理功能來管理不同的子功能。這由的權限管控時直接利用後端 API 功能來檢核目前的使用者是否具有相關權限來執行這些功能。在本筆記後面我們也會利用 Angular Router Guard 的功能來實現 Local 如何來管理 user 與路由間的執行權限。

```ts
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { AppSettings } from '../helpers/appSettings';

const API_URL = AppSettings.API_URL + 'users/test/';

@Injectable({
  providedIn: 'root'
})

export class UserService {

  constructor(private http: HttpClient) { }
  
  getPublicContent(): Observable<any> {
    return this.http.get(API_URL + 'all', { responseType: 'text' })
  }

  getUserBoard(): Observable<any> {
    return this.http.get(API_URL + 'user', { responseType: 'text' })
  }

  getModeratorBoard(): Observable<any> {
    return this.http.get(API_URL + 'moderator', { responseType: 'text' })
  }

  getAdminBoard(): Observable<any> {
    return this.http.get(API_URL + 'admin', { responseType: 'text' })
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
上述完成的 Interceptor 要記得`注入`到 app module providers 設定中（第14、32行）。
同時，在前述的 services 中我們使用到了 HttpClient，所以也必須匯入 HttpClient Module（第3、30行）。

```ts {linenos=table,hl_lines=[3,14,30,32]}
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { LoginComponent } from './pages/login/login.component';
import { RegisterComponent } from './pages/register/register.component';
import { HomeComponent } from './pages/home/home.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { BoardAdminComponent } from './pages/board-admin/board-admin.component';
import { BoardModeratorComponent } from './pages/board-moderator/board-moderator.component';
import { BoardUserComponent } from './pages/board-user/board-user.component';
import { authInterceptorProviders } from './shared/interceptor/auth.interceptor';

@NgModule({
  declarations: [
    AppComponent,
    LoginComponent,
    RegisterComponent,
    HomeComponent,
    ProfileComponent,
    BoardAdminComponent,
    BoardModeratorComponent,
    BoardUserComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule
  ],
  providers: [authInterceptorProviders],
  bootstrap: [AppComponent]
})
export class AppModule { }
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
註冊元件的程式碼
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

![image](https://user-images.githubusercontent.com/21993717/173984484-7ff4cf0d-4716-461f-a378-b45332e05812.png)


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

![image](https://user-images.githubusercontent.com/21993717/173984628-624f11cd-78a9-4008-88cb-4705e2af52e1.png)


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

## 新增 Role-base components

接下來要完成的功能是為了展示不同授權使用者可以看到的 UI 及 執行不同功能的權限控制。

### 新增 Public Component
```ts
//home.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService } from '../_services/user.service';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  content?: string;

  constructor(private userService: UserService) { }

  ngOnInit(): void {
    this.userService.getPublicContent().subscribe({
      next: data => {
        this.content = data;
      },
      error: err => {
        this.content = JSON.parse(err.error).message;
      }
    });
  }
}
```
```html
<!-- home.component.heml -->
<!-- board-user.component.heml -->
<!-- board-moderator.component.heml -->
<!-- board-admin.component.heml -->
<div class="container">
  <header class="jumbotron">
    <p>{{ content }}</p>
  </header>
</div>
```

### 新增 User Component
```ts
// board-user.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService } from 'src/app/shared/services/user.service';

@Component({
  selector: 'app-board-user',
  templateUrl: './board-user.component.html',
  styleUrls: ['./board-user.component.scss']
})
export class BoardUserComponent implements OnInit {
  content?: string;
  constructor(private userService: UserService) { }

  ngOnInit(): void {
    this.userService.getUserBoard().subscribe({
      next: data => {
        this.content = data;
      },
      error: err => {
        this.content = JSON.parse(err.error).message;
      }
    })
  }
}

```
### 新增 Moderator Component
```ts
// board-moderator.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService } from 'src/app/shared/services/user.service';

@Component({
  selector: 'app-board-moderator',
  templateUrl: './board-moderator.component.html',
  styleUrls: ['./board-moderator.component.scss']
})
export class BoardModeratorComponent implements OnInit {
  content?: string;
  constructor(private userService: UserService) { }

  ngOnInit(): void {
    this.userService.getModeratorBoard().subscribe({
      next: data => {
        this.content = data;
      },
      error: err => {
        this.content = JSON.parse(err.error).message;
      }
    })
  }
}
```
### 新增 Admin Component
```ts
// board-admin.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService } from 'src/app/shared/services/user.service';

@Component({
  selector: 'app-board-admin',
  templateUrl: './board-admin.component.html',
  styleUrls: ['./board-admin.component.scss']
})
export class BoardAdminComponent implements OnInit {
  content?: string;
  constructor(private userService: UserService) { }

  ngOnInit(): void {
    this.userService.getAdminBoard().subscribe({
      next: data => {
        this.content = data;
      },
      error: err => {
        this.content = JSON.parse(err.error).message;
      }
    })
  }
}
```

## 路由設置
透過路由的設置，程式才會知道當使用者在瀏覧器特定的 URI 時，程式應該去執那支相對應的程式。路由模組檔案（app-routin.module.ts）內容如下：

```ts {linenos=table,hl_lines=[11-23]}
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { BoardAdminComponent } from './pages/board-admin/board-admin.component';
import { BoardModeratorComponent } from './pages/board-moderator/board-moderator.component';
import { BoardUserComponent } from './pages/board-user/board-user.component';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  { path: 'profile', component: ProfileComponent },
  { path: 'user', component: BoardUserComponent},
  { path: 'moderator', component: BoardModeratorComponent },
  { path: 'admin', component: BoardAdminComponent },
  { path: '', redirectTo: 'home', pathMatch: 'full'}
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
import { ReactiveFormsModule } from '@angular/forms'
import { HttpClientModule } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { LoginComponent } from './pages/login/login.component';
import { RegisterComponent } from './pages/register/register.component';
import { HomeComponent } from './pages/home/home.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { BoardAdminComponent } from './pages/board-admin/board-admin.component';
import { BoardModeratorComponent } from './pages/board-moderator/board-moderator.component';
import { BoardUserComponent } from './pages/board-user/board-user.component';
import { authInterceptorProviders } from './shared/interceptor/auth.interceptor';

@NgModule({
  declarations: [
    AppComponent,
    LoginComponent,
    RegisterComponent,
    HomeComponent,
    ProfileComponent,
    BoardAdminComponent,
    BoardModeratorComponent,
    BoardUserComponent
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

App 元件的程式碼
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
  private roles: string[] = [];
  isLoggedIn = false; //用來記錄是否已登入系統的變數
  showAdminBoard = false; //記錄是否有｀管理者｀角色的變數
  showModeratorBoard = false;　//記錄是否有｀版主｀角色的變數
  username?: string;

  constructor(private tokenStorageService: TokenStorageService) {}

  ngOnInit(): void {
    this.isLoggedIn = !!this.tokenStorageService.getToken();  // 若在 session storage 中有有效的 token 則表示已有正常的登入系統

    if (this.isLoggedIn) {
      const user = this.tokenStorageService.getUser();
      this.roles = user.roles;

      this.showAdminBoard = this.roles.includes('ADMIN'); //判斷是否有管理者角色
      this.showModeratorBoard = this.roles.includes('MODERATOR');　//判斷是否有版主角色

      this.username = user.username;
    }
  }

  logout(): void {
    this.tokenStorageService.signOut(); //清除 session storage
    location.reload();
  }
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
        <li class="nav-item" *ngIf="showAdminBoard">
          <a href="/admin" class="nav-link" routerLink="admin">管理者</a>
        </li>
        <li class="nav-item" *ngIf="showModeratorBoard">
          <a href="/moderator" class="nav-link" routerLink="moderator">版主</a>
        </li>
        <li class="nav-item">
          <a href="/user" class="nav-link" *ngIf="isLoggedIn" routerLink="user">使用者</a>
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

首頁為預設的 Page，同時它也是一供公開的功能，沒有使用權限的限定，所以即使你未登入，也可以瀏覽。

![image](https://user-images.githubusercontent.com/21993717/173994940-5ce62d05-63fa-4c11-b239-f7dd77ddc88d.png)

2. 一般使用者登入後的畫面

以已註冊的使用者登入系統後，可以發現主頁面上的功能選項多出了“使用者” 與 "jack" 兩項

![image](https://user-images.githubusercontent.com/21993717/174007267-afa46a4a-9814-469c-8080-6198b2f09f39.png)

其中 ｀jack｀ 指的是現在已登入者的 username (登入帳號)，同時點進這功能會顯示使用者的明細資料。
![image](https://user-images.githubusercontent.com/21993717/174007446-a0535b43-c022-450e-89f9-72d53874f01d.png)

而｀使用者｀這個功能項目也是隨著使用者登入成功後才顯示出來的。
![image](https://user-images.githubusercontent.com/21993717/174007566-618af925-843a-44a0-b6a2-8423248eb64b.png)


3. 管理角色使用者登入後的畫面

下圖是以｀管理者｀角色登入後所顯示的畫面。而因為這個使用者同時角色多重角色（admin & Moderator)，所以功能選項同時出現了 `管理者｀｀版主｀｀使用者｀等項目。
![image](https://user-images.githubusercontent.com/21993717/174008794-8a8bdcb1-58b1-479d-abf3-4aab0039d5f1.png)


## Route Guard 
上述 Role-base components 執行時的權限管控是透過 API 端的功能來實現，這樣的方式有其必要性，特別是在保護 Web API 端點的執行權限。

下圖中是指還沒有登入時刻意的在瀏覽器輸入　“http://localhost:4200/user”，結果以目前程式的流程，這個路由所對應到的程式是有被執行的，只是因為我們在後台的 API 中有啟動了保護措施，所以檢核出目前未登入的狀態不應該可以執行這個特定的功能，所以回傳了“目前未被授權”的錯誤訊息。

![image](https://user-images.githubusercontent.com/21993717/174021815-42e08e2b-7007-4174-a881-5cdfa4341c97.png)

而下面第二個案例，使用者 jack 是一般使用者，所以登入後顯示可執行功能如下圖只有｀使用者｀這項功能。此刻若直接在瀏覽器輸入　http://localhost:4200/admin 會發現雖然畫面上沒有顯示｀管理者｀功能選項，但你還是可以｀執行｀它，也因為後端 API 有檢核，所以回傳了｀Require Admin Role!｀的錯誤內容。

![image](https://user-images.githubusercontent.com/21993717/174021061-1502a876-bc83-4484-82ea-650ce8f9e5b9.png)

### 帳號認證攔截器

接下來要介紹的　路由保護器(攔截器)　是一種 local 端保護機制。透過 Route Guard 的設置可以在使用者端就能效管理各個 route 的執行權限的控管。

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
  
  constructor(private router: Router, private tokenService: TokenStorageService) {};

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    
    if(this.tokenService.getToken()) return true; 
    this.router.navigate(['/login']);
    return true;
  }  
}
```

app-routing.module.ts 中的路由設定，將 AuthGurad 加入到要保護的 路由設定　中。如下程式第18行`{ path: 'add-todo', canActivate:[AuthGuard], component: AddTodoComponent },`，其中的　`canActivate:[AuthGuard]` 就是幫這個路由設定加入了 ‘AuthGuard’ 這個攔截器來起到保護這個路由的作用。
```ts {linenos=table,hl_lines=[11,18-20]}
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { BoardAdminComponent } from './pages/board-admin/board-admin.component';
import { BoardModeratorComponent } from './pages/board-moderator/board-moderator.component';
import { BoardUserComponent } from './pages/board-user/board-user.component';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';
import { AuthGuard } from './shared/guard/auth.guard';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  { path: 'profile', component: ProfileComponent },
  { path: 'user', component: BoardUserComponent, canActivate:[AuthGuard] },
  { path: 'moderator', component: BoardModeratorComponent, canActivate:[AuthGuard, RoleGuard], data: {roles: ['moderator']} },
  { path: 'admin', component: BoardAdminComponent, canActivate:[AuthGuard, RoleGuard], data: {roles: ['admin']} },
  { path: '', redirectTo: 'home', pathMatch: 'full'}
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### 角色授權攔截器

roleGuard 這個路由保護器的功能就是來攔截現行的使用者是否具備有特定的角色，若沒有指定的角色不得執行特定的路由。並顯示目前使有者所具備的角色有那些。

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
      let searchRole = 'USER';
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

app-routing.module.ts 中的路由設定，將 AuthGurad 加入到要保護的 路由設定　中。如下程式第18行`{ path: 'add-todo', canActivate:[AuthGuard], component: AddTodoComponent },`，其中的　`canActivate:[AuthGuard]` 就是幫這個路由設定加入了 ‘AuthGuard’ 這個攔截器來起到保護這個路由的作用。

```ts {linenos=table,hl_lines=[12,20-21]}
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { BoardAdminComponent } from './pages/board-admin/board-admin.component';
import { BoardModeratorComponent } from './pages/board-moderator/board-moderator.component';
import { BoardUserComponent } from './pages/board-user/board-user.component';
import { HomeComponent } from './pages/home/home.component';
import { LoginComponent } from './pages/login/login.component';
import { ProfileComponent } from './pages/profile/profile.component';
import { RegisterComponent } from './pages/register/register.component';
import { AuthGuard } from './shared/guard/auth.guard';
import { RoleGuard } from './shared/guard/role.guard';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'login', component: LoginComponent },
  { path: 'register', component: RegisterComponent },
  { path: 'profile', component: ProfileComponent },
  { path: 'user', component: BoardUserComponent, canActivate:[AuthGuard] },
  { path: 'moderator', component: BoardModeratorComponent, canActivate:[AuthGuard, RoleGuard], data: {roles: ['moderator']} },
  { path: 'admin', component: BoardAdminComponent, canActivate:[AuthGuard, RoleGuard], data: {roles: ['admin']} },
  { path: '', redirectTo: 'home', pathMatch: 'full'}
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```