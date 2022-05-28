---
title: "使用 Angular 13 建立 Reactive Form 表單驗證範例"
date: 2022-05-28
description: "在這個範例中可以看到使用 Angular Reactive Form 搭配 Bootstrip UI Framework 所建立的　｀使用者資料註冊｀　功能，同時實現一個簡易版的｀自定驗證器｀(custome validator)"
tags: ["angular", "reactive form", "custome validator", "bootstrap"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular13-form-validation.git)*


本文將記錄如何一步步從無到有使用 Angular 13 Reactive Form 表單驗證 與 Bootstrap 建立 一個使用者資料註冊的表單　功能，在這個表單中當按下送出時會自動檢核使用者所輸入的資料是否合乎程式中所設定的檢核邏輯，並顯示合適的訊息反應給使用者。其中將會使用到下列技術:

- Ａngular CLI
- Bootstrap 4 & 5 (UI Framework)
- Angular Reactive Form
- Custome Validator

## 建立新專案

```bash
$ ng version

Angular CLI: 13.3.6
Node: 16.14.0
Package Manager: npm 8.3.1
OS: linux x64

Angular: 13.3.9
... animations, common, compiler, compiler-cli, core, forms
... platform-browser, platform-browser-dynamic, router

Package                         Version
---------------------------------------------------------
@angular-devkit/architect       0.1303.6
@angular-devkit/build-angular   13.3.6
@angular-devkit/core            13.3.6
@angular-devkit/schematics      13.3.6
@angular/cli                    13.3.6
@schematics/angular             13.3.6
rxjs                            7.5.5
typescript                      4.6.4

$ ng new form-validation    # 使用 ng cli 來建立專案
? Would you like to add Angular routing? (y/N) N    # 選擇 N 不使用 routing 功能
? Which stylesheet format would you like to use?    # 選擇 SCSS 
  CSS 
❯ SCSS   [ https://sass-lang.com/documentation/syntax#scss                ] 
  Sass   [ https://sass-lang.com/documentation/syntax#the-indented-syntax ] 
  Less   [ http://lesscss.org                                             ] 

...

 Packages installed successfully.
    Successfully initialized git.

$ cd form-validation/
$ ls -al
總用量 844
drwxrwxr-x   6 egs egs   4096  五  28 15:08 .
drwxrwxr-x   3 egs egs   4096  五  28 15:07 ..
-rw-rw-r--   1 egs egs   3261  五  28 15:07 angular.json
-rw-rw-r--   1 egs egs    600  五  28 15:07 .browserslistrc
-rw-rw-r--   1 egs egs    274  五  28 15:07 .editorconfig
drwxrwxr-x   8 egs egs   4096  五  28 15:08 .git
-rw-rw-r--   1 egs egs    548  五  28 15:07 .gitignore
-rw-rw-r--   1 egs egs   1432  五  28 15:07 karma.conf.js
drwxrwxr-x 599 egs egs  20480  五  28 15:08 node_modules
-rw-rw-r--   1 egs egs   1078  五  28 15:07 package.json
-rw-rw-r--   1 egs egs 778846  五  28 15:08 package-lock.json
-rw-rw-r--   1 egs egs   1068  五  28 15:07 README.md
drwxrwxr-x   5 egs egs   4096  五  28 15:07 src
-rw-rw-r--   1 egs egs    287  五  28 15:07 tsconfig.app.json
-rw-rw-r--   1 egs egs    863  五  28 15:07 tsconfig.json
-rw-rw-r--   1 egs egs    333  五  28 15:07 tsconfig.spec.json
drwxrwxr-x   2 egs egs   4096  五  28 15:07 .vscode

$ code .　# 打開 vscode
```

## 設置專案

### 匯入 ReactiveFormModule 模組

> 開啟 src/app/app.module.ts 並由 @angular/forms 匯入 ReactiveFormModule 

```ts {linenos=table,hl_lines=[3,11]}
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { ReactiveFormsModule } from '@angular/forms';
import { AppComponent } from './app.component';
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    ReactiveFormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### 使用 Bootstrap

> 方法一: 開啟 src/index.html 在 <head> 加入 link tag
- 使用 bootstrap 4 時使用第 9 行匯入語法
- 使用 bootstrap 5 時使用第 10 行匯入語法　
> 上述語法二擇一

> 由於 bootstrap 4 & 5 兩個版本語法有相異處，在範例中將展示不同的寫法

```html　{linenos=table,hl_lines=[9,10]}
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>FormValidation</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css" integrity="sha384-zCbKRCUGaJDkqS1kPbPd7TveP5iyJE0EjAuZQTgFLD2ylzuqKfdKlfG/eSrtxUkn" crossorigin="anonymous">
  <!-- <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous"> -->
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

> 方法二: 使用 npm install 來進行安裝
> 1.1 先使用 npm install 將 bootstrap 安裝到專案的 node_modules 目錄下

```bash {linenos=table,hl_lines=[2,10]}
# 下述版本二擇一
$ npm install bootstarp@4.6.1
npm WARN deprecated popper.js@1.16.1: You can find the new Popper v2 at @popperjs/core, this package is dedicated to the legacy v1

added 3 packages, and audited 928 packages in 1s

108 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

$ npm install bootstrap@5.1.3
added 2 packages, and audited 927 packages in 2s

108 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```
> npm install 成功後，可以在 package.json 中查看到已裝 bootstrap 的資訊

```json {linenos=table,hl_lines=[10]}
 "dependencies": {
    "@angular/animations": "~13.3.0",
    "@angular/common": "~13.3.0",
    "@angular/compiler": "~13.3.0",
    "@angular/core": "~13.3.0",
    "@angular/forms": "~13.3.0",
    "@angular/platform-browser": "~13.3.0",
    "@angular/platform-browser-dynamic": "~13.3.0",
    "@angular/router": "~13.3.0",
    "bootstrap": "^4.6.1",
    "rxjs": "~7.5.0",
    "tslib": "^2.3.0",
    "zone.js": "~0.11.4"
  },
 ```

> 1.2 npm install 成功後，還必須在 angular.json architect/build/options/styles 中匯入已安裝的 bootstrap 

```json {linenos=table,hl_lines=[31]}
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "form-validation": {
      "projectType": "application",
      "schematics": {
        "@schematics/angular:component": {
          "style": "css"
        }
      },
      "root": "",
      "sourceRoot": "src",
      "prefix": "app",
      "architect": {
        "build": {
          "builder": "@angular-devkit/build-angular:browser",
          "options": {
            "outputPath": "dist/form-validation",
            "index": "src/index.html",
            "main": "src/main.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "tsconfig.app.json",
            "assets": [
              "src/favicon.ico",
              "src/assets"
            ],
            "styles": [
              "src/styles.scss",
              "node_modules/bootstrap/scss/bootstrap.scss"
            ],
            "scripts": []
            ...
          },
        }
      }
    }
  }
}
```

## 開始撰寫程式

開啟 app.component.ts 程式檔案，並先 import @angular/form module 中的　AbstractControl, FormBuilder, FormGroup, Validators。我們會使用 Angular FormBuilder 建立一個 FormGroup 物件（表單屬性），然後綁定到模板 <form> 元素（稍後使用 [formGroup] 指令）。 Validators 提供了一組內置的驗證器（required、minLength、maxLength…），可供表單控件(control)所使用。

```ts {linenos=table,hl_lines=[2]}
import { Component, OnInit } from '@angular/core';
import { AbstractControl, FormBuilder, FormGroup, Validators } from '@angular/forms';
```

> 完成後程式如下
```ts {linenos=table,hl_lines=["10-18",19,"24-50","53-55"]}
import { Component, OnInit } from '@angular/core';
import { AbstractControl, FormBuilder, FormControl, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})

export class AppComponent implements OnInit {
  form: FormGroup = new FormGroup({
    fullname: new FormControl(''),
    username: new FormControl(''),
    email: new FormControl(''),
    password: new FormControl(''),
    confirmPassword: new FormControl(''),
    acceptTerms: new FormControl(false),
  });
  submitted = false;

  constructor(private formBuilder: FormBuilder) {}

  ngOnInit(): void {
    this.form = this.formBuilder.group(
      {
        fullname: ['', Validators.required],
        username: [
          '',
          [
            Validators.required,
            Validators.minLength(6),
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
        acceptTerms: [false, Validators.requiredTrue]        
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
    console.log(JSON.stringify(this.form.value, null, 2));
  }

  onReset(): void {
    this.submitted = false;
    this.form.reset();
  }
}
```
> 在上述程式裡，我們所建立的 this.form (程式 24-50 行)這個 FormGroup 物件中使用到了許多內建的表單驗證器，如：Validators.required（必填）、Validators.minLength（最小長度）、Validators.maxLength（最大長度）、Validators.requiredTrue（必須為 true）等，同時在程式第 48 行也會使用到｀自定驗證器｀(稍後會進行如何撰寫一個自定驗證器)

> 在程式第51行我們定義了一個 getter 以方便我們在 template 中存取 form 中的控件（contol）。透過這個 getter function 你可以使用｀f.username｀ 來取代 ｀form.controls.username｀，使 template（表單樣版） 看起簡潔些。

## 自定 CSS 設定

為控制整張 form 的寬度，我們在 app.component.scss 中定義了一個 class - register-form，定義它最大寬度，並套用在 html 檔案中。

```css
.register-form {
    max-width: 350px;
    margin: auto;
  }
```

## 自定表單驗證器

在前述 reactive 表單定義中我們希望在 “Confirm Password” 欄位除了“必填”的檢核邏輯外，還要有一個驗證的機制是｀比對 “Password” 與 “Confirm Password” 這兩個欄位值必須一致"，這個邏輯在`內建的表單欄位驗證器`中並沒有提供，所以我們要自行撰寫這個驗證器。

自定驗證器就像我們在日常程式中經常使用的函數一樣。你可以為任何給定場景創立自定驗證器。在 Angular 中建立自定驗證非常簡單，就像建立其他函數一樣。自定驗證器將 AbstractControl 作為參數，如果驗證失敗，則以 `key: value` 對的形式回傳一個物件。

先建立一個子目錄 utils，新增一個 validation.ts 程式檔，主要的邏輯有
- 首先，若檢查有任何誤則回傳 null
- 再則，若兩個被檢核的欄位值不相同，則回傳

```ts
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
> 完成後的驗證器會被使用在 app.component.ts form 的定義中，見下程式第26行

```ts {linenos=table,hl_lines=[26]}
  ngOnInit(): void {
    this.form = this.formBuilder.group(
      {
        fullname: ['', Validators.required],
        username: [
          '',
          [
            Validators.required,
            Validators.minLength(6),
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
        acceptTerms: [false, Validators.requiredTrue]        
      },
      {
        validators: [Validation.match('password', 'confirmPassword')]
      }
    )
  }
```

## 執行程式

> 結行結果：

![2022-05-28 21-47-28](https://user-images.githubusercontent.com/21993717/170828462-2ecfdd90-6bf5-4f33-8004-b6aabbd5ac4d.png)

> 按下｀送出｀按鈕將所有檢核不合可訊息顯示在欄位下方

![2022-05-28 21-54-29](https://user-images.githubusercontent.com/21993717/170828491-0f43fbec-aaae-4b9e-ae59-c8c39f175258.png)

> email 格式不符的驗證

![2022-05-28 21-48-39](https://user-images.githubusercontent.com/21993717/170828527-4a43bf29-b996-49b1-b397-6f4bc15c1658.png)

> password 長度的驗證

![2022-05-28 21-48-53](https://user-images.githubusercontent.com/21993717/170828578-323a89ce-5b47-42ed-81a9-b0c786799fc6.png)

> confirm password 自定驗證喌右的檢核

![2022-05-28 21-49-01](https://user-images.githubusercontent.com/21993717/170828590-11280fa4-198e-4137-9c3b-4e0430c0e3f4.png)

> 檢核完成

![2022-05-28 21-49-09](https://user-images.githubusercontent.com/21993717/170828608-74f67436-e4a8-4973-a581-7b4348c08fc6.png)


## 套用 bootstrap 5 的 html 

> index.html 中 link 到 bootstrp@5.1.3 的版本

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>FormValidation</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <!-- <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css" integrity="sha384-zCbKRCUGaJDkqS1kPbPd7TveP5iyJE0EjAuZQTgFLD2ylzuqKfdKlfG/eSrtxUkn" crossorigin="anonymous"> -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

> app.component.html 套用的 bootstrp class 需要微調

```html
<div class="container-fluid register-form">
  <form needs-validation [formGroup]="form" (ngSubmit)="onSubmit()">
    <div class="note">  
      <h1> 使用者資料註冊 </h1>  
    </div>  

    <div class="mb-4">
      <label for="fullname" class="form-label">Full Name</label>
      <input type="text" formControlName="fullname" id="fullname" class="form-control"
      [ngClass]="{ 'is-invalid': submitted && f['fullname'].errors,
                   'is-valid': submitted && !f['fullname'].errors }" 
      />
      <div class="invalid-feedback">
        Full Name 為必填
      </div>
      <div class="valid-feedback">
        填寫正確
      </div>
    </div>
    
    <div class="mb-4">
      <label for="username" class="form-label">Username</label>
      <input type="text" formControlName="username" id="username" class="form-control"
      [ngClass]="{ 'is-invalid': submitted && f['username'].errors,
                   'is-valid': submitted && !f['username'].errors }" 
      />
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
      <input type="text" formControlName="email" id="email" class="form-control"
      [ngClass]="{ 'is-invalid': submitted && f['email'].errors,
                   'is-valid': submitted && !f['email'].errors }"
      />
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
      <input type="password" formControlName="password" id="password" class="form-control"
      [ngClass]="{ 'is-invalid': submitted && f['password'].errors,
                   'is-valid': submitted && !f['password'].errors }" 
      />
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
      <input type="password" formControlName="confirmPassword" id="confirmPassword" class="form-control"
      [ngClass]="{ 'is-invalid': submitted && f['confirmPassword'].errors,
                   'is-valid': submitted && !f['confirmPassword'].errors }" 
      />
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

    <div class="mb-4 form-check">
      <input type="checkbox" formControlName="acceptTerms" id="acceptTerms" class="form-check-input"
        [ngClass]="{ 'is-invalid': submitted && f['acceptTerms'].errors }"/>
      <label for="acceptTerms" class="form-check-label">
        我已閱讀並同意條款
      </label>
      <div *ngIf="submitted && f['acceptTerms'].errors" class="invalid-feedback">
        同意條款 為必填
      </div>
    </div>

    <div class="mb-4">
      <button type="submit" class="btn btn-primary">送出</button>
      <button type="button" (click)="onReset()"
      class="btn btn-warning float-end">
        重置
      </button>
    </div>
  </form>
</div>
```