---
title: "Angular Interceptor 使用案例二：搭配 Jason Web Token + Session Storage 來實現使用者身份驗證功能"
date: 2023-02-25
draft: false
description: "使用 Interceptor 來完成當程式讀取後端資料時於畫面上顯示 “讀取中...“ 動畫效果的等待畫面，讓使用者有良好的使用體驗"
tags: ["angular", "webapi", "interceptor", "httpclient", "css", "rxjs"]
categories: ["angular"]
---
*[github Source code](https://github.com/calvinegs/angular-interceptor-jwt.git)*

# Angular Interceptor 使用案例二：搭配 Jason Web Token + Session Storage 來實現使用者身份驗證功能

使用者驗證在所有的應用程式中都是很重要的一個環節。本篇筆記中將使用 Angular Interceptor + Jason Web Token 以及 Session Storage 來實現這個重點功能。

本篇這個“前端登入功能”所搭配的後端程式可詳見 *[“使用 .NET 6.0 進行 JWT 身份驗證的 ASP.NET Web Api”](https://calvinegs.github.io/posts/dotnet6-webapi-jwt/)* 

執行步驟如下：

- 取得預備的程式碼
- 建立 appSettings 來存放程式參數值
- 建立 auth.service.ts
- 建立 token-storage.service.ts
- 完成 login page component 中的程式碼
- 執行測試
- 建立 interceptor 程式
- 建立 Guard 程式


## 取得預備的程式碼

在 *["使用 Angular Material 建立一個簡易的 Admin Template II"](https://calvinegs.github.io/posts/angular-material-adminui-ii/)* 已經使用 Material UI Component 設計好登入畫面，接下來就直接捉取這個版本的程式碼來接續完成使用者登入功能的所有程式。

可使用以下 git clone 指令來取得該版本的 Source Code “git clone --branch v2.0 https://github.com/calvinegs/Angular_Material_Admin_Template jwtAuth”

```bash
$ git clone --branch v2.0 https://github.com/calvinegs/Angular_Material_Admin_Template jwtAuth
正複製到 'jwtAuth'...
remote: Enumerating objects: 112, done.
remote: Counting objects: 100% (112/112), done.
remote: Compressing objects: 100% (77/77), done.
remote: Total 112 (delta 37), reused 104 (delta 29), pack-reused 0
接收物件中: 100% (112/112), 229.41 KiB | 1.35 MiB/s, 完成.
處理 delta 中: 100% (37/37), 完成.
註：切換至 “14330473003d6fe7c52cf5218113e1aa52f6b19c”。

您正處於「分離 HEAD」狀態。您可以檢視、進行實驗性修改並提交，
而且您可以在切回分支時，捨棄在此狀態下所做的提交
而不對分支造成影響。

如果您想要透過建立分支來保留在此狀態下所做的提交，
您可以現在或稍後在 switch 指令使用 -c 選項。例如：

  git switch -c <新分支名稱>

或者是使用下述命令復原此動作：

  git switch -

將組態變數 advice.detachedHead 設定為 false，即可關閉本建議

$ cd jwtAuth
$ git swithc -c main
$ npm i

$ code .    # 開啟 VS Code
```

## 建立 appSettings 來存放程式參數值

考慮到彈性應用，新增一支 appSettings.ts 程式。在 shared 目錄下新增一個　helpers 的子目錄，並新增一支 appSettings.ts 程式，內容如下：

設置一個靜態常數 API_URL，並把它 export 出來，讓外面的程式可以引用它。

```ts
export class AppSettings {
    static API_URL = 'https://localhost:7087/api/';
}
```

## 建立 auth.service.ts

使用 angular cli 建立一支 Service 程式，用來呼叫後端 API。

```bash
$ npx ng g s services/auth
```

這支 service 程式中透過使用 httpClient 來連結 web api 端點（https://localhost:7087/api/Authenticate/login）來進行使用者資料驗證。


auth.service.ts 內容如下：

```ts {linenos=table,hl_lines=[1,3,5,12,"14-19"],linenostart=1}
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { AppSettings } from '../shared/helpers/appSettings';

const API_URL = AppSettings.API_URL + 'Authenticate/';

@Injectable({
  providedIn: 'root'
})
export class AuthService {

  constructor(private http: HttpClient) { }

  logint(email: string, password: string): Observable<any> {
    return this.http.post(API_URL + 'login', {
      email,
      password
    })
  }
}
```

## 建立 jwt-storage.service.ts

接著建立一支處理 jwt 存放在 Session Storage 中及由 Session Storage 讀取已完成驗證的 JWT 功能的 service 程式。

```bash
$ npx ng g s services/jwtStorage
```

jwt-storage.service.ts 內容如下：

```ts {linenos=table,hl_lines=[],linenostart=1}
import { Injectable } from '@angular/core';

const TOKEN_KEY = 'auth-token';

@Injectable({
  providedIn: 'root'
})
export class JwtStorageService {

  constructor() { }

  logout(): void {
    window.sessionStorage.clear();
  }

  isLogin() {
    return (sessionStorage.getItem(TOKEN_KEY));
  }

  saveToken(token: string): void {
    sessionStorage.removeItem(TOKEN_KEY);
    sessionStorage.setItem(TOKEN_KEY, token);
  }
}
```

## 完成 login page component 中的程式碼

login.component.ts 內容如下：

```ts {linenos=table,hl_lines=[],linenostart=1}
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { AuthService } from 'src/app/services/auth.service';
import { JwtStorageService } from 'src/app/services/jwt-storage.service';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent {
  hide: boolean = false;

  constructor(private fb: FormBuilder,
    private authService: AuthService,
    private tokenService: JwtStorageService) {
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

    const { email, password } = this.loginForm.getRawValue();

    this.authService.login(email, password).subscribe({
      next: data => {
        this.tokenService.saveToken(data.token);
      },
      error: err => {
        console.log('error');
      }
    });
  }
}
```

## 執行測試

在 Terminal 中輸入 npm start 來啟動程式，並開啟瀏覽器，輸入：（http://localhost:4200/login）

```bash
$ npm start
```

![image](https://user-images.githubusercontent.com/21993717/222092940-82b51fea-8527-4f5b-a543-34f562110174.png)

在登入前，先開啟 chrome 開發者工具頁（F12），切到 Application 頁籤，此刻會發現並沒有任何一筆資料存在 Session Storage 中

註：Session Storage 與 Local Storage 的功能及用法類似，最大的差別在 “Session Storage” 在該網頁關掉時會自動清楚，這個特性在測試環境中特別方便（不用每次測試都要手動去刪除 Local Storage 資料），所以在程式中是採用 Session Storage。

![image](https://user-images.githubusercontent.com/21993717/222093710-d80668b6-8af7-40d2-866d-7cd693d222cc.png)


登入成功後，可以查看一下瀏覽器中的 session storae 是否已存放對應的 JWT 的資料。

![image](https://user-images.githubusercontent.com/21993717/222095158-27b69273-47ac-4ce4-a63a-441941e1d0c0.png)

註: 在登入前請先到使用的 SQL server 資料庫中加入一筆使用者資料。


## 建立一支 interceptor 程式

程式到此已經可正常完成登入作業並取得由後端回傳的 Token。

接下來要回到本篇筆記的“正題“，如何透過 Angular Interceptor 來幫 每個 http request 加入 Token。

這支 Interceptor 程式用來在每個 http request 發出時，檢查若已登入檢核已完成，就會將儲存在 session storage 中的 JWT attach 到 http request Header 中，如此再搭配 web api 上的保護機制，就可以防止非法使用者去拜訪 API 上特定的 end point。

```bash
$ npx ng g interceptor interecpt/auth
```

auth.interceptor.ts 內容如下，程式中比較特間的地方就是，不能直接去 Interceptor 取得的 request 內容，要變更 request header時必須先使用 clone 來複製新的 request 再依需求來修改這個新的 request。

```ts {linenos=table,hl_lines=[],linenostart=1}
import { Injectable } from '@angular/core';
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor
} from '@angular/common/http';
import { Observable } from 'rxjs';
import { JwtStorageService } from 'src/app/services/jwt-storage.service';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {

  constructor(private tokenService: JwtStorageService) {}

  intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    let authReq = request;
    if (this.tokenService.isLogin()) {
      const token = this.tokenService.getToken();
      authReq = request.clone({
        setHeaders: {
          Authorization: `Bearer ${token}`
        }
      })
    }
    return next.handle(authReq);
  }
}
```

### 註冊 interecptor

新建立完成的 AuthInterceptor 必須在 app.module.ts 進行註冊：

```ts
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: LoadingSpinInterceptor,
      multi: true
    },
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    }
  ],
```

## 建立 Guard (路由保護) 程式

路由保護器(攔截器)　是一種 local 端保護機制。透過 Route Guard 的設置可以在使用者端就能效管理各個 route 的執行權限的控管。

```bash
$ npx ng g guard shared/guard/auth --implements CanActivate
```

authGuard 這個路由保護器的功能就是來攔截沒有正常登入系統的使用者不得執行特定的路由。在程式中透過 tokenService.getToken() 功能來讀取 token，若 session storeage 中沒有合法 token，表示目前還沒有正確的登入系統，並將程式指向登入功能。

auth.guard.ts 內容如下：

```ts {linenos=table,hl_lines=[],linenostart=1}
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';
import { JwtStorageService } from 'src/app/services/jwt-storage.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {

  constructor(private router: Router, private tokenService: JwtStorageService){}

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
      
      if (this.tokenService.isLogin()) return true;
      this.router.navigate(['/login']);
      return true;
  }
}
```

### 使用 Guard 來保護 ‘getdata` 路由

在 app-routing.module.ts 程式中加入路由保護機制。

```ts {linenos=table,hl_lines=[16],linenostart=1}
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { FlexboxComponent } from './components/flexbox/flexbox.component';
import { GetDataComponent } from './components/get-data/get-data.component';
import { LoginComponent } from './components/login/login.component';
import { NavigationComponent } from './navigation/navigation.component';
import { AuthGuard } from './shared/guard/auth.guard';

const routes: Routes = [
  {
    path: '', 
    component: NavigationComponent,
    children: [
      { path: '', redirectTo: 'flexbox', pathMatch: 'full'},
      { path: 'flexbox', component: FlexboxComponent },
      { path: 'getdata', component: GetDataComponent, canActivate: [AuthGuard] },
    ]
  },
  {
      path: 'login',
      component: LoginComponent,
  },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```


## 完善其他功能

最後再幫程式加入其他小功能來完善整個程式的功能及流暢度。

- 在 Navigator Header 右側加入下接選單，加入登入／登出功能
- 在 JwtStorageService 中加入相關功能來輔助程式可以支援動態顯示“登入／登出”
- 在 Login 功能中加入登入成功後自動跳轉回主畫面功能

在 navigation.component.html 中加入：

```html {linenos=table,hl_lines=[16],linenostart=1}
<!-- ... -->
      <button mat-icon-button [matMenuTriggerFor]="menu" aria-label="Options">
        <mat-icon>more_vert</mat-icon>
      </button>
      <mat-menu #menu="matMenu">
        <button mat-menu-item (click)="logout()" [disabled]="!(isLogin$ | async)">
          <mat-icon>logout</mat-icon>
          <span>Logout</span>
        </button>
        <button mat-menu-item (click)="login()" [disabled]="isLogin$ |async">
          <mat-icon>login</mat-icon>
          <span>Login</span>
        </button>
      </mat-menu>
<!-- ... -->
```

navigation.component.ts 內容調整如下：

```ts {linenos=table,hl_lines=[17,32],linenostart=1}
import { Component } from '@angular/core';
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';
import { Observable } from 'rxjs';
import { map, shareReplay } from 'rxjs/operators';
import { LoadingService } from '../services/loading.service';
import { JwtStorageService } from '../services/jwt-storage.service';
import { Router } from '@angular/router';

@Component({
  selector: 'app-navigation',
  templateUrl: './navigation.component.html',
  styleUrls: ['./navigation.component.scss']
})
export class NavigationComponent {
  isDarkTheme = false;
  loading$ = this._loadingService.loading$;
  isLogin$ = this.tokenService.isLogin$;

  isHandset$: Observable<boolean> = this.breakpointObserver.observe(Breakpoints.Handset)
    .pipe(
      map(result => result.matches),
      shareReplay()
    );

  constructor(private breakpointObserver: BreakpointObserver,
    private _loadingService: LoadingService,
    private tokenService: JwtStorageService,
    private router: Router) {}

  ngOnInit() {
    this.isDarkTheme = localStorage.getItem('theme') === "Dark" ? true : false;
    this.tokenService.checkLoginStatus();
  }
  
  storeThemeSelection() {
    localStorage.setItem('theme', this.isDarkTheme ? "Dark" : "Light");
  }

  logout() {
    this.tokenService.logout();
  }

  login() {
    this.router.navigate(['/login']);
  }
}
```

在 jwt-storage.service.ts 中加入可即時反應登入狀態的 observable:

```ts {linenos=table,hl_lines=[2,"10-18","26-18"],linenostart=1}
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable, of, switchMap } from 'rxjs';

const TOKEN_KEY = 'auth-token';

@Injectable({
  providedIn: 'root'
})
export class JwtStorageService {
  private _isLogin$ = new BehaviorSubject<boolean>(false);
  isLogin$: Observable<boolean> = this._isLogin$.pipe(
    switchMap(isLogin => {
      if (!isLogin) {
        return of(false);
      }
      return of(true);
    })
  )
  constructor() { }

  logout(): void {
    window.sessionStorage.clear();
    this._isLogin$.next(false);
  }

  checkLoginStatus(): void {
    this._isLogin$.next(!!sessionStorage.getItem(TOKEN_KEY));
  }

  isLogin() {
    return (sessionStorage.getItem(TOKEN_KEY));
  }

  getToken() {
    return sessionStorage.getItem(TOKEN_KEY);
  }

  saveToken(token: string): void {
    sessionStorage.removeItem(TOKEN_KEY);
    sessionStorage.setItem(TOKEN_KEY, token);
  }
}
```

## 最後的測試結果

啟動程式

```bash
$ npx ng s -o
```

![image](https://user-images.githubusercontent.com/21993717/222322530-46381fab-5b73-4b12-a865-e4eb50e7558f.png)


![image](https://user-images.githubusercontent.com/21993717/222322659-a3afe15f-082a-4564-93ac-7433b8ad3e01.png)

在尚未登入時去執行 "loging spinner" 功能時系統會自動跳轉至‘登入’畫面要求使用者先進行身份驗證，否則無法執行’被保護‘的這項功能。

![2023-03-02 11-26-35 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/222323421-ac61e284-14da-4d05-a76e-ceacc95fad95.png)

一旦登入成功，就可以順利執行 "loging spinner" 功能，同時“登入／登出”功能也能正確切換不同的使用狀態。

![image](https://user-images.githubusercontent.com/21993717/222323818-6bb97e98-9e6c-4760-8b4a-67830b050d3b.png)