---
title: "ASP.NET Core 5 Web API 加入權限管理 (Dotnet Core Identity Framework + JWT)"
date: 2022-01-20
draft: false
description: "透過 Identity Framework 與 JWT 來為 ASP.NET Core 5.0 加入使用者權限管理功能"
tags: ["dotnet 5", "webapi", "Identity Framework", "JWT"]
categories: ["webapi"]
---

在另一篇筆記中 *[ASP.NET Core 5 Web API - 從無到有](https://calvinegs.github.io/posts/dotnet5-webapi/)* 已經了解如何從無到有 使用 Dotnet core 5.0 建立 一個 Web API，本篇筆記將以此有基礎來記錄如何使用 Asp.Net Core Identity framework 及 JWT 來建置一個簡單又安全的 "使用者權限管理"功能。

*[github Source code #tag: web_api](https://github.com/calvinegs/DotNet-Web-Api-5.git)*

## 使用 Asp.Net Core Identity framework 來管理使用者使用權限

ASP.NET Core Identity:

- 支援使用者介面 (UI) 登入功能的 API。
- 管理使用者、密碼、設定檔資料、角色、宣告、權杖、電子郵件確認等。

ASP.Net Core Identity Framework 是一個很方便且還算完善的使用權限管理架構。

## 安裝給 AspNetCore Idendity Framework 使用的相關套件

```bash
$ dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore --version 5.0.13
$ dotnet add package Microsoft.AspNetCore.Identity.UI --version 5.0.13
```

除了安裝相關套件外，還要調整相關程式:

- 在 Startup.cs 檔案中將 AspNetCore Identity Service 注入到 container 中 (before services.AddControllers())

```cs
    # 在 startup.cs 檔案中 services.AddControllers() 指令前加入以下指令
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApiDbContext>();

```

- 在 Startup.cs 檔案中 HTTP request pipeline 中 新加入 UseAuthentication()

```cs
  # 在 startup.cs 檔案中 app.UseAuthorization() 指令前加入以下指令
  app.UseAuthentication();
```

- 使用 AspNetCore Identity，則 DataContext (ApiDbContext.cs 中) 必須要繼承
  IdentityDbContext， 同時 Model creationg 時要改成呼叫 base.OnModelCreation

```cs  {hl_lines=[1,3,13],linenostart=1}
public partial class ApiDbContext : IdentityDbContext
{
    public DbSet<ItemData> ItemData { get; set; }
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

## 新增一個 entity framework 遷移 並 更新資料庫

完成上述程式調整後，來執行資料庫遷移(migrations)

```bash
  $ dotnet build
  $ dotnet ef migrations add "Add Identity Framework"
  $ dotnet ef database update (app.db 產生 Identity Framework 會使用到的資料表)
```

![image](https://user-images.githubusercontent.com/21993717/150063728-21bf7eaa-509c-4ede-8920-e4699308d726.png)

## 新增 使用者註冊和登入時使用的 Data model class (Models/AuthData.cs)

```cs
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace Todo5.Models
{
    public class RegistrationResponse
    {
        public string Token { get; set; }
        public bool Success { get; set; }
        public List<string> Errors { get; set; }
    }

    public class UserLoginRequest
    {
        [Required]
        [EmailAddress]
        public string Email { get; set; }
        [Required]
        public string Password { get; set; }
    }

    public class UserRegistrationDto
    {
        [Required]
        public string Username { get; set; }
        [Required]
        [EmailAddress]
        public string Email { get; set; }
        [Required]
        public string Password { get; set; }
    }
}
```

## 新增 註冊和登入邏輯 (Controllers/AuthManagementControll.cs)

```cs
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Mvc;
using Todo5.Models;

namespace Todo5.Controllers
{
    [Route("api/[controller]")] // api/authManagement
    [ApiController]
    public class AuthManagementController : ControllerBase
    {
        private readonly UserManager<IdentityUser> _userManager;

        public AuthManagementController(
            UserManager<IdentityUser> userManager)
        {
            _userManager = userManager;
        }

        [HttpPost]
        [Route("Register")]
        public async Task<IActionResult> Register([FromBody] UserRegistrationDto user)
        {
            if (ModelState.IsValid)
            {
                // We can utilise the model
                var existingUser = await _userManager.FindByEmailAsync(user.Email);

                if (existingUser != null)
                {
                    return BadRequest(new RegistrationResponse()
                    {
                        Errors = new List<string>() {
                                "Email already in use"
                            },
                        Success = false
                    });
                }

                var newUser = new IdentityUser() { Email = user.Email, UserName = user.Username };
                var isCreated = await _userManager.CreateAsync(newUser, user.Password);
                if (isCreated.Succeeded)
                {
                    return Ok(new RegistrationResponse()
                    {
                        Success = true,
                    });
                }
                else
                {
                    return BadRequest(new RegistrationResponse()
                    {
                        Errors = isCreated.Errors.Select(x => x.Description).ToList(),
                        Success = false
                    });
                }
            }

            return BadRequest(new RegistrationResponse()
            {
                Errors = new List<string>() {
                        "Invalid payload"
                    },
                Success = false
            });
        }

        [HttpPost]
        [Route("Login")]
        public async Task<IActionResult> Login([FromBody] UserLoginRequest user)
        {
            if (ModelState.IsValid)
            {
                var existingUser = await _userManager.FindByEmailAsync(user.Email);

                if (existingUser == null)
                {
                    return BadRequest(new RegistrationResponse()
                    {
                        Errors = new List<string>() {
                                "Invalid login request"
                            },
                        Success = false
                    });
                }

                var isCorrect = await _userManager.CheckPasswordAsync(existingUser, user.Password);

                if (!isCorrect)
                {
                    return BadRequest(new RegistrationResponse()
                    {
                        Errors = new List<string>() {
                            "Invalid login request"
                        },
                        Success = false
                    });
                }

                return Ok(new RegistrationResponse()
                {
                    Success = true,
                });
            }

            return BadRequest(new RegistrationResponse()
            {
                Errors = new List<string>() {
                        "Invalid payload"
                    },
                Success = false
            });
        }
    }
}
```

使用者帳號註冊
![image](https://user-images.githubusercontent.com/21993717/150066349-09098e03-7cfc-4900-b2a0-1ed43002e050.png)

註冊成功
![image](https://user-images.githubusercontent.com/21993717/150066441-99be8b28-3c5a-47f7-8c4e-a79b5841776e.png)

使用已註冊成功帳號來進行登入
![image](https://user-images.githubusercontent.com/21993717/150067581-684bc3a7-9114-419f-a871-d1f0cc89f96b.png)

## 建立 git 新版本

```bash
$ git add . && git commit -m "Add Asp.Net Core Identity framework"
```

## 新增 JWT 功能

到目前為止我們已透過 Dotnet Core Identity framework 完成了簡易的使用者資料驗證的功能(註冊/登入)，下面要繼續完成採用 JWT 進行 Token-based 的身分驗證與授權實作。

JWT(Json Web Token)是一個實現授權功能上相對簡單又安全方式。實作的步驟包含了三個部分：

- 產生合法有效的 JWT Token
- 驗證合法有效的 JWT Token
- 限制特定 API 只能在通過 JWT 驗證的 HTTP 要求才能存取

首先，先加入相關套件:

```bash
$ dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer --version 5.0.13
$ dotnet add package Microsoft.IdentityModel.Tokens --version 6.11.1
```

## 產生合法有效的 JWT Token

```cs
private string GenerateJwtToken(IdentityUser user)
{
    var key = Encoding.ASCII.GetBytes(_appSettings.Secret);

    var claims = new ClaimsIdentity(new []
    {
        new Claim("Id", user.Id),
        new Claim(JwtRegisteredClaimNames.Email, user.Email),
        new Claim(JwtRegisteredClaimNames.Sub, user.Email),
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
    });

    var tokenDescriptor = new SecurityTokenDescriptor
    {
        Subject = claims,
        Expires = DateTime.UtcNow.AddHours(6),
        SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature)
    };

    var jwtTokenHandler = new JwtSecurityTokenHandler();
    var token = jwtTokenHandler.CreateToken(tokenDescriptor);
    var jwtToken = jwtTokenHandler.WriteToken(token);

    return jwtToken;
}
```

## 驗證合法有效的 JWT Token

先在 Helpers 目錄下新增 AppSettings Class 用來存放 JWT 使用到的 Key

```cs
namespace Todo5.Helpers
{
    public class AppSettings
    {
        public string Secret { get; set; }
    }

}
```

並在 appsettings.json 設定檔中加入 Secret 值:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=app.db; Cache=Shared"
  },
  "AppSettings": {
    "Secret": "This is a test for Authorization in asp.net webapi. Using JWT Technology to keep user info."
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

再在 Startup.cs 檔案中注入 "驗證合法有效 JWT Token 的功能"

```cs
// 開始: 為 JWT 新增的程式碼
services.Configure<AppSettings>(Configuration.GetSection("AppSettings"));
var key = Encoding.ASCII.GetBytes(Configuration["AppSettings:Secret"]);
var TokenValidationParameters = new TokenValidationParameters
{
    ValidateIssuerSigningKey = true, // this will validate the 3rd part of the jwt token using the secret that we added in the appsettings and verify we have generated the jwt token
    IssuerSigningKey = new SymmetricSecurityKey(key), // Add the secret key to our Jwt encryption
    ValidateIssuer = false,
    ValidateAudience = false,
    ValidateLifetime = true,
    RequireExpirationTime = false,
};
services
    .AddAuthentication(options =>
    {
        options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
        options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    })
    .AddJwtBearer(cfg =>
    {
        cfg.RequireHttpsMetadata = false;
        cfg.TokenValidationParameters = TokenValidationParameters;
    });
// 結束: 為 JWT 新增的程式碼
```

## 限制特定 API 只能在通過 JWT 驗證的 HTTP 要求才能存取

在欲限制的功能(function in Controller)前加入 "[Authorize]" 屬性 (TodoController.cs 程式中)

```cs
// GET: api/Todo
[HttpGet]
[Authorize] # 加入屬性
public async Task<ActionResult<IEnumerable<ItemData>>> GetItemData()
{
    return await _context.ItemData.ToListAsync();
}
```

## 為 Swagger 加入 JWT 功能

程式到此已完成加入 JWT 功能，下面是為了使用 Swagger 進行 API 測試時，可以有 JWT 相關功能而調整的部份

```cs
// 註解掉原有程式碼 (以下三行)
// services.AddSwaggerGen(c =>
// {
//     c.SwaggerDoc("v1", new OpenApiInfo { Title = "Todo5", Version = "v1" });
// });

// 改成下面的程式，讓 Swagger 具備有 JWT 的相關功能
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "TodoApp", Version = "v1" });
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

在 Swagger 的 UI 中，畫面右上角多出了 "Authorize" 按鈕。此功能是用來記錄 登入成功後所回傳的 Token

![image](https://user-images.githubusercontent.com/21993717/150116431-00e54773-fa90-4fea-a001-5b10f3706548.png)

此刻 API 已要求要擕帶有合法 Token，若在未透過"Authorize"功能填入 Token 時來操作 API，將會回傳"未授權"的錯誤。如下圖:
![image](https://user-images.githubusercontent.com/21993717/150155450-0f5ad464-4983-49ac-b12d-716e64251b0b.png)

先以合法使用者帳號登入，在登入成功後，回傳的 Response body 中會帶有 Token，將此 Token 值複製下來
![image](https://user-images.githubusercontent.com/21993717/150154988-ceddd518-9f1f-4109-8a31-aee4fccc1871.png)

按下 Authorize 按鈕後，填入該 Token 值，並在 Token 值前鍵入 "bearer "
![image](https://user-images.githubusercontent.com/21993717/150154759-f9e06f71-ba5c-47c0-b649-8f3817ccd8f9.png)

再次操作 Get API，即可成功取得回傳值。
![image](https://user-images.githubusercontent.com/21993717/150155282-969d23a6-f163-4b89-83da-edf46a0699eb.png)
