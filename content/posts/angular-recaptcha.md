---
title: "在 Angular 中實現 CAPTCHA 驗證碼功能"
date: 2023-03-21
description: "本篇將要紀錄如何在 Angular 14 使用者驗證功能中加入 reCAPTCHA 驗證碼功能來幫你的 App 安全升級，免除機器人的暴力破解"
tags: ["angular", "reCAPTCHA"]
categories: ["angular"]
---

# 在 Angular 中實現 CAPTCHA 驗證碼功能

先來看看完成後的效果：

![2023-03-21 20-56-34 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/226612604-1a7d0da9-fcaf-41cc-8e80-c3d0d2ba4326.png)


## 關於 reCAPTCHA

目前 reCAPTCHA 共有 v2、v3、Enterprise 三種版本。在此我們將採用 v2。使用 reCAPTCHA 需先申在 google 網站上申請 API Key，申請完成後會有二個 API Key，一個是被用來放在前端 App 中，另一個是使用在呼叫 Google API 取得驗證結果時要用的。申請網站在 https://www.google.com/recaptcha/admin，使用 Gmail 帳號登入，若從未申請過會跳到註冊新網站頁面：

- 識別這組 Key 用的標籤文字
- reCAPTCHA 類型 (使用 v2)
- 應用網域 (本機測試可加 localhost)
- 登入 Gmail 是預設擁有者，可加入其他管理者
- 接受服務條款
- 傳送通知給擁有者（發生問題或流量異常時發送通知）

![image](https://user-images.githubusercontent.com/21993717/226610210-d4b21880-bad8-4ecc-9f59-234836cbee4d.png)

使用 Gmail 帳號進行登入
![image](https://user-images.githubusercontent.com/21993717/226614270-a3e0dc62-078e-4971-8094-f26033b9dfd7.png)

![image](https://user-images.githubusercontent.com/21993717/226615645-125acfc4-6aad-4c1a-b416-5b4314a93539.png)

輸入好資料後按下提交

![image](https://user-images.githubusercontent.com/21993717/226615965-49e6d7db-0e71-4513-b57a-132b1c1f017b.png)

完成後會産生二個 api key:

![image](https://user-images.githubusercontent.com/21993717/226616329-f70fbd08-44d2-4194-b4b1-989eaca4330a.png)

按下“複製網站金鑰”備用。（將放至 angular login component 中）


## 設置專案環境

這個專案不會從頭從無到有紀錄每一個建置的步驟，而是採取套用 **“使用 Angular 14 建立 Reactive Form 表單驗證範例”** 這篇筆記所完成的程式碼來擴充 reCAPTCHA 功能。


### 複製即有專案
首先 git clone 即有的 MySQL 專案

```bash
$ git clone https://github.com/calvinegs/angular14-auth-jwt.git angular-recaptcha

$ cd angular-recaptcha
```

## 安裝 ngx-captcha 套件

由於我們的 angular 專案是 v14，所以安裝 ngx-captcha 時請指定 v12 版本。

```bash
$ npm i ngx-captcha@12.0.2
```

## 在 App.module.ts 中匯入 NgxCaptchaModule

```ts {linenos=table,hl_lines=[16,34]}
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
import { NgxCaptchaModule } from 'ngx-captcha';

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
    HttpClientModule,
    NgxCaptchaModule
  ],
  providers: [authInterceptorProviders],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

## 將前述取得的 api key 寫入到 appSettings 中

appSettings.ts

```ts {linenos=table,hl_lines=[3,4,5]}
export const AppSettings = {
     API_URL : 'http://localhost:5000/api/',
     recaptcha: {
        siteKey: '6LeE4BwlAAAAADWMaRf8C8o8R91PYaoGfO3YQ2_f',
      },
}
```

## 在 login component class 中加入相關程式

先在程式中(app.component.ts) 取得 AppSettings 中的 api key :

```ts {linenos=table,hl_lines=[]}
	siteKey: string = AppSettings.recaptcha.siteKey;
```

在原有的 login component 中登錄功能的表單僅有“user name“ 及 "password" 二個欄位，新加入 "recaptcha" 這個欄位。

```ts {linenos=table,hl_lines=[5]}
	this.form = this.formBuilder.group(
      {
        username: ['',[Validators.required]],
        password: ['',[Validators.required]],
        recaptcha: ['',[Validators.required]]
      }
    )
```

## 在 login component view template 中加入 recaptcha 欄位

在 login.component.html 中加入新欄位的設定程式

```html {linenos=table,hl_lines=[]}
      <div class="mb-4">
        <ngx-recaptcha2 #captchaElem
          [siteKey]="this.siteKey" formControlName="recaptcha"
          [ngClass]="{ 'is-invalid': submitted && f['recaptcha'].errors,
          'is-valid': submitted && !f['recaptcha'].errors }"
        >
        </ngx-recaptcha2>
        <div *ngIf="submitted && f['recaptcha'].errors" class="invalid-feedback">
          <div *ngIf="f['recaptcha'].errors['required']">請勾選</div>
        </div>
      </div>
```

完整的 login.component.html 程式如下：

```html {linenos=table,hl_lines=[]}
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
        <ngx-recaptcha2 #captchaElem
          [siteKey]="this.siteKey" 
          formControlName="recaptcha"
          [ngClass]="{ 'is-invalid': submitted && f['recaptcha'].errors,
          'is-valid': submitted && !f['recaptcha'].errors }"
        >
        </ngx-recaptcha2>
        <div *ngIf="submitted && f['recaptcha'].errors" class="invalid-feedback">
          <div *ngIf="f['recaptcha'].errors['required']">請勾選</div>
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

完整的 login.component.ts 程式如下：
除前述的相關功能外，還額外在“重置”按鈕中加入了 reloadCaptcha 的功能， 來重置 reloadCaptcha 選項勾選的狀態。

```ts {linenos=table,hl_lines=[]}
import { Component, OnInit, ViewChild } from '@angular/core';
import { AbstractControl, FormBuilder, FormControl, FormGroup, Validators } from '@angular/forms';
import { ReCaptcha2Component } from 'ngx-captcha';
import { AppSettings } from 'src/app/shared/helpers/appSettings';
import { AuthService } from 'src/app/shared/services/auth.service';
import { TokenStorageService } from 'src/app/shared/services/token-storage.service';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {
  @ViewChild('captchaElem') captchaElem: ReCaptcha2Component | undefined;

  form: FormGroup = new FormGroup({
    username: new FormControl(''),
    password: new FormControl(''),
    recaptcha: new FormControl('')
  });
  siteKey: string = AppSettings.recaptcha.siteKey;
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
        password: ['',[Validators.required]],
        recaptcha: ['',[Validators.required]]
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
    this.captchaElem?.reloadCaptcha();

  }
}
```

## 測試結果

按下“送出”時，所有必填欄位皆會檢核是否正確填妥資料。

![image](https://user-images.githubusercontent.com/21993717/226624792-4dd6fb0b-9f06-44c8-b089-a3d0ea43811d.png)


正常的登入流程，使用者僅須輸入“user name”、“password” 及 “勾選” “我不是機器人”選項即可送出

![image](https://user-images.githubusercontent.com/21993717/226626466-5b1d377c-f1e9-4013-958a-c8d0bed2a6fb.png)

當系統判斷出有可疑登入時便自動切人工輸入的方式來進行驗證

![image](https://user-images.githubusercontent.com/21993717/226625368-cc583de6-c401-4683-9fa1-890bc0810a21.png)