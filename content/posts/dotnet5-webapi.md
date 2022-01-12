---
title: "ASP.NET Core 5 Web API"
date: 2022-01-11
draft: false
description: "使用 ASP.NET Core 5.0 建立 Web API"
tags: ["dotnet 5", "webapi"]
categories: ["webapi"]
---

# TodoApp(Donet 5): Sqlite + EF + Asp.net Code Generator

## 使用 dotnet cli 建立專案

```bash
$ dotnet --version # 檢查 dotnet 版本，目前版本為: 5.0.201

$ dotnet new webapi -n "Todo5" && cd "Todo5"
```

```bash
$ ls # 查看 專案檔案結構
```

![image](https://user-images.githubusercontent.com/21993717/148718867-37ffe9a1-ada1-486a-ab86-74832dc94796.png)

```bash
$ dotnet new gitignore # 使用 dotnet cli 來產生預設的 git ignore 檔案
```

![image](https://user-images.githubusercontent.com/21993717/148718998-4208993f-cbfb-4822-95bf-34b852a36ac1.png)

## 建立 git 初始版本

```bash
$ git init && git add . && git commit -m "Initial commit"
```

## 安裝本機工具

此方式安裝的工具，僅限本機存取(只針對目前的目錄和子目錄)， 首先透過 dotnet new tool-manifest 命令來產生工具資訊清單檔，再使用 dotnet tool install 來安裝各式工具程式。這樣的方式好處是在專案若多人協助方式時，則可利用 dotnet tool restore 命令將紀錄在 .config/dotnet-tools.json 的工具資訊清單檔重建在不同協助人員的電腦中。

```bash
$ dotnet new tool-manifest #會產生 .config/dotnet-tools.json 檔案
$ dotnet tool install dotnet-ef --version 5.0.13 #使用 local 安裝方式來安裝 Entity Framework 工具
$ dotnet tool install dotnet-aspnet-codegenerator --version 5.0.2 #使用 local 安裝方式來安裝 Code Generator 工具

$  cat .\.config\dotnet-tools.json # 查看安裝上述二項工具後的設定資訊
```

```json
{
  "version": 1,
  "isRoot": true,
  "tools": {
    "dotnet-ef": {
      "version": "5.0.13",
      "commands": ["dotnet-ef"]
    },
    "dotnet-aspnet-codegenerator": {
      "version": "5.0.2",
      "commands": ["dotnet-aspnet-codegenerator"]
    }
  }
}
```

## 安裝程式使用的相關套件

```bash
$ dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 5.0.13 #Sqlite 使用的套件
$ dotnet add package Microsoft.EntityFrameworkCore.Tools --version 5.0.13 #使用 dotnet Entity Framework時必須安裝此套件
$ dotnet add package Microsoft.EntityFrameworkCore.Design --version 5.0.13 #使用 dotnet Entity Framework時必須安裝此套件
$ dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 5.0.13 #使用 dotnet Entity Framework時必須安裝此套件

$ dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 5.0.2 #搭配 dotnet-aspnet-codegenerator 使用
```

安裝的程式套件資訊紀錄在 "專案".csproj 檔案中

```bash
$ cat .\Todo5.csproj #查看 安裝套件的相關設定值
```

```json
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
    <RootNamespace>_5Todo</RootNamespace>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="5.0.13">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="5.0.13" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="5.0.13" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="5.0.13">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="5.0.2" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.6.3" />
  </ItemGroup>

</Project>
```

## 建立 git 新版本

```bash
$ git add . && git commit -m "Add EFCore NuGet packages"
```

## 新增 database context (自動產生)

```bash
$ dotnet ef dbcontext scaffold "Data Source=app.db; Cache=Shared" Microsoft.EntityFrameworkCore.Sqlite -c ApiDbContext -o Data #在專案目錄 ./Data 子目錄下新建立一個 ApiDbContext.cs 的 DB Context file

$ dotnet run #可使用 dotnet watch run 命令來自動監控程式碼的變動
```

![image](https://user-images.githubusercontent.com/21993717/148719457-836a1de0-9bf8-4e5a-9126-1da820823c26.png)

## 開啟瀏覽器查看以下網址 "https://localhost:5001/WeatherForecast"

```json
[
  {
    "date": "2022-01-11T11:44:43.1899069+08:00",
    "temperatureC": -6,
    "temperatureF": 22,
    "summary": "Hot"
  },
  {
    "date": "2022-01-12T11:44:43.190114+08:00",
    "temperatureC": 28,
    "temperatureF": 82,
    "summary": "Balmy"
  },
  {
    "date": "2022-01-13T11:44:43.1901167+08:00",
    "temperatureC": 23,
    "temperatureF": 73,
    "summary": "Hot"
  },
  {
    "date": "2022-01-14T11:44:43.1901169+08:00",
    "temperatureC": 42,
    "temperatureF": 107,
    "summary": "Warm"
  },
  {
    "date": "2022-01-15T11:44:43.1901171+08:00",
    "temperatureC": 22,
    "temperatureF": 71,
    "summary": "Cool"
  }
]
```

## 開啟瀏覽器查看以下 Swagger 網址 "https://localhst:5001/swagger/index.html"

![image](https://user-images.githubusercontent.com/21993717/148719637-bae91a1c-9d56-4cf7-a45f-a577b95a5f50.png)

![image](https://user-images.githubusercontent.com/21993717/148716406-a73dd4c1-7dac-4e39-8947-3a81b6680278.png)

## 建立另一個 git 新版本

```bash
$ git add . && git commit -m "Create dbcontext classes using dotnet-ef"
```

## Open VS Code

```bash
$ Code .
```

## 在 Models 目錄下新增一個 model(模型) class - ItemData

```cs
namespace Todo5.Models
{
    public class ItemData
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Details { get; set; }
        public bool Done { get; set; }
    }
}
```

## 在 ApiDbContext.cs 中宣告一個 ItemData table

`public DbSet<ItemData> ItemDatas { get; set; }`

```cs
using System;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata;
using Todo5.Models;

#nullable disable

namespace Todo5.Data
{
    public partial class ApiDbContext : DbContext
    {
        public DbSet<ItemData> ItemDatas { get; set; }
        public ApiDbContext()
        {
        }
        //...
    }
}
```

## 在 appsettings.json 檔案中新增 ConnectionString 設定資料

```json
"ConnectionStrings": {
  "DefaultConnection": "Data Source=app.db;Cache=Shared"
},
```

## 在 Startup.cs 檔案中註冊 database context (連絡到 Sqlite DB)

```cs
services.AddDbContext<ApiDbContext>(options =>
  options.UseSqlite(
    Configuration.GetConnectionString("DefaultConnection")
  )
);
```

```cs
namespace TodoApp
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContext<ApiDbContext>(options =>
                options.UseSqlite(
                    Configuration.GetConnectionString("DefaultConnection")
                    )
            );

            services.AddControllers();
            // ...
        }
    };
}
```
## 移除預設產生的樣本程式碼的 WeatherForecast.cs & WeatherForecastController.cs

## Add the initial migration to create the database

```bash
  $ dotnet build
  $ dotnet ef migrations add "Initial Migrations"
  $ dotnet ef database update (在根目錄產生 app.db sqlite database)
```

## 使用 ASPNET Codegenerator 自動產生 Todo Controller

```bash
$ dotnet aspnet-codegenerator controller -name TodoController -async -api -m ItemData -dc ApiDbContext -outDir Controllers
```

## 執行 Swagger

開啟瀏覽器查看以下網址 "https://localhst:5001/swagger/index.html"

![image](https://user-images.githubusercontent.com/21993717/148916660-59563cfd-0e8d-4628-ad49-42ef9330a0dc.png)

![image](https://user-images.githubusercontent.com/21993717/148928836-3a079601-e35f-4898-8fc4-963d647f77ab.png)

透過 POST Method 新增一筆資料

![image](https://user-images.githubusercontent.com/21993717/148929217-339e3f86-972c-414b-a015-61ebe3118254.png)
使用 GET Method 查詢資料
![image](https://user-images.githubusercontent.com/21993717/148928509-f8076263-95d9-4f75-9c88-9b38f1cd12e9.png)

![image](https://user-images.githubusercontent.com/21993717/148928685-84833fad-82b9-4d40-ac43-b32817d880e5.png)
