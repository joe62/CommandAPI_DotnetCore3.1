# The Complete ASP.NET Core 3 API

<The Complete ASP.NET Core 3 API Tutorial Hands-On Building, Testing, and Deploying by Les Jackson (z-lib.org).pdf>

## Setting Up Your Development Environment

`dotnet dev-certs https --trust`

`vs code` [C# 插件](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [Insert GUID](https://marketplace.visualstudio.com/items?itemName=heaths.vscode-guid) 

```bash
// c:\temp\how2react\CommandAPISolution\
src>dotnet new web -n CommandAPI
test>dotnet new xunit -n CommandAPI.Tests
dotnet new sln --name CommandAPISolution
dotnet sln CommandAPISolution.sln add src/CommandAPI/CommandAPI.csproj test/CommandAPI.Tests/CommandAPI.Tests.csproj
dotnet add test/CommandAPI.Tests/CommandAPI.Tests.csproj reference src/CommandAPI/CommandAPI.
csproj
```

```bash
c:\temp\CommandAPISolution>tree
卷 SSD_OS 的文件夹 PATH 列表
卷序列号为 069F-BFD4
C:.
├─src
│  └─CommandAPI
│      ├─obj
│      └─Properties
└─test
    └─CommandAPI.Tests
        └─obj
```

<img src="https://img2020.cnblogs.com/blog/30756/202011/30756-20201104111332044-1141433564.png" alt="image-20201104111327547" style="zoom:50%;" />



```sh
cd src\CommandAPI
dotnet run

```

http://localhost:5000

<img src="https://img2020.cnblogs.com/blog/30756/202011/30756-20201104113431792-838441554.png" alt="image-20201104113428297" style="zoom:50%;" />



## Models, DTO (Data Transfer Objects), Repositories, and Data Access

- `Model`: Represents the internal domain data of our application (the "M" in MVC)
- `DTOs`: 向外展示必需的信息而不是全部models
- `Data Access (aka DB Context = Entity Framework Core)`:   指定Models对应到特定存储层（PostgreSQL, SQL Server, etc.)
- `Repository`:  数据存储



## Setting Up Your Local Git Repo

```
git init
git status
git add .
git commit -m "Initial Commit"

git remote add origin https://github.com/joe62/CommandAPI
git push -u origin master
```



## PostgreSQL Database with Docker

```sh
docker run --name some-postgres -e POSTGRES_PASSWORD=Videocomm1 -p 5432:5432 -d postgres
docker ps

```

## Entity Framework Command-Line Tools

```sh
dotnet tool install --global dotnet-ef
dotnet ef

dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL

dotnet ef migrations remove
dotnet ef migrations add AddCommandsToDb
dotnet ef database update
```

## Environment Variables and User Secrets

`appsettings.json->Appsettings.Development.json->Secrets.json->Environment Vairables -> Command Line Args`

```xml
// src\CommandAPI\CommandAPI.csproj
  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
    <UserSecretsId>052308e3-81dd-4877-b321-a0cc06490566</UserSecretsId>
  </PropertyGroup>
```

```sh
dotnet user-secrets set "UserID" "sa"
dotnet user-secrets set "Password" "Videocomm1"
cat %APPDATA%\Microsoft\UserSecrets\052308e3-81dd-4877-b321-a0cc06490566\Secrets.json
{
  "UserID": "sa",
  "Password": "Videocomm1"
}
```

`Linux/OSX: ~/.microsoft/usersecrets/052308e3-81dd-4877-b321-a0cc06490566/secrets.json`

```json
// src\CommandAPI\appsettings.Development.json
  "ConnectionStrings": {
    "PostgreSqlConnection": "Host=localhost;Port=5432;Database=CmdAPI;Pooling=true;"
  }
```

```c#
// Startup.cs
    public void ConfigureServices(IServiceCollection services)
    {
      var builder = new NpgsqlConnectionStringBuilder();
      builder.ConnectionString = Configuration
        .GetConnectionString("PostgreSqlConnection");
      builder.Username = Configuration["UserID"];
      builder.Password = Configuration["Password"];
      services.AddDbContext<CommandContext>(opt => opt.UseNpgsql(builder.ConnectionString));
      services.AddControllers();
      services.AddScoped<ICommandAPIRepo, SqlCommandAPIRepo>();
    }
```



## DTOs (Data Transfer Objects)

```sh
dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection
dotnet add package Microsoft.AspNetCore.JsonPatch
dotnet add package Microsoft.AspNetCore.Mvc.NewtonsoftJson
```



## Unit Testing Our API

> automated regression suite, fast, isolated, repeatable, self=checking, timely, focused, xUnit



```
cd  test\CommandAPI.Tests
dotnet add package Microsoft.EntityFrameworkCore.Relational 
dotnet add package Moq
dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection
```

## CI/CD

- CI很容易； 代表持续集成。 CI是从一个或多个使用同一软件的开发人员那里进行任何代码更改，然后通过构建和测试该代码将这些更改合并回主代码“分支”的过程。 顾名思义，此过程是连续的，通常在开发人员将“检入”代码更改为代码存储库时触发（就像您对Git / GitHub所做的那样）。CI的全部目的是确保主（或主）代码分支在整个构建活动中保持健康，并且由从事该代码的多个开发人员引入的任何新更改都不会冲突并破坏构建。
- CD可能会更加混乱。 为什么？ 我们会听到人们同时使用以下两个术语来表示CD：持续交付和持续部署。

![image-20201106135752163](https://img2020.cnblogs.com/blog/30756/202011/30756-20201106135756696-838710033.png)

Figure 12-1. Continuous: integration, delivery, and deployment



[1]: https://github.com/binarythistle/Complete-ASP-NET-3-API-Tutorial-Book
[2]: https://github.com/binarythistle/Secure-Daemon-Client
[source code]: https://github.com/binarythistle/Complete-ASP-NET-3-API-Tutorial-Book
[DBeaver]: https://dbeaver.io/download/