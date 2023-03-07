---
title: "使用 .NET 7.0 建立使用 以 JWT 身份驗證機制的 ASP.NET Web Api（並使用 Microsoft Identity 框架來存儲使用者和角色等資料）"
date: 2023-03-06
description: "本篇將要紀錄如何使用　.NET 7.0 來建立 一個可方便擴展 Web API"
tags: ["dotnet 7", "webapi", "jwt", "docker", "PostgreSQL DB", "openapi", "swagger"]
categories: ["webapi"]
---
*[github Source code](https://github.com/calvinegs/dotnet7-webapi-jwt.git)*

本文將記錄如何一步步從無到有，使用 Dotnet Core 7.0 建立 ASP.NET Core Web API，其中將會使用到下列技術:

- Dotnet CLI
- Entity Framework 7.0
- Json Web Token
- PostgreSQL DB (Docker Version)
- ASP.NET Core Generator

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
│   └── TodoList.cs
├── obj/
├── Properties/
│   └── launchSettings.json
├── .gitignore
├── appsettings.Development.json
├── appsettings.json
├── dotnet7-webapi-jwt.csproj
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
|PUT| /api/Todos/:id|update Todo by id|
|DELETE| /api/Todos/:id|remove Todo by id|

## 建置新專案

### dotnet 版本管理檢查

你的 Local 可能已安裝了多個版本的 dotnet 環境，使用以下指令來檢查目前的 dotnet 版本

```bash
$ dotnet --version  # 顯示目前所使用的版本
7.0.201 # 預設是最新的版本
```

### 使用 dotnet cli 建立新專案

```bash
$ dotnet new webapi -o dotnet7-webapi-jwt
歡迎使用 .NET 7.0!
---------------------
SDK 版本: 7.0.201

遙測
---------
.NET 工具會收集使用資料，協助我們改進您的體驗。資料會由 Microsoft 收集，並分享給社群使用。您可以選擇退出遙測，只要使用您慣用的殼層，將 DOTNET_CLI_TELEMETRY_OPTOUT 環境變數設定為 '1' 或 'true' 即可。

閱讀更多有關 .NET CLI 工具遙測的內容: https://aka.ms/dotnet-cli-telemetry

----------------
已安裝 ASP.NET Core HTTPS 開發憑證。
若要信任憑證，請執行 'dotnet dev-certs https --trust' (僅限 Windows 與 macOS )。
深入了解 HTTPS: https://aka.ms/dotnet-https
----------------
撰寫第一個應用程式: https://aka.ms/dotnet-hello-world
了解全新功能: https://aka.ms/dotnet-whats-new
探索文件: https://aka.ms/dotnet-docs
於 GitHub 回報問題和尋找來源: https://github.com/dotnet/core
Use 'dotnet --help' 查看可用的命令或瀏覽: https://aka.ms/dotnet-cli
--------------------------------------------------------------------------------------
範本「ASP.NET Core Web API」已成功建立。

正在處理建立後的動作...
正在還原 /home/egs/cal-data/Sources/nhis/dotnet7-webapi-jwt/dotnet7-webapi-jwt.csproj:
  正在判斷要還原的專案...
  已還原 /home/egs/cal-data/Sources/nhis/dotnet7-webapi-jwt/dotnet7-webapi-jwt.csproj (4.93 sec 內)。
還原成功。
```
新專案

```
$ cd dotnet7-webapi-jwt
$ ls -al
總用量 40
drwxrwxr-x 5 egs egs 4096  3月  6 10:23 .
drwxrwxr-x 5 egs egs 4096  3月  6 10:22 ..
-rw-rw-r-- 1 egs egs  127  3月  6 10:22 appsettings.Development.json
-rw-rw-r-- 1 egs egs  151  3月  6 10:22 appsettings.json
drwxrwxr-x 2 egs egs 4096  3月  6 10:22 Controllers
-rw-rw-r-- 1 egs egs  463  3月  6 10:22 dotnet7-webapi-jwt.csproj
drwxrwxr-x 2 egs egs 4096  3月  6 10:23 obj
-rw-rw-r-- 1 egs egs  557  3月  6 10:22 Program.cs
drwxrwxr-x 2 egs egs 4096  3月  6 10:22 Properties
-rw-rw-r-- 1 egs egs  267  3月  6 10:22 WeatherForecast.cs
```

### dotnet 版本管理

```bash
$ dotnet --list-sdks    # 顯示已安裝的 sdk 版本資訊
5.0.408 [/usr/share/dotnet/sdk]
6.0.406 [/usr/share/dotnet/sdk]
7.0.201 [/usr/share/dotnet/sdk]

$ dotnet --list-runtimes
Microsoft.AspNetCore.App 5.0.17 [/usr/share/dotnet/shared/Microsoft.AspNetCore.App]
Microsoft.AspNetCore.App 6.0.14 [/usr/share/dotnet/shared/Microsoft.AspNetCore.App]
Microsoft.AspNetCore.App 7.0.3 [/usr/share/dotnet/shared/Microsoft.AspNetCore.App]
Microsoft.NETCore.App 5.0.17 [/usr/share/dotnet/shared/Microsoft.NETCore.App]
Microsoft.NETCore.App 6.0.14 [/usr/share/dotnet/shared/Microsoft.NETCore.App]
Microsoft.NETCore.App 7.0.3 [/usr/share/dotnet/shared/Microsoft.NET# .App]
```

由於 dotnet 版本演化滿快的，所以會建議在專案目錄中要指定使用 SDK 的版本，以免當你又安裝了更新的版本（如8.0）後程式執行出問題。

```bash
$ dotnet new globaljson --sdk-version 7.0.201
範本「global.json 檔案」已成功建立。

$ cat global.json
{
  "sdk": {
    "version": "7.0.201"
  }
}
```

### 版本控管

使用 dotnet cli 來產生預設的 git ignore 檔案

```bash
$ dotnet new gitignore
```

建立 git 初始版本

```bash
$ git init && git add . && git commit -m "Initial commit"
```

### 安裝本機工具

此方式安裝的工具，僅限本機存取(只針對目前的目錄和子目錄)， 首先透過 dotnet new tool-manifest 命令來產生工具資訊清單檔，再使用 dotnet tool install 來安裝各式工具程式。這樣的方式好處是在專案若多人協助方式時，則可利用 dotnet tool restore 命令將紀錄在 ./.config/dotnet-tools.json 的工具資訊清單檔重建在不同協助人員的電腦中。

```bash
$ dotnet new tool-manifest #會產生 .config/dotnet-tools.json 檔案
$ dotnet tool install --local dotnet-ef #使用 local 安裝方式來安裝 Entity Framework 工具
您可使用下列命令，從此目錄叫用工具: 'dotnet tool run dotnet-ef' 或 'dotnet dotnet-ef'。
已成功安裝工具 'dotnet-ef' (版本 '7.0.3')。項目已新增至資訊清單檔 /home/egs/cal-data/Sources/nhis/dotnet7-webapi-jwt/.config/dotnet-tools.json。

$ dotnet tool install --local dotnet-aspnet-codegenerator #使用 local 安裝方式來安裝 Code Generator 工具
您可使用下列命令，從此目錄叫用工具: 'dotnet tool run dotnet-aspnet-codegenerator' 或 'dotnet dotnet-aspnet-codegenerator'。
已成功安裝工具 'dotnet-aspnet-codegenerator' (版本 '7.0.4')。項目已新增至資訊清單檔 /home/egs/cal-data/Sources/nhis/dotnet7-webapi-jwt/.config/dotnet-tools.json。

$ cat ./.config/dotnet-tools.json 
{
  "version": 1,
  "isRoot": true,
  "tools": {
    "dotnet-ef": {
      "version": "7.0.3",
      "commands": [
        "dotnet-ef"
      ]
    },
    "dotnet-aspnet-codegenerator": {
      "version": "7.0.4",
      "commands": [
        "dotnet-aspnet-codegenerator"
      ]
    }
  }
}
```

### 安裝程式使用的相關套件

```bash
$ dotnet add package Microsoft.EntityFrameworkCore.Tools #使用 dotnet Entity Framework 時必須安裝此套件
$ dotnet add package Microsoft.EntityFrameworkCore.Design #使用 dotnet Entity Framework 時必須安裝此套件

$ dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore #使用 Identity Framework 時必須安裝此套件
$ dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL #使用 PostgreSQL DB 當後端資料庫須安裝此套件
$ dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer #要使用 Token Base 權限管理須安裝此套件

$ dotnet add package Microsoft.EntityFrameworkCore.SqlServer #使用 dotnet aspnet-codegenerator 時必須安裝此套件 
$ dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design #搭配 dotnet-aspnet-codegenerator 使用
$ dotnet add package System.Configuration.ConfigurationManager  #搭配 dotnet-aspnet-codegenerator 使用
```

安裝的程式套件資訊紀錄在 "專案".csproj 檔案中

```bash
$ cat dotnet7-webapi-jwt.csproj #查看 安裝套件的相關設定值
```

```xml {linenos=table,hl_lines=["11-14","18-19","23-26"]}
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net7.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <RootNamespace>dotnet7_webapi_jwt</RootNamespace>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="7.0.3" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.entityframeworkCore" Version="7.0.3" />
    <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="7.0.3" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="7.0.3">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="7.0.3" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="7.0.3">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.VisualStudio.Web.codeGeneration.Design" Version="7.0.4" />
    <PackageReference Include="Npgsql.entityFrameworkCore.PostgreSQL" Version="7.0.3" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.4.0" />
    <PackageReference Include="system.Configuration.ConfigurationManager" Version="7.0.0" />
  </ItemGroup>

</Project>

```

### 建立 git 新版本

```bash
$ git add . && git commit -m "Add EFCore NuGet packages"
```

## 執行程式

```bash
$ dotnet watch
```

![image](https://user-images.githubusercontent.com/21993717/223012544-e88e0fb2-6d40-4fe5-8233-b9ae9a702912.png)

![image](https://user-images.githubusercontent.com/21993717/223012745-4c2b8f14-2bb6-41cd-87e7-3f9ae6362cbd.png)

![image](https://user-images.githubusercontent.com/21993717/223012875-d96bf10f-d83e-4991-9220-74306cb08c66.png)


打開 VS Code

```bash
$ code .
```

目前産生的程式架構

![image](https://user-images.githubusercontent.com/21993717/223013649-3ec7ac4a-1ef4-42f6-b127-64a7965ae227.png)


## 設置使用 Entity Framework相關設定

### 新增 database context (自動產生)

使用 dotnet ef 工具在專案目錄 ./Data 子目錄下新建立一個 ApiDbContext.cs 的 DB Context file

註：在使用前先把 後端資料庫 環境備妥，安裝 PostGreSQL DB 可參考此篇筆紀　*[使用 Docker 執行 PostgresSQL ](https://calvinegs.github.io/posts/docker-postgres-pgadmin/)*

```bash
$ dotnet ef dbcontext scaffold "User ID =docker;Password=docker;Server=localhost;Port=5432;Database=pg_testdb; Integrated Security=true;Pooling=true" Npgsql.entityFrameworkCore.PostgreSQL -c ApiDbContext -o Data

Build started...
Build succeeded.
To protect potentially sensitive information in your connection string, you should move it out of source code. You can avoid scaffolding the connection string by using the Name= syntax to read it from configuration - see https://go.microsoft.com/fwlink/?linkid=2131148. For more guidance on storing connection strings, see http://go.microsoft.com/fwlink/?LinkId=723263.
```

![image](https://user-images.githubusercontent.com/21993717/223016661-69c446de-4f58-4eaf-af8d-8718b72cfd76.png)


```cs
using System;
using System.Collections.Generic;
using Microsoft.EntityFrameworkCore;

namespace dotnet7_webapi_jwt.Data;

public partial class ApiDbContext : DbContext
{
    public ApiDbContext()
    {
    }

    public ApiDbContext(DbContextOptions<ApiDbContext> options)
        : base(options)
    {
    }

    // protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    //     => optionsBuilder.UseNpgsql("User ID =docker;Password=docker;Server=localhost;Port=5432;Database=pg_testdb; Integrated Security=true;Pooling=true");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        OnModelCreatingPartial(modelBuilder);
    }

    partial void OnModelCreatingPartial(ModelBuilder modelBuilder);
}
```

在 appsettings.json 檔案中加入 Connection String
```bash {{linenos=table,hl_lines=[9,10]}}
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "ConnStr": "User ID =docker;Password=docker;Server=localhost;Port=5432;Database=pg_testdb; Integrated Security=true;Pooling=true"
  }
}
```

### 使用 Asp.Net Core Identity framework 來管理使用者使用權限

ASP.NET Core Identity:

- 支援使用者介面 (UI) 登入功能的 API。
- 管理使用者、密碼、設定檔資料、角色、宣告、權杖、電子郵件確認等。

ASP.Net Core Identity Framework 是一個方便且還完善的使用權限管理架構。

### 將相關 Asp.Net Core Identity framework 功能注入到 container 中

除了安裝相關套件外，還要調整相關程式:

- 在 Program.cs 檔案中將加入以下程式碼 (before services.AddControllers())

```cs
ConfigurationManager _configuration = builder.Configuration;

// Add services to the container.
builder.Services.AddDbContext<ApiDbContext>(
    options => options.UseSqlServer(
        _configuration.GetConnectionString("ConnStr")
    )
);
builder.Services.AddIdentity<IdentityUser, IdentityRole>()
    .AddEntityFrameworkStores<ApiDbContext>()
    .AddDefaultTokenProviders();
```

- 使用 AspNetCore Identity，則 DataContext (ApiDbContext.cs 中) 必須要繼承
  IdentityDbContext， 同時 Model creationg 時要改成呼叫 base.OnModelCreation

```cs {linenos=table,hl_lines=[1,12],linenostart=1}
public partial class ApiDbContext : IdentityDbContext<IdentityUser>
{
    public ApiDbContext()
    {
    }

// ...

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
      // OnModelCreatingPartial(modelBuilder);
      base.OnModelCreating(modelBuilder);
    }
}
```

### 新增一個 entity framework 遷移 並 更新資料庫

完成上述程式調整後，來執行資料庫遷移(migrations)

```bash
$ dotnet ef migrations add "Add Identity Framework"

Build started...
Build succeeded.
Done. To undo this action, use 'ef migrations remove'

$ dotnet ef database update
```

在　dotnet ef migrations add "Add Identity Framework" 指令完成後，可以在專案目錄下發生産生新的子目錄 Migrations，並有三個新檔案

![image](https://user-images.githubusercontent.com/21993717/223018638-a1478c5a-6fff-4959-b339-f74beba970ff.png)

在 dotnet ef database update 指令完成後，PostgreSQL pg_testdb 資料庫中產生 Identity Framework 會使用到的資料表

![image](https://user-images.githubusercontent.com/21993717/223018710-dfe253c8-a9b4-43b2-ba04-77d9cf8ad458.png)

### 建立 git 新版本

```bash
$ git add . && git commit -m "新增一個 entity framework 遷移 並 更新資料庫"
```

## 使用 Jason Web Token 

### 在 appsettings.json 中自定JWT實作會使用到的設定值
```json {linenos=table,hl_lines=["7-12]}
{
// ...  
  "ConnectionStrings": {
    "ConnStr": "User ID =docker;Password=docker;Server=localhost;Port=5432;Database=pg_testdb; Integrated Security=true;Pooling=true"
  },
  "JwtSettings": {
    "ValidIssuer": "Dotnet7WebApiDemo",
    "ValidAudience": "Dotnet7WebApiDemo",
    "Secret": "Dotnet7 WebApi Demo. Using Json Web Token Technology to keep user info."
  }
}
```

### 新增 使用者註冊和登入時使用的 Data model class (Models/AuthenticateData.cs)

```cs {linenos=table,hl_lines=[],linenostart=1}
using System.ComponentModel.DataAnnotations;

namespace dotnet7_webapi_jwt.Models;

public class Response
{
    public string? Status { get; set; }
    public string? Message { get; set; }
}

public class LoginModel
{
    [EmailAddress]
    [Required(ErrorMessage = "Eamil Address is required")]
    public string? Email { get; set; }

    [Required(ErrorMessage = "Password is required")]
    public string? Password { get; set; }
}

public class RegisterModel
{
    [Required(ErrorMessage = "User Name is required")]
    public string? Username { get; set; }

    [EmailAddress]
    [Required(ErrorMessage = "Email Address is required")]
    public string? Email { get; set; }

    [Required(ErrorMessage = "Password is required")]
    public string? Password { get; set; }
}

public static class UserRoles
{
    public const string Admin = "Admin";
    public const string User = "User";
}
```

### 新增 註冊和登入邏輯 (Controllers/AuthenticateControll.cs)

```cs {linenos=table,hl_lines=[],linenostart=1}
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using dotnet7_webapi_jwt.Models;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Tokens;

namespace dotnet7_webapi_jwt.Controllers;

[Route("api/[controller]")]
[ApiController]
public class AuthenticateController : ControllerBase
{
    private readonly UserManager<IdentityUser> _userManager;
    private readonly RoleManager<IdentityRole> _roleManager;
    private readonly IConfiguration _configuration;

    public AuthenticateController(
        UserManager<IdentityUser> userManager,
        RoleManager<IdentityRole> roleManager,
        IConfiguration configuration)
    {
        _userManager = userManager;
        _roleManager = roleManager;
        _configuration = configuration;
    }

    [HttpPost]
    [Route("login")]
    public async Task<IActionResult> Login([FromBody] LoginModel userModel)
    {
        var user = await _userManager.FindByEmailAsync(userModel.Email);
        if (user != null && await _userManager.CheckPasswordAsync(user, userModel.Password))
        {
            var userRoles = await _userManager.GetRolesAsync(user);

            var claims = new List<Claim>
                {
                    new Claim(ClaimTypes.Name, user.UserName),
                    new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
                };

            foreach (var userRole in userRoles)
            {
                claims.Add(new Claim(ClaimTypes.Role, userRole));
            }

            var token = CreateToken(claims);

            return Ok(new
            {
                token = new JwtSecurityTokenHandler().WriteToken(token),
                expiration = token.ValidTo
            });
        }
        return Unauthorized();
    }

    [HttpPost]
    [Route("register")]
    public async Task<IActionResult> Register([FromBody] RegisterModel model)
    {
        var userExists = await _userManager.FindByEmailAsync(model.Email);
        if (userExists != null)
            return StatusCode(StatusCodes.Status500InternalServerError, new Response { 
                Status = "Error", Message = "User already exists!" 
            });

        IdentityUser user = new()
        {
            Email = model.Email,
            SecurityStamp = Guid.NewGuid().ToString(),
            UserName = model.Username
        };
        var result = await _userManager.CreateAsync(user, model.Password);
        if (!result.Succeeded)
            return StatusCode(StatusCodes.Status500InternalServerError, new Response { 
                Status = "Error", Message = "User creation failed! Please check user details and try again." 
            });

        return Ok(new Response { Status = "Success", Message = "User created successfully!" });
    }

    [HttpPost]
    [Route("register-admin")]
    public async Task<IActionResult> RegisterAdmin([FromBody] RegisterModel model)
    {
        var userExists = await _userManager.FindByEmailAsync(model.Email);
        if (userExists != null)
            return StatusCode(StatusCodes.Status500InternalServerError, new Response { 
                Status = "Error", Message = "User already exists!" 
            });

        IdentityUser user = new()
        {
            Email = model.Email,
            SecurityStamp = Guid.NewGuid().ToString(),
            UserName = model.Username
        };
        var result = await _userManager.CreateAsync(user, model.Password);
        if (!result.Succeeded)
            return StatusCode(StatusCodes.Status500InternalServerError, new Response { 
                Status = "Error", Message = "User creation failed! Please check user details and try again." 
            });

        if (!await _roleManager.RoleExistsAsync(UserRoles.Admin))
            await _roleManager.CreateAsync(new IdentityRole(UserRoles.Admin));
        if (!await _roleManager.RoleExistsAsync(UserRoles.User))
            await _roleManager.CreateAsync(new IdentityRole(UserRoles.User));

        if (await _roleManager.RoleExistsAsync(UserRoles.Admin))
        {
            await _userManager.AddToRoleAsync(user, UserRoles.Admin);
        }
        if (await _roleManager.RoleExistsAsync(UserRoles.Admin))
        {
            await _userManager.AddToRoleAsync(user, UserRoles.User);
        }
        return Ok(new Response { Status = "Success", Message = "User created successfully!" });
    }

    private JwtSecurityToken CreateToken(List<Claim> claims)
    {
        var secretkey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(
            _configuration.GetValue<string>("JwtSettings:Secret")));    // _configuration.GetSection("JwtSettings:Secret").Value)

        var credentials = new SigningCredentials(secretkey, SecurityAlgorithms.HmacSha512Signature);

        var token = new JwtSecurityToken(   // 亦可使用　SecurityTokenDescriptor　來産生 Token
            issuer: _configuration.GetValue<string>("JwtSettings:ValidIssuer"),
            audience: _configuration.GetValue<string>("JwtSettings:ValidAudience"),
            expires: DateTime.Now.AddDays(1),
            claims: claims,
            signingCredentials: credentials);

        return token;
    }
}
```

## 有關實作 JWT 的流程

透過 JWT 的實作可以讓你的專案實現 Token-base 的身份驗證與授權。 （Json Web Token) 實作的過程大致可以分成三個部分:
- 在登入成功後産生合法的 JWT Token
- 每次收到 request 時驗證是否為合法有效的 JWT Token
- 在特定 API Endpoint 上驗證是否帶有 “合法有效的 JWT Token”，以達到權限管理的需求

### 産生合法的 Jason Web Token

在上述 AuthenticateController.cs 程式中，我們建立一個 CreateToken() 的 function，並在登入檢核成功時産生一個 token 回傳。

![image](https://user-images.githubusercontent.com/21993717/169645083-9cbe450b-e30e-490e-8b66-1c05ea0c9a31.png)


### 設置驗證是否為合法有效的 JWT Token

第一步，透過 DI 將 JWT 相關設定設置好，在 Programe.cs 檔案中的 ”`builder.Services.AddControllers();`“ 
程式碼之前加入以下有關使用 JWT 使用者授權驗證的程式邏輯：

```cs {linenos=table,hl_lines=[],linenostart=1}
builder.Services.AddAuthentication(options =>
    {
        options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;

    })
    .AddJwtBearer(options =>
    {
        // 當驗證失敗時，回應標頭會包含 WWW-Authenticate 標頭，這裡會顯示失敗的詳細錯誤原因
        options.IncludeErrorDetails = true; // 預設值為 true，有時會特別關閉

        options.TokenValidationParameters = new TokenValidationParameters
        {
            // 透過這項宣告，就可以從 "NAME" 取值
            NameClaimType = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
            // 透過這項宣告，就可以從 "Role" 取值，並可讓 [Authorize] 判斷角色
            RoleClaimType = "http://schemas.microsoft.com/ws/2008/06/identity/claims/role",

            // 驗證 Issuer (一般都會)
            ValidateIssuer = true,
            ValidIssuer = _configuration.GetValue<string>("JwtSettings:ValidIssuer"),

            // 驗證 Audience (通常不太需要)
            ValidateAudience = false,
            //ValidAudience = = _configuration.GetValue<string>("JwtSettings:ValidAudience"),

            // 驗證 Token 的有效期間 (一般都會)
            ValidateLifetime = true,

            // 如果 Token 中包含 key 才需要驗證，一般都只有簽章而已
            ValidateIssuerSigningKey = false,

            // 應該從 IConfiguration 取得
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secret))
        };
    });
```

同時宣告一個 Secret 變數，並由 appsettings.json 系統配置檔中讀出 Jwt Secret 的設定。

```cs {linenos=table,hl_lines=[3],linenostart=1}
// ...
ConfigurationManager _configuration = builder.Configuration;
var secret = _configuration.GetValue<string>("JwtSettings:Secret");
// ...
```

第二步，要啟動 request pipeline 中的 Middleware (UseAuthentication & UseAuthorization 都需要)

```cs {linenos=table,hl_lines=[2],linenostart=1}
// ...
app.UseAuthentication();
app.UseAuthorization();
// ...
```

### 在特定 API EndPoint 上驗證是否帶有合法有效的 JWT Token

在 WeatherForecastController.cs Get() function 上加入 “`[Authorize]`” 即可

```cs {linenos=table,hl_lines=[1]}
    [Authorize]
    [HttpGet(Name = "GetWeatherForecast")]
    public IEnumerable<WeatherForecast> Get()
    {
        return Enumerable.Range(1, 5).Select(index => new WeatherForecast
        {
            Date = DateTime.Now.AddDays(index),
            TemperatureC = Random.Shared.Next(-20, 55),
            Summary = Summaries[Random.Shared.Next(Summaries.Length)]
        })
        .ToArray();
    }
```

### 使用 OpenApi Swagger 來測試 API

如上程式加入`[Authorize]`後，以`dotnet watch`執行程式，進入 OpenApi Swagger 網頁瀏覽 weatherforecast endpoint，會回傳 Status: 401 Unauthorized 的錯誤訊息。

![image](https://user-images.githubusercontent.com/21993717/223022987-6142887b-11be-4d23-88d7-39adc27ca45a.png)


在 OpenApi Swagger 網頁點選 register endpoint 來註冊一個新使用者

![image](https://user-images.githubusercontent.com/21993717/223023581-bfa2d47e-39ca-4c3b-b9de-36b2890b9bf8.png)

輸入註冊資料，執行送出，畫面顯示成功！

![image](https://user-images.githubusercontent.com/21993717/223023730-995193d9-dec8-40ee-9afc-7aff1fbeddf0.png)

可查看資料庫結果，已在 AspNetUsers Table 中新建立一筆使用者資料：

![image](https://user-images.githubusercontent.com/21993717/223023969-40466f8b-efb5-4ea9-b730-dac3e2d0bae7.png)


OpenApi Swagger 來測試 API 時, Swagger 測試網頁預設是沒有設定 Token 的功能,必須將程式碼中的 “`builder.Services.AddSwaggerGen();`”改成以下內容

```cs {linenos=table,hl_lines=[]}
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "JwtDemo", Version = "v1" });
    c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
    {
        In = ParameterLocation.Header,
        Description = "Please enter JWT with Bearer into field",
        Name = "Authorization",
        Type = SecuritySchemeType.ApiKey
    });
    c.AddSecurityRequirement(new OpenApiSecurityRequirement
    {
        {
            new OpenApiSecurityScheme
                {
                    Reference = new OpenApiReference { Type = ReferenceType.SecurityScheme, Id = "Bearer"}
                },
            new string[] {}
        }
    });
});
```
有了上述的程式設定，當再次 dotnet watch 啟動程式後，瀏覽器呈現的 Swagger 畫面右上角會多出了｀Authorize｀ 的按鈕。按下按鈕就是讓你填入登入成功後回傳的 Token

![image](https://user-images.githubusercontent.com/21993717/168768593-f2dde2f9-d4b2-43dd-85f5-ef080ff8c13a.png)

使用合法（已完成註冊）使用者帳號／密碼來登入：

![image](https://user-images.githubusercontent.com/21993717/223043509-22f879fe-83f5-47c0-ab6e-131797212e7b.png)
登入成功後回傳的 response 中會包含 token：

![image](https://user-images.githubusercontent.com/21993717/223041748-d99925fe-afbe-484c-818b-32a933719a02.png)


在 "Value:"" 文字框內填入 "Bearer eyJhbGciOiJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGRzaWctbW9yZSNobWFjLXNoYTUxMiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZ....."，再按下 Authorize 按鈕即表下在接下來的 Request 中都會自動帶入 Token 傳給 WebApi Server。 （請注意 Bearer後再先接著一個空白字元再加上 Token值）

![image](https://user-images.githubusercontent.com/21993717/168768975-0274c3cf-eba6-49bc-a696-36df5b2a97db.png)

再次執行　"WeatherForecast" 的測試(Execute)　就可正常的取得回傳值了

![image](https://user-images.githubusercontent.com/21993717/223042216-89d313db-adc3-4659-87b7-afac0d57ff62.png)


### 加入 git 版本控制

```bash
$ git commit -m "finished JWT function" -a
```

## 使用　aspnet-codegenerator　工具來自動産生程式碼

最後我們來看看如何使用工具來自動産生程式去維護一個新的資料表


### 在 Models 目錄下新增一個 model(模型) class - ItemData

使用 dotnet cli 來新增一個 class 

```bash
$ dotnet new class -o Models -n TodoList
```

産生出來的空的程式框架：

![image](https://user-images.githubusercontent.com/21993717/223054862-53a8302f-f529-4d29-9874-a0946a249dab.png)


將 Models/ItemData.cs 補齊欄位設定

```cs {linenos=table,hl_lines=[]}
namespace dotnet7_webapi_jwt.Models;
public class TodoList
{
    public int Id { get; set; }
    public string? Title { get; set; }
    public string? Details { get; set; }
    public bool Done { get; set; }
}
```

### 在 ApiDbContext.cs 中宣告一個 TodoList table

```cs
        public DbSet<TodoList>? TodoList { get; set; }  #放在程式最後面
```

### 新增一個遷移與更新資料庫

```bash
$ dotnet build
$ dotnet ef migrations add "Add New Table - TodoList"
$ dotnet ef database update 
```

註：｀$ dotnet ef migrations remove｀ 可移除最後一個 migration


### 使用 ASPNET Codegenerator 自動產生 Todo Controller

```bash
$ dotnet aspnet-codegenerator controller -name TodoController -async -api -m TodoList -dc ApiDbContext -outDir Controllers

Building project ...
Finding the generator 'controller'...
Running the generator 'controller'...

Minimal hosting scenario!
Attempting to compile the application in memory.
Attempting to figure out the EntityFramework metadata for the model and DbContext: 'TodoList'

Using database provider 'Npgsql.EntityFrameworkCore.PostgreSQL'!

Added Controller : '/Controllers/TodoController.cs'.
RunTime 00:00:08.68
```

TodoController.cs

```cs {linenos=table,hl_lines=[]}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using dotnet7_webapi_jwt.Data;
using dotnet7_webapi_jwt.Models;

namespace dotnet7_webapi_jwt.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class TodoController : ControllerBase
    {
        private readonly ApiDbContext _context;

        public TodoController(ApiDbContext context)
        {
            _context = context;
        }

        // GET: api/Todo
        [HttpGet]
        public async Task<ActionResult<IEnumerable<ItemData>>> GetItemData()
        {
          if (_context.ItemData == null)
          {
              return NotFound();
          }
            return await _context.ItemData.ToListAsync();
        }

        // GET: api/Todo/5
        [HttpGet("{id}")]
        public async Task<ActionResult<ItemData>> GetItemData(int id)
        {
          if (_context.ItemData == null)
          {
              return NotFound();
          }
            var itemData = await _context.ItemData.FindAsync(id);

            if (itemData == null)
            {
                return NotFound();
            }

            return itemData;
        }

        // PUT: api/Todo/5
        // To protect from overposting attacks, see https://go.microsoft.com/fwlink/?linkid=2123754
        [HttpPut("{id}")]
        public async Task<IActionResult> PutItemData(int id, ItemData itemData)
        {
            if (id != itemData.Id)
            {
                return BadRequest();
            }

            _context.Entry(itemData).State = EntityState.Modified;

            try
            {
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!ItemDataExists(id))
                {
                    return NotFound();
                }
                else
                {
                    throw;
                }
            }

            return NoContent();
        }

        // POST: api/Todo
        // To protect from overposting attacks, see https://go.microsoft.com/fwlink/?linkid=2123754
        [HttpPost]
        public async Task<ActionResult<ItemData>> PostItemData(ItemData itemData)
        {
          if (_context.ItemData == null)
          {
              return Problem("Entity set 'ApiDbContext.ItemData'  is null.");
          }
            _context.ItemData.Add(itemData);
            await _context.SaveChangesAsync();

            return CreatedAtAction("GetItemData", new { id = itemData.Id }, itemData);
        }

        // DELETE: api/Todo/5
        [HttpDelete("{id}")]
        public async Task<IActionResult> DeleteItemData(int id)
        {
            if (_context.ItemData == null)
            {
                return NotFound();
            }
            var itemData = await _context.ItemData.FindAsync(id);
            if (itemData == null)
            {
                return NotFound();
            }

            _context.ItemData.Remove(itemData);
            await _context.SaveChangesAsync();

            return NoContent();
        }

        private bool ItemDataExists(int id)
        {
            return (_context.ItemData?.Any(e => e.Id == id)).GetValueOrDefault();
        }
    }
}

```

### 測試新功能

在 open api - swagger 網頁上透過 POST 的 EndPoint 新增一筆 Toto list

![image](https://user-images.githubusercontent.com/21993717/169677520-3cba411e-f4e1-421c-88a9-0db52a981b47.png)

送出新增的資料，回覆新增成功

![image](https://user-images.githubusercontent.com/21993717/169677538-55e9ca68-5bf1-49ce-9bd1-4fa6a0f1fe53.png)

由資料庫中可以查詢到新建立的 record

!![image](https://user-images.githubusercontent.com/21993717/223059310-a4ec3825-325e-4bbd-8465-c67ab2bf1e46.png)


透過 GET 的 EndPoint 也可以查詢到新增 Toto list

![image](https://user-images.githubusercontent.com/21993717/169677646-1d086a10-8230-46ae-97f3-2ce4b0d249ea.png)


以上可以發現使用 ASPNET Codegenerator 自動産生的程式就可簡單的完成資料表格的新增、查詢、修改、刪除等日常功能，真是方便呢！

## CORS 議題

Web App 與 Web Api Server 若處於“不同源”時，當 App 使用 http request 呼叫 Web Api Server 上端點時就會有“同源策略“的問題，這個狀況在測試環境中尤為明顯。
要解決這個問題就必須透過 CORS (Cross-Origin Resource Sharing) 相關設定來應對。在 DotNet Core 中設定相關簡易，僅須在 Program.cs 中加入以下二段程式碼即可：

以下程式碼放在 “`builder.Services.AddControllers();`” 之前：

```cs {linenos=table,hl_lines=[]}
var MyAllOrigins = "allowAll";
builder.Services.AddCors(option => 
    option.AddPolicy(name: MyAllOrigins, 
        policy =>
        {
            policy.WithOrigins("http://localhost:4200").AllowAnyMethod().AllowAnyHeader();
        }
    )
);
```
以及

以下程式碼放在 “`app.UseHttpsRedirection();`” 之前：

```cs
app.UseCors(MyAllOrigins);
```

完成後，當前端 Web App (如： angular 在測試環境下預設是使用 localhost:4200) 使用 http request 呼叫後端 Web Api (本例中 Web Api 使用的是 localhost:5023) 時就可避到同源策略的要求。

Program.cs 完整程式碼
```cs {linenos=table,hl_lines=[]}
using System.Text;
using dotnet7_webapi_jwt.Data;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Identity;
using Microsoft.EntityFrameworkCore;
using Microsoft.IdentityModel.Tokens;
using Microsoft.OpenApi.Models;

var builder = WebApplication.CreateBuilder(args);

ConfigurationManager _configuration = builder.Configuration;
var secret = _configuration.GetValue<string>("JwtSettings:Secret");

// Add services to the container.
builder.Services.AddDbContext<ApiDbContext>(
    options => options.UseNpgsql(
        _configuration.GetConnectionString("ConnStr")
    )
);
builder.Services.AddIdentity<IdentityUser, IdentityRole>()
    .AddEntityFrameworkStores<ApiDbContext>()
    .AddDefaultTokenProviders();

builder.Services.AddAuthentication(options =>
    {
        options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;

    })
    .AddJwtBearer(options =>
    {
        // 當驗證失敗時，回應標頭會包含 WWW-Authenticate 標頭，這裡會顯示失敗的詳細錯誤原因
        options.IncludeErrorDetails = true; // 預設值為 true，有時會特別關閉

        options.TokenValidationParameters = new TokenValidationParameters
        {
            // 透過這項宣告，就可以從 "NAME" 取值
            NameClaimType = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
            // 透過這項宣告，就可以從 "Role" 取值，並可讓 [Authorize] 判斷角色
            RoleClaimType = "http://schemas.microsoft.com/ws/2008/06/identity/claims/role",

            // 驗證 Issuer (一般都會)
            ValidateIssuer = true,
            ValidIssuer = _configuration.GetValue<string>("JwtSettings:ValidIssuer"),

            // 驗證 Audience (通常不太需要)
            ValidateAudience = false,
            //ValidAudience = = _configuration.GetValue<string>("JwtSettings:ValidAudience"),

            // 驗證 Token 的有效期間 (一般都會)
            ValidateLifetime = true,

            // 如果 Token 中包含 key 才需要驗證，一般都只有簽章而已
            ValidateIssuerSigningKey = false,

            // 應該從 IConfiguration 取得
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secret))
        };
    });
        
var allOrigins = "allowAll";
builder.Services.AddCors(option => 
    option.AddPolicy(name: allOrigins, 
        policy =>
        {
            policy.WithOrigins("http://localhost:4200").AllowAnyMethod().AllowAnyHeader();
        }
    )
);

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "JwtDemo", Version = "v1" });
    c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
    {
        In = ParameterLocation.Header,
        Description = "Please enter JWT with Bearer into field",
        Name = "Authorization",
        Type = SecuritySchemeType.ApiKey
    });
    c.AddSecurityRequirement(new OpenApiSecurityRequirement
    {
        {
            new OpenApiSecurityScheme
                {
                    Reference = new OpenApiReference { Type = ReferenceType.SecurityScheme, Id = "Bearer"}
                },
            new string[] {}
        }
    });
});

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseCors(allOrigins);
app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();

app.Run();
```