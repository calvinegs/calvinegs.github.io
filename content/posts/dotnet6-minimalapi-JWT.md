---
title: "使用 ASP.NET Core 6 提供的 Minimal APIs 新框架建置一個極簡的 Web API 服務"
date: 2022-05-13
description: "在 ASP.NET Core 提供 Minimal APIs 新框架前，你使用的是 MVC 框架，比起 Node.js + Express MVC 框架是顯得煩複許多，也許你只是一個提供極簡 rest web api 服務如：Microservices，但你還是得先建好必要的程式架構才能開始你的程式撰寫，有了 Minimal APIs 新框架後一切都變得簡單了"
tags: ["dotnet 6", "webapi", "minimal api", "jwt"]
categories: ["webapi"]
---
*[github Source code #tag: minimal_webapi_jwt](https://github.com/calvinegs/Minimal-Webapi-Jwt)*

本篇筆記中將紀錄如何使用 ASP.NET Core 6 提供的 Minimal APIs 新框架建置一個使用 Token-base 身份驗證的 Web API 網站。

## 建立新專案

```bash
$ dotnet new webapi -o JwtAuthDemo -minimal
範本「ASP.NET Core Web API」已成功建立。

正在處理建立後的動作...
正在 /home/egs/cal-data/tech-test/webapi/Minimal/JwtAuthDemo/JwtAuthDemo.csproj 上執行 'dotnet restore'...
  正在判斷要還原的專案...
  已還原 /home/egs/cal-data/tech-test/webapi/Minimal/JwtAuthDemo/JwtAuthDemo.csproj (214 ms 內)。
還原成功。

$ cd JwtAuthDemo
$ ls -al
總用量 32
drwxrwxr-x 4 egs egs 4096  五  13 18:36 .
drwxrwxr-x 7 egs egs 4096  五  13 18:36 ..
-rw-rw-r-- 1 egs egs  127  五  13 18:36 appsettings.Development.json
-rw-rw-r-- 1 egs egs  151  五  13 18:36 appsettings.json
-rw-rw-r-- 1 egs egs  327  五  13 18:36 JwtAuthDemo.csproj
drwxrwxr-x 2 egs egs 4096  五  13 18:36 obj
-rw-rw-r-- 1 egs egs 1131  五  13 18:36 Program.cs
drwxrwxr-x 2 egs egs 4096  五  13 18:36 Properties
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

> 安裝 Microsoft.AspNetCore.Authentication.JwtBearer 套件

```bash {hl_lines=[1,13]}
$ dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer

$ cat JwtAuthDemo.csproj 
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="6.0.5" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.2.3" />
  </ItemGroup>

</Project>
```

> 加入 Git 新版本

```bash
$ git commit -m "Add new package Microsoft.AspNetCore.Authentication.JwtBearer" -a

$ code .    # 打開 VS Code
```
> 目前産生的程式架構

![image](https://user-images.githubusercontent.com/21993717/168269485-478b1ace-676d-48e5-a96d-be69cfcbe5a3.png)


> 執行程式

```bash
$ dotnet watch
```

![image](https://user-images.githubusercontent.com/21993717/168411109-019af6d6-0e08-4808-99bf-e0224694f479.png)

![image](https://user-images.githubusercontent.com/21993717/168411149-d9f9777d-a74a-4ef0-a1af-f42bff2a38f0.png)


## 實作 JWT 的流程

上圖中可以看到，由系統自動産生的 sample code 已經可以正常執行。

接下來將在程式中實作JWT功能， 透過 JWT　的實作可以讓你的專案實現 Token-base 的身份驗證與授權。 （Json Web Token) 實作的過程大致可以分成三個部分:
- 在登入成功後産生合法的 JWT Token
- 每次收到 request 時驗證是否為合法有效的 JWT Token
- 在特定 API Endpoint 上驗證是否帶有 “合法有效的 JWT Token”，以達到權限管理的需求


## 産生合法的 Jason Web Token

> 在 Program.cs 中新一個建立 Token 的 function

```cs
string CreateToken(LoginViewModel user)
{
    List<Claim> claims = new List<Claim>
    {
        new Claim(ClaimTypes.Name, user.Username),
        new Claim(ClaimTypes.Role, "Admin")
    };

    ConfigurationManager _configuration = builder.Configuration;

    var secretkey = new SymmetricSecurityKey(System.Text.Encoding.UTF8.GetBytes(
        _configuration.GetValue<string>("JwtSettings:Secret")));    // _configuration.GetSection("JwtSettings:Secret").Value)

    var credentials = new SigningCredentials(secretkey, SecurityAlgorithms.HmacSha512Signature);

    var token = new JwtSecurityToken(   // 亦可使用　SecurityTokenDescriptor　來産生 Token
        issuer: _configuration.GetValue<string>("JwtSettings:Issuer"),
        audience: _configuration.GetValue<string>("JwtSettings:Audience"),
        claims: claims,
        expires: DateTime.Now.AddDays(1),
        signingCredentials: credentials);

    var jwt = new JwtSecurityTokenHandler().WriteToken(token);

    return jwt;
}
```

> 建立一個 登入 的 Endpoint

```cs
app.MapPost("/signin", (LoginViewModel login) =>
    {
        if (ValidateUser(login))  // 驗證登入的帳號是否合法
        {
            var token = CreateToken(login); // 若為合法使用者，則産生一個使用 Token
            return Results.Ok(new { token }); // 將登入狀態與Token一併回傳前端
        }
        else
        {
            return Results.BadRequest();  // 驗證失敗時回傳 status: 400 Bad Request
        }
    }
).WithName("SignIn").AllowAnonymous();

bool ValidateUser(LoginViewModel login)
{
    return login.Username == "cal" ? true : false;
}
```

> 建立一個 model 來接收 login 資料

```cs
record LoginViewModel(string Username, string Password);
```

![image](https://user-images.githubusercontent.com/21993717/168706315-16b2658a-ff6a-45c2-b8b3-3c1eee0c829c.png)


## 驗證是否為合法有效的 JWT Token

> 第一步，透過 DI 將 JWT 相關設定設置好

```cs
builder.Services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
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
            ValidIssuer = _configuration.GetValue<string>("JwtSettings:Issuer"),

            // 驗證 Audience (通常不太需要)
            ValidateAudience = false,
            //ValidAudience = "JwtAuthDemo", // 不驗證就不需要填寫

            // 驗證 Token 的有效期間 (一般都會)
            ValidateLifetime = true,

            // 如果 Token 中包含 key 才需要驗證，一般都只有簽章而已
            ValidateIssuerSigningKey = false,

            // 應該從 IConfiguration 取得
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secret))
        };
    });

builder.Services.AddAuthorization();
```

> 第二步，要記得也要啟動 request pipeline 中的 Middleware (UseAuthentication & UseAuthorization 都需要)

```cs
app.UseAuthentication();
app.UseAuthorization();
```

## 在特定 API EndPoint 上驗證是否帶有合法有效的 JWT Token

> 加入`.RequireAuthorization()` 即可

```cs {hl_lines=[13]}
app.MapGet("/weatherforecast", () =>
{
    var forecast =  Enumerable.Range(1, 5).Select(index =>
        new WeatherForecast
        (
            DateTime.Now.AddDays(index),
            Random.Shared.Next(-20, 55),
            summaries[Random.Shared.Next(summaries.Length)]
        ))
        .ToArray();
    return forecast;
})
.WithName("GetWeatherForecast").RequireAuthorization();
```

> 如上程式加入`.RequireAuthorization()`後，再重新瀏覽 weatherforecast endpoint，會回傳 Status: 401 Unauthorized 的錯誤訊息。

![image](https://user-images.githubusercontent.com/21993717/168756602-7019db97-5f36-41bf-9e47-a6e14150ab36.png)

> 使用 Postman，先加入 Authorization Header，並將登入成功後回傳的 Token 加到 Authorization Header 中。再次送出就可正常的取得所有天氣預測資料了。

![image](https://user-images.githubusercontent.com/21993717/168757528-61bac493-c21d-468e-87f6-ffa1adad4f27.png)

> 加入 git 版本控制

```bash
$ git commit -m "finished JWT function" -a
```

> 完整程式如下(Program.cs)

```cs {hl_lines}
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;

var builder = WebApplication.CreateBuilder(args);

ConfigurationManager _configuration = builder.Configuration;
var secret = _configuration.GetValue<string>("JwtSettings:Secret");

// Add services to the container.
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

builder.Services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
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
            ValidIssuer = _configuration.GetValue<string>("JwtSettings:Issuer"),

            // 驗證 Audience (通常不太需要)
            ValidateAudience = false,
            //ValidAudience = "JwtAuthDemo", // 不驗證就不需要填寫

            // 驗證 Token 的有效期間 (一般都會)
            ValidateLifetime = true,

            // 如果 Token 中包含 key 才需要驗證，一般都只有簽章而已
            ValidateIssuerSigningKey = false,

            // 應該從 IConfiguration 取得
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secret))
        };
    });

builder.Services.AddAuthorization();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthentication();
app.UseAuthorization();

var summaries = new[]
{
    "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
};

app.MapGet("/weatherforecast", () =>
{
    var forecast =  Enumerable.Range(1, 5).Select(index =>
        new WeatherForecast
        (
            DateTime.Now.AddDays(index),
            Random.Shared.Next(-20, 55),
            summaries[Random.Shared.Next(summaries.Length)]
        ))
        .ToArray();
    return forecast;
})
.WithName("GetWeatherForecast").RequireAuthorization();

app.MapPost("/signin", (LoginViewModel login) =>
    {
        if (ValidateUser(login))
        {
            var token = CreateToken(login);
            return Results.Ok(new { token });
        }
        else
        {
            return Results.BadRequest();
        }
    }
).WithName("SignIn").AllowAnonymous();

string CreateToken(LoginViewModel user)
{
    List<Claim> claims = new List<Claim>
    {
        new Claim(ClaimTypes.Name, user.Username),
        new Claim(ClaimTypes.Role, "Admin"),
        new Claim(ClaimTypes.Role, "Users"),
        new Claim("ProjectType", "TTG"),
    };

    var secretkey = new SymmetricSecurityKey(System.Text.Encoding.UTF8.GetBytes(secret));    
    // _configuration.GetSection("JwtSettings:Secret").Value)

    var credentials = new SigningCredentials(secretkey, SecurityAlgorithms.HmacSha512Signature);

    var token = new JwtSecurityToken(   // 亦可使用　SecurityTokenDescriptor　來産生 Token
        issuer: _configuration.GetValue<string>("JwtSettings:Issuer"),
        audience: _configuration.GetValue<string>("JwtSettings:Audience"),
        claims: claims,
        expires: DateTime.Now.AddDays(1),
        signingCredentials: credentials);

    var jwt = new JwtSecurityTokenHandler().WriteToken(token);

    return jwt;
}

bool ValidateUser(LoginViewModel login)
{
    return login.Username == "cal" ? true : false;
}

app.MapGet("/claims", (ClaimsPrincipal user) =>
    {
        return Results.Ok(user.Claims.Select(p => new { p.Type, p.Value }));
    })
    .WithName("Claims")
    .RequireAuthorization();

app.MapGet("/username", (ClaimsPrincipal user) =>
    {
        return Results.Ok(user.Claims.FirstOrDefault(p => p.Type == ClaimTypes.Name)?.Value);
    })
    .WithName("Username")
    .RequireAuthorization();

app.MapGet("/roles", (ClaimsPrincipal user) =>
    {
        return Results.Ok(user.Claims.Select(p => new { p.Type, p.Value }).Where( c=> c.Type == ClaimTypes.Role));
    })
    .WithName("Userrole")
    .RequireAuthorization();

app.MapGet("/issuer", (ClaimsPrincipal user) =>
    {
        return Results.Ok(user.Claims.FirstOrDefault(p => p.Type == "iss")?.Value);
    })
    .WithName("Issuer")
    .RequireAuthorization();

await app.RunAsync();

record WeatherForecast(DateTime Date, int TemperatureC, string? Summary)
{
    public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
}

record LoginViewModel(string Username, string Password);
```

### 使用 OpenApi Swagger 來測試 API

> 將程式碼中的 `builder.Services.AddSwaggerGen();`改成以下內容

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
