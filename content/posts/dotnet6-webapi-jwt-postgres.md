---
title: "使用 .NET 6.0 進行 JWT 身份驗證的 ASP.NET Web Api。並使用 Microsoft Identity 框架來存儲使用者和角色等資料（使用 PostgresSQL）"
date: 2022-06-02
description: "採用 PostgresSQL 為後端資料庫，使用 .NET 6.0 進行 JWT 身份驗證的 ASP.NET Web Api。並使用 Microsoft Identity 框架來存儲使用者和角色等資料"
tags: ["dotnet 6", "webapi", "jwt", "docker", "postgres", "openapi", "swagger"]
categories: ["webapi"]
---
*[github Source code](https://github.com/calvinegs/dotnet6-webapi-jwt-postgres.git)*


在之前的筆記中 *[使用 .NET 6.0 進行 JWT 身份驗證的 ASP.NET Web Api。並使用 Microsoft Identity 框架來存儲使用者和角色等資料](https://calvinegs.github.io/posts/dotnet6-minimalapi-jwt/)* 已仔細的紀錄如何使用 .Net 6.0 來建置一個支援 JWT ＆ Identity framework 的 WebApi 網站應用程式，在該篇筆記中所使用的資料庫是 `MS SQL 2019`，而這篇筆記裡將紀錄如何將它改成支援 PostgresSQL。

若你還沒有安裝 PostgresSQL 可以參考這筆記先將資料庫管理系統備妥 *[使用 Docker 執行 PostgresSQL 與 pgAdmin](https://calvinegs.github.io/posts/docker-postgres-pgadmin/)*

## 專案完成後的檔案結構

```
./專案目錄
├── .config/
│   └── dotnet-tools.json
├── .vscode/
│   ├── launch.js
│   └── tasks.json
├── Controller/
│   ├── AuthenticateController.cs
│   ├── TodoController.cs
│   └── WeatherForecast.cs
├── Data/
│   └── ApiDbContext.cs
├── Migrations/
├── Models/
│   ├── AuthenticateData.cs
│   └── ItemData.cs
├── obj/
├── Properties/
│   └── launchSettings.json
├── .gitignore
├── appsettings.Development.json
├── appsettings.json
├── dotnet6-webapi-jwt.csproj
├── global.json
├── Program.cs
├── README.md
└── WeatherForecast.cs
```

## 專案完成後所提供的 API 端點

|Methods|Urls|Actions|
|-------|-----------------------------|-------------------------------------------------|
|POST| /api/Authenticate/login|註冊新使用者帳號|
|POST| /api/Authenticate/register|使用者帳號登入|
|POST| /api/Authenticate/register-admin|管理者帳號登入|
|GET| /api/Todos|get all Todos|
|POST| /api/Todos|add New Todo|
|GET| /api/Todos/:id|get Todo by id|
|PUT| /api/todos/:id|update Todo by id|
|DELETE| /api/todos/:id|remove Todo by id|

## git clone Example Code

先 git clone ｀使用 .NET 6.0 進行 JWT 身份驗證的 ASP.NET Web Api。並使用 Microsoft Identity 框架來存儲使用者和角色等資料｀ 這篇筆記的 Source code

```bash
$ git clone https://github.com/calvinegs/dotnet6-webapi-jwt.git dotnet6-webapi-postgress
```

## restore 相依的套件

```bash
$ cd dotnet6-webapi-postgress

$ dotnet restore

  正在判斷要還原的專案...
  已還原 /home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/dotnet6-webapi-jwt.csproj (1.12 sec 內)。
```

## 除移、SQL Server 套件改使用 Postgres 相關套件

```bash
$ dotnet remove package Microsoft.EntityFrameworkCore.SqlServer
info : 正在從專案 '/home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/dotnet6-webapi-jwt.csproj' 中移除套件 'Microsoft.EntityFrameworkCore.SqlServer' 的 PackageReference。

$ dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
  正在判斷要還原的專案...
  Writing /tmp/tmpU1vZfj.tmp
info : 正在將套件 'Npgsql.EntityFrameworkCore.PostgreSQL' 的 PackageReference 新增至專案 '/home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/dotnet6-webapi-jwt.csproj'。
info :   GET https://api.nuget.org/v3/registration5-gz-semver2/npgsql.entityframeworkcore.postgresql/index.json
info :   OK https://api.nuget.org/v3/registration5-gz-semver2/npgsql.entityframeworkcore.postgresql/index.json 858 毫秒
info : 正在還原 /home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/dotnet6-webapi-jwt.csproj 的封裝...
info : 套件 'Npgsql.EntityFrameworkCore.PostgreSQL' 與專案 '/home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/dotnet6-webapi-jwt.csproj' 中的所有架構相容。
info : 已將套件 'Npgsql.EntityFrameworkCore.PostgreSQL' 版本 '6.0.4' 的 PackageReference 新增至檔案 '/home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/dotnet6-webapi-jwt.csproj'。
info : 正在將資產檔案寫入磁碟。路徑: /home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/obj/project.assets.json
log  : 已還原 /home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/dotnet6-webapi-jwt.csproj (371 ms 內)。
```


## 使用 VSCode 開啟專案

```bash
$ code .
```

開啟 dotnet6-webapi-jwt.csproj 專案檔可發現已採用 ｀Npgsql.EntityFrameworkCore.PostgreSQL｀套件。

```xml {linenos=table,hl_lines=[22]}
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <RootNamespace>dotnet6_webapi_jwt</RootNamespace>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="6.0.5" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="6.0.5" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="6.0.5">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="6.0.5">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="6.0.5" />
    <PackageReference Include="Npgsql.EntityFrameworkCore.PostgreSQL" Version="6.0.4" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.2.3" />
  </ItemGroup>

</Project>
```

### 修改 DbContext 參數

修改 Program.cs 中 DbContext 的參數設定

```cs
builder.Services.AddDbContext<ApiDbContext>(
    options => options.UseSqlServer(
        _configuration.GetConnectionString("ConnStr")
    )
);
```

> 改成

```cs
builder.Services.AddDbContext<ApiDbContext>(
    options => options.UseNpgsql(
        _configuration.GetConnectionString("ConnStr")
    )
);
```

### 修改 appsettings.json 中的 Conection String


```json
  "ConnectionStrings": {
    "ConnStr": "Data Source=localhost;Initial Catalog=TestDB;User ID=SA;Password=Sql@12345"
  },
```

> 改成

```json
  "ConnectionStrings": {
    "ConnStr": "User ID =docker;Password=docker;Server=localhost;Port=5432;Database=pg_testdb; Integrated Security=true;Pooling=true"
  },
```

### 將原有專案的 Migrations 子目錄移除

```bash
$ rm -f ./Migrations

$ dotnet build # 重新建置程式
Microsoft (R) Build Engine for .NET 17.2.0+41abc5629 版
Copyright (C) Microsoft Corporation. 著作權所有，並保留一切權利。

  正在判斷要還原的專案...
  所有專案都在最新狀態，可進行還原。
  dotnet6-webapi-jwt -> /home/egs/cal-data/tech-test/webapi/test/dotnet6-webapi-postgress/bin/Debug/net6.0/dotnet6-webapi-jwt.dll

建置成功。
    0 個警告
    0 個錯誤

經過時間 00:00:02.21
```

### 新增一個資料遷移
```bash
$ dotnet ef Migrations add "Initial Migrations"
Build started...
Build succeeded.
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 6.0.5 initialized 'ApiDbContext' using provider 'Npgsql.EntityFrameworkCore.PostgreSQL:6.0.4+6cb649128e3e7aa8eddd77dfa75b34bad51e6e94' with options: None
Done. To undo this action, use 'ef migrations remove'
```

### 將資料遷移更新至資料庫

```
$ dotnet ef database update

![2022-06-02 18-07-55](https://user-images.githubusercontent.com/21993717/171607590-cd6e06ad-7ae7-47d2-bfba-afe9aab0d11e.png)
```

> 資料遷移成功後會在資料庫中自動産生 Identity Framework 所使用的資料表，而自定的 Model 也會一併産生新資料表

![2022-06-02 18-07-55](https://user-images.githubusercontent.com/21993717/171634977-ae819471-bbdd-4385-be7c-c0a6b538304f.png)


## 執行結果

![2022-06-02 18-15-28](https://user-images.githubusercontent.com/21993717/171635449-3e4fbd95-03ea-404f-b674-62437d5d3d5e.png)