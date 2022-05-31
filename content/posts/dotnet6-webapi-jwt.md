---
title: "使用 .NET 6.0 進行 JWT 身份驗證的 ASP.NET Web Api。並使用 Microsoft Identity 框架來存儲使用者和角色等資料"
date: 2022-05-20
description: "在前面的筆記中已紀錄了使用 ASP.NET Core 所提供的精簡 Minimal APIs 新框架。本篇將要紀錄如何使用　.NET 6.0“正統”的 MVC Web Api 架構來撰寫一個可方便擴展 Web API"
tags: ["dotnet 6", "webapi", "jwt", "docker", "sqlserver", "openapi", "swagger"]
categories: ["webapi"]
---
*[github Source code #tag: dotnet6_webapi_jwt](https://github.com/calvinegs/dotnet6-webapi-jwt)*

本文將記錄如何一步步從無到有使用 Dotnet Core 6.0 建立 ASP.NET Core Web API，其中將會使用到下列技術:

- Dotnet CLI
- Entity Framework
- Json Web Token
- SQL Server (Docker Version)
- ASP.NET Core Generator

## 建置新專案

```bash
$ dotnet new webapi -o dotnet6-webapi-jwt
範本「ASP.NET Core Web API」已成功建立。

正在處理建立後的動作...
正在 /home/egs/cal-data/tech-test/webapi/dotnet6-webapi-jwt/dotnet6-webapi-jwt.csproj 上執行 'dotnet restore'...
  正在判斷要還原的專案...
  已還原 /home/egs/cal-data/tech-test/webapi/dotnet6-webapi-jwt/dotnet6-webapi-jwt.csproj (238 ms 內)。
還原成功。


$ cd dotnet6-webapi-jwt
$ ls -al
總用量 40
drwxrwxr-x  5 egs egs 4096  五  20 09:38 .
drwxrwxr-x 12 egs egs 4096  五  20 09:38 ..
-rw-rw-r--  1 egs egs  127  五  20 09:38 appsettings.Development.json
-rw-rw-r--  1 egs egs  151  五  20 09:38 appsettings.json
drwxrwxr-x  2 egs egs 4096  五  20 09:38 Controllers
-rw-rw-r--  1 egs egs  382  五  20 09:38 dotnet6-webapi-jwt.csproj
drwxrwxr-x  2 egs egs 4096  五  20 09:38 obj
-rw-rw-r--  1 egs egs  557  五  20 09:38 Program.cs
drwxrwxr-x  2 egs egs 4096  五  20 09:38 Properties
-rw-rw-r--  1 egs egs  267  五  20 09:38 WeatherForecast.cs
```

> dotnet code 版本安裝與管理

```bash
$ dotnet --list-sdks    # 顯示已安裝的 sdk 版本資訊
5.0.408 [/usr/share/dotnet/sdk]
6.0.300 [/usr/share/dotnet/sdk]

$ dotnet --version  # 顯示目前所使用的版本
6.0.300 # 預設是最新的版本
```

> 由於 dotnet 版本演化滿快的，所以會建議在專案目錄中要指定使用 SDK 的版本，以免當你又安裝了更新版本（如7.0）後程式執行出問題。

```bash
$ dotnet new globaljson --sdk-version 6.0.300
範本「global.json 檔案」已成功建立。

$ cat global.json
{
  "sdk": {
    "version": "6.0.300"
  }
}
```

> 使用 dotnet cli 來產生預設的 git ignore 檔案

```bash
$ dotnet new gitignore
```

> 建立 git 初始版本

```bash
$ git init && git add . && git commit -m "Initial commit"
```

### 安裝本機工具

此方式安裝的工具，僅限本機存取(只針對目前的目錄和子目錄)， 首先透過 dotnet new tool-manifest 命令來產生工具資訊清單檔，再使用 dotnet tool install 來安裝各式工具程式。這樣的方式好處是在專案若多人協助方式時，則可利用 dotnet tool restore 命令將紀錄在 .config/dotnet-tools.json 的工具資訊清單檔重建在不同協助人員的電腦中。

```bash
$ dotnet new tool-manifest #會產生 .config/dotnet-tools.json 檔案
$ dotnet tool install --local dotnet-ef #使用 local 安裝方式來安裝 Entity Framework 工具
您可使用下列命令，從此目錄叫用工具: 'dotnet tool run dotnet-ef' 或 'dotnet dotnet-ef'。
已成功安裝工具 'dotnet-ef' (版本 '6.0.5')。項目已新增至資訊清單檔 /home/egs/cal-data/tech-test/webapi/dotnet6-webapi-jwt/.config/dotnet-tools.json。

$ dotnet tool install --local dotnet-aspnet-codegenerator #使用 local 安裝方式來安裝 Code Generator 工具
您可使用下列命令，從此目錄叫用工具: 'dotnet tool run dotnet-aspnet-codegenerator' 或 'dotnet dotnet-aspnet-codegenerator'。
已成功安裝工具 'dotnet-aspnet-codegenerator' (版本 '6.0.5')。項目已新增至資訊清單檔 /home/egs/cal-data/tech-test/webapi/dotnet6-webapi-jwt/.config/dotnet-tools.json。

$  cat .\.config\dotnet-tools.json # 查看安裝上述二項工具後的設定資訊
```

```json {linenos=table,hl_lines=[5,9]}
{
  "version": 1,
  "isRoot": true,
  "tools": {
    "dotnet-ef": {
      "version": "6.0.5",
      "commands": ["dotnet-ef"]
    },
    "dotnet-aspnet-codegenerator": {
      "version": "6.0.5",
      "commands": ["dotnet-aspnet-codegenerator"]
    }
  }
}
```

### 安裝程式使用的相關套件

```bash
$ dotnet add package Microsoft.EntityFrameworkCore.Tools #使用 dotnet Entity Framework時必須安裝此套件
$ dotnet add package Microsoft.EntityFrameworkCore.Design #使用 dotnet Entity Framework時必須安裝此套件
$ dotnet add package Microsoft.EntityFrameworkCore.SqlServer #使用 dotnet Entity Framework時必須安裝此套件
$ dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore #使用 Identity Framework時必須安裝此套件
$ dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer #

$ dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design #搭配 dotnet-aspnet-codegenerator 使用
```

安裝的程式套件資訊紀錄在 "專案".csproj 檔案中

```bash
$ cat dotnet6-webapi-jwt.csproj #查看 安裝套件的相關設定值
```

```xml {linenos=table,hl_lines=["11-13","17-18"]}
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
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="6.0.5" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="6.0.5">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.2.3" />
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

![image](https://user-images.githubusercontent.com/21993717/168411109-019af6d6-0e08-4808-99bf-e0224694f479.png)

![image](https://user-images.githubusercontent.com/21993717/168411149-d9f9777d-a74a-4ef0-a1af-f42bff2a38f0.png)


> 打開 VS Code

```bash
$ code .
```

> 目前産生的程式架構

![2022-05-20 10-14-27](https://user-images.githubusercontent.com/21993717/169435113-7c2de7af-6faf-49dd-a58e-2d1e7fdf971e.png)


## 設置使用 Entity Framework相關設定

### 新增 database context (自動產生)

> 使用 dotnet ef 工具在專案目錄 ./Data 子目錄下新建立一個 ApiDbContext.cs 的 DB Context file

註：在使用前先把 SQL Server 環境傋妥，安裝 SQL Server 可參考此篇筆紀　*[github Source code](https://calvinegs.github.io/posts/sqlserver2019-docker/)*

```bash
$ dotnet ef dbcontext scaffold "Data Source=localhost;Initial Catalog=TestDB;User ID=SA;Password=Sql@12345" Microsoft.EntityFrameworkCore.SqlServer -c ApiDbContext -o Data 

Build started...
Build succeeded.
To protect potentially sensitive information in your connection string, you should move it out of source code. You can avoid scaffolding the connection string by using the Name= syntax to read it from configuration - see https://go.microsoft.com/fwlink/?linkid=2131148. For more guidance on storing connection strings, see http://go.microsoft.com/fwlink/?LinkId=723263.
```

![image](https://user-images.githubusercontent.com/21993717/169450862-23c0632a-d0d9-45ac-b639-294fa6622bb0.png)

```cs
using System;
using System.Collections.Generic;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata;

namespace dotnet6_webapi_jwt.Data
{
    public partial class ApiDbContext : DbContext
    {
        public ApiDbContext()
        {
        }

        public ApiDbContext(DbContextOptions<ApiDbContext> options)
            : base(options)
        {
        }

        public virtual DbSet<Inventory> Inventories { get; set; } = null!;

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            if (!optionsBuilder.IsConfigured)
            {
                // optionsBuilder.UseSqlServer("Data Source=localhost;Initial Catalog=TestDB;User ID=SA;Password=Sql@12345");
            }
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Inventory>(entity =>
            {
                entity.HasNoKey();

                entity.ToTable("Inventory");

                entity.Property(e => e.Id).HasColumnName("id");

                entity.Property(e => e.Name)
                    .HasMaxLength(50)
                    .HasColumnName("name");

                entity.Property(e => e.Quantity).HasColumnName("quantity");
            });

            OnModelCreatingPartial(modelBuilder);
        }

        partial void OnModelCreatingPartial(ModelBuilder modelBuilder);
    }
}
```

> 在 appsettings.json 檔案中加入 Connection String
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
    "ConnStr": "Data Source=localhost;Initial Catalog=TestDB;User ID=SA;Password=Sql@12345"
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

```cs  {linenos=table,hl_lines=[1,12],linenostart=1}
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
  $ dotnet build
  $ dotnet ef migrations add "Add Identity Framework"

Build started...
Build succeeded.
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 6.0.5 initialized 'ApiDbContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer:6.0.5' with options: None
Done. To undo this action, use 'ef migrations remove'

  $ dotnet ef database update
```

> 在　dotnet ef migrations add "Add Identity Framework" 指令完成後，可以在專案目錄下發生産生新的子目錄 Migrations，並有三個新檔案

![image](https://user-images.githubusercontent.com/21993717/169499823-eb5c166e-dd7a-47c0-b3c8-cff6d344e043.png)

> 在 dotnet ef database update 指令完成後，SQL Server TestDB 資料庫中產生 Identity Framework 會使用到的資料表

![image](https://user-images.githubusercontent.com/21993717/169503362-b93426a4-117a-43ea-be0a-cff9a758c669.png)


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
    "ConnStr": "Data Source=localhost;Initial Catalog=TestDB;User ID=SA;Password=Sql@12345"
  },
  "JwtSettings": {
    "ValidIssuer": "Dotnet6WebApiDemo",
    "ValidAudience": "Dotnet6WebApiDemo",
    "Secret": "Dotnet6 WebApi Demo. Using Json Web Token Technology to keep user info."
  }
}
```

### 新增 使用者註冊和登入時使用的 Data model class (Models/AuthenticateData.cs)

```cs
using System.ComponentModel.DataAnnotations;

namespace dotnet6_webapi_jwt.Models;

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

```cs
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using dotnet6_webapi_jwt.Models;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Tokens;

namespace dotnet6_webapi_jwt.Controllers;

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

> 在上述 AuthenticateController.cs 程式中，我們建立一個 CreateToken() 的 function，並在登入檢核成功時産生一個 token 回傳。

![image](https://user-images.githubusercontent.com/21993717/169645083-9cbe450b-e30e-490e-8b66-1c05ea0c9a31.png)


### 設置驗證是否為合法有效的 JWT Token

> 第一步，透過 DI 將 JWT 相關設定設置好

```cs
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

> 第二步，要啟動 request pipeline 中的 Middleware (UseAuthentication & UseAuthorization 都需要)

```cs
app.UseAuthentication();
app.UseAuthorization();
```

### 在特定 API EndPoint 上驗證是否帶有合法有效的 JWT Token

> 在 WeatherForecastController.cs Get() function 上加入`[Authorize]` 即可

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

> 如上程式加入`[Authorize]`後，再重新瀏覽 weatherforecast endpoint，會回傳 Status: 401 Unauthorized 的錯誤訊息。

![image](https://user-images.githubusercontent.com/21993717/169671893-ee4be031-2fea-46bb-b419-c6938557a0f9.png)

> 先登入取得 Token

![image](https://user-images.githubusercontent.com/21993717/169676210-0802d786-ebaa-4fe7-a166-35fd25073ce9.png)


> 先加入 Authorization Header，並將登入成功後回傳的 Token 加到 Authorization Header 中。再次送出就可正常的取得所有天氣預測資料了。

![image](https://user-images.githubusercontent.com/21993717/169676250-eab03c8e-ce1e-4ef5-ac35-15026d058821.png)


### 使用 OpenApi Swagger 來測試 API

> OpenApi Swagger 來測試 API時, 因為　Swagger 測試網頁預設是沒有設定 Token 的功能,必須將程式碼中的 `builder.Services.AddSwaggerGen();`改成以下內容

```cs
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
> 有了上述的程式設定，當再次 dotnet run 啟動程式後，瀏覽器呈現的 Swagger 畫面右上角會多出了｀Authorize｀ 的按鈕。按下按鈕就是讓你填入登入成功後回傳的 Token

![image](https://user-images.githubusercontent.com/21993717/168768593-f2dde2f9-d4b2-43dd-85f5-ef080ff8c13a.png)

> 在 Value: 文字框內填入 "Bearer yJhbGciOiJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGRzaWctbW9yZSNobWFjLXNoYTUxMiIsInR5cCI6IkpXVCJ9....."，再按下 Authorize 按鈕即表下在接下來的 Request 中都會自動帶入 Token 傳給 WebApi Server。 （請注意 Bearer後再先接著一個空白字元再加上 Token值）

![image](https://user-images.githubusercontent.com/21993717/168768975-0274c3cf-eba6-49bc-a696-36df5b2a97db.png)

> 再次執行　"WeatherForecast" 的測試(Execute)　就可正常的取得回傳值了

![image](https://user-images.githubusercontent.com/21993717/169676372-25c30496-45eb-409c-acdb-06771cd26c8b.png)


>　若要使用 "角色" 的授權檢核，只須將｀[Authorize]｀改成｀[Authorize(Roles = UserRoles.Admin)]｀即可

```cs {linenos=table, hl_lines=[1]}
    [Authorize(Roles = UserRoles.Admin)]
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

### 加入 git 版本控制

```bash
$ git commit -m "finished JWT function" -a
```

## 使用　aspnet-codegenerator　工具來自動産生程式碼

最後我們來看看如何使用工具來自動産生程式去維護一個新的資料表


### 在 Models 目錄下新增一個 model(模型) class - ItemData

> Models/ItemData.cs

```cs
namespace dotnet6_webapi_jwt.Models;
public class ItemData
{
    public int Id { get; set; }
    public string? Title { get; set; }
    public string? Details { get; set; }
    public bool Done { get; set; }
}
```

### 在 ApiDbContext.cs 中宣告一個 ItemData table

```cs
        public DbSet<ItemData>? ItemData { get; set; }
```

### 新增一個遷移與更新資料庫

```bash
$ dotnet build
$ dotnet ef migrations add "Add New Table - ItemData"
$ dotnet ef database update 
```

### 使用 ASPNET Codegenerator 自動產生 Todo Controller

```bash
$ dotnet aspnet-codegenerator controller -name TodoController -async -api -m ItemData -dc ApiDbContext -outDir Controllers


Building project ...
Finding the generator 'controller'...
Running the generator 'controller'...

Minimal hosting scenario!
Attempting to compile the application in memory with the modified DbContext.
Attempting to figure out the EntityFramework metadata for the model and DbContext: 'ItemData'
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 6.0.5 initialized 'ApiDbContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer:6.0.5' with options: None
Added Controller : '/Controllers/TodoController.cs'.
RunTime 00:00:10.90
```

> TodoController.cs

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using dotnet6_webapi_jwt.Data;
using dotnet6_webapi_jwt.Models;

namespace dotnet6_webapi_jwt.Controllers
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

> 在 open api - swagger 網頁上透過 POST 的 EndPoint 新增一筆 Toto list

![image](https://user-images.githubusercontent.com/21993717/169677520-3cba411e-f4e1-421c-88a9-0db52a981b47.png)

> 送出新增的資料，回覆新增成功

![image](https://user-images.githubusercontent.com/21993717/169677538-55e9ca68-5bf1-49ce-9bd1-4fa6a0f1fe53.png)

> 由資料庫中可以查詢到新建立的 record

![image](https://user-images.githubusercontent.com/21993717/169677555-cad25702-3769-4a54-a8b5-9dc996ba9dd6.png)

> 透過 GET 的 EndPoint 也可以查詢到新增 Toto list

![image](https://user-images.githubusercontent.com/21993717/169677646-1d086a10-8230-46ae-97f3-2ce4b0d249ea.png)


以上可以發現使用 ASPNET Codegenerator 自動産生的程式就可簡單的完成資料表格的新增、查詢、修改、刪除等日常功能，真是方便呢！