---
title: "ASP.NET Core 6 Web API 使用 Dapper ORM 連結 Oracle"
date: 2022-01-28
draft: false
description: "採用 Dapper 這個小而美的 ORM 架構並以 Repository Pattern 來撰寫 ASP.NET Core 6 Web Api"
tags: ["dotnet 6", "webapi", "dapper", "repository", "jwt"]
categories: ["webapi"]
---

當已有現存的資料庫(此以 Oracle 為例)並且存在有歷史資料，或與其他系統共用資料庫，在這前題下，通常無法隨意的去更改資料庫結構，這時若要採用 Entity Framework 架構就容易遇到資料庫結構正規化的問題。另一種情境是，舊系統使用了非常的SQL語法，要將這些邏輯"翻譯"成合理的 Entity Framework 架構有技術上或時間上的限制時，就會考慮使用 Dapper 這個輕量的 ORM 搭配上 SQL 語法。

```bash
$ dotnet new webapi -o OracleDapperRepository && cd OracleDapperRepository
$ dotnet build
$ dotnet run
$ git init && git add . && git commit -m "Initial commit"

$ dotnet new gitignore
$ dotnet add package Dapper --2.0.123   # 加入 Dapper package
$ dotnet add package Oracle.ManagedDataAccess.Core --3.21.50  # 加入 連結 Oracle package
$ git add . && git commit -m "Add Dapper & Oracle Data Access NuGet packages"

$ cat OracleDapperRepository.csproj
```
```json
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Dapper" Version="2.0.123" />
    <PackageReference Include="Oracle.ManagedDataAccess.Core" Version="3.21.50" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.2.3" />
  </ItemGroup>

</Project>
```

## Open VS Code

在 appsettings.json 檔案中加入 Connection String 

```json
{
    "ConnectionStrings": {
		"OracleConnection":"Data Source=192.0.0.1/ORCLCDB;User ID=username;Password=password"
    },
    //...
}
```

## 在專案目錄結構中建立新資料夾
在專案目錄中新增 Models 和 Repositories 二個目錄，並在 Repositories 目錄下建立一個 Interfaces 子目錄

## 在 Models 資料夾中新增 Data Model 
使用快捷鍵 Ctrl+N,C 來新增 Model class : Models\sysmstaf.cs

```cs
namespace OracleDapperRepository.Models
{
    public class sysmstaf
    {
        public string? STAFF_ID { get; set; }
        public int ROLE_ID { get; set; }
        public string? PASS_TX { get; set; }
        public string? LOGIN_TX { get; set; }

    }
}
```
## 在 Interfaces 目錄下新增一個 ISysmstafRepository.cs Interface file

```cs
using OracleDapperRepository.Models;

namespace OracleDapperRepository.Repositories.Interfaces
{
    public interface ISysmstafRepository
    {
        Task<sysmstaf> GetByStaffID(string staffId);
        Task<sysmstaf> GetByLoginID(string loginID);
        Task<IEnumerable<sysmstaf>> GetAllData();
    }
}
```

## 在 Repositories 目錄下新增 SysmstafRepository.cs

```cs
using System.Data;
using Dapper;
using Oracle.ManagedDataAccess.Client;
using OracleDapperRepository.Models;
using OracleDapperRepository.Repositories.Interfaces;

namespace OracleDapperRepository.Repositories
{
    public class SysmstafRepository : ISysmstafRepository
    {
        private readonly string _connectionString;

        public SysmstafRepository(string connectionStr)
        {
            _connectionString = connectionStr;
        }

        public IDbConnection Connection
        {
            get
            {
                return new OracleConnection(_connectionString);
            }
        }

        public async Task<sysmstaf> GetByLoginID(string loginID)
        {
            using (IDbConnection conn = Connection)
            {
                string sSQL = "select * from sysmstaf where login_tx= :LOGIN_ID";
                return await conn.QueryFirstOrDefaultAsync<sysmstaf>(sSQL, new { LOGIN_ID = loginID });
            }
        }

        public async Task<sysmstaf> GetByStaffID(string staffId)
        {
            using (IDbConnection conn = Connection)
            {
                string sSQL = "select * from sysmstaf where staff_id= :STAFF_ID";
                return await conn.QueryFirstOrDefaultAsync<sysmstaf>(sSQL, new { STAFF_ID = staffId });
            }
        }

        public async Task<IEnumerable<sysmstaf>> GetAllData()
        {
            using (IDbConnection conn = Connection)
            {
                string sSql = "select * from sysmstaf Order by login_tx";
                var result = await conn.QueryAsync<sysmstaf>(sSql);
                return result.ToList();
            }
        }
    }
}
```

## 在 Controllers 目錄下新增 SysmstafController.cs

```cs
using Microsoft.AspNetCore.Mvc;
using OracleDapperRepository.Models;
using OracleDapperRepository.Repositories.Interfaces;

namespace OracleDapperRepository.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class SysmstafController : ControllerBase
    {
        private readonly ISysmstafRepository _sysmstafRepo;

        public SysmstafController(ISysmstafRepository sysmstafRepo)
        {
            _sysmstafRepo = sysmstafRepo;
        }

        [HttpGet]
        [Route("StaffId/{staffId}")]
        public async Task<ActionResult<sysmstaf>> GetByStaffID(string staffId)
        {
            return await _sysmstafRepo.GetByStaffID(staffId);
        }

        [HttpGet]
        [Route("LoginId/{loginId}")]
        public async Task<ActionResult<sysmstaf>> GetByLoginID(string loginId)
        {
            return await _sysmstafRepo.GetByLoginID(loginId);
        }

        [HttpGet]
        [Route("GetAllData")]
        public async Task<IEnumerable<sysmstaf>> GetAllData()
        {
            return await _sysmstafRepo.GetAllData(); ;
        }
    }
}
```

## 在 Program.cs 中將 SysmstafRepository 注入 Container 中

```cs
using OracleDapperRepository.Repositories;
using OracleDapperRepository.Repositories.Interfaces;

var builder = WebApplication.CreateBuilder(args);

string ConnectionString = builder.Configuration.GetConnectionString("OracleConnection");    // 取得 appsettings.json 中的 ConnectionStrings 設定值

// Add services to the container.
builder.Services.AddTransient<ISysmstafRepository>(x => new SysmstafRepository(ConnectionString));  // 將

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();
// ...
```

## 透過 swagger (OpenAPI) 來看結果

![image](https://user-images.githubusercontent.com/21993717/151686300-bd9952d1-49a8-4e5c-bf8a-c67c6eea8123.png)


![image](https://user-images.githubusercontent.com/21993717/151686344-f3328b67-0e83-4205-babe-f03fb5927dac.png)

![image](https://user-images.githubusercontent.com/21993717/151686366-88b823fd-826e-4a98-af9c-f95be24d3d19.png)

*[github Source code #tag: dapper_oracle](https://github.com/calvinegs/dotnet6-webapi-oracle-dapper.git)*