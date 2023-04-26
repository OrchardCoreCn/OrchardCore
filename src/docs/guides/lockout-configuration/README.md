# 如何更改锁定配置

锁定设置使用ASP.NET Identity中配置的设置进行设置。这些选项用于定义例如锁定发生时用户被锁定的时间间隔，或允许在用户被锁定之前的失败访问尝试次数。

有关ASP.NET Core Identity中LockoutOptions的文档可在此处找到：  
https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions

## 从外部配置文件读取配置

更好的方法是从配置源读取配置，如设置文件，环境变量，命令行参数。有关默认配置提供程序使用的默认配置源的完整列表以及如何自定义它，请参见相关的ASP.NET Core文档：  
https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/

例如，使用`appsettings.{Environment}.json`设置文件，如`appsettings.json`，`appsettings.Production.json`和`appsettings.Development.json`，将允许您根据环境指定不同的设置并在部署时转换配置节。

为此，在`Startup`类中添加一个`Configuration`属性：

```csharp
public IConfiguration Configuration { get; }
```
为了初始化 `Configuration`，请在构造函数中设置属性，如下所示：

```csharp
public Startup(IConfiguration configuration)
{
    Configuration = configuration;
}
```

然后，使用以下代码将配置绑定到从配置文件中读取的配置：

```csharp
services.Configure<IdentityOptions>(options =>
{
    Configuration.GetSection("IdentityOptions").Bind(options);
});
```

最后，在名为 `appsettings.json` 的文件中创建此配置的 JSON，如下所示：
## 概述

您刚刚学习了如何从`appsettings.json`文件配置锁定设置。 

```json
{  
  "IdentityOptions": {
    "Lockout": {
      "AllowedForNewUsers": true,
      "DefaultLockoutTimeSpan ": "00:05:00",
      "MaxFailedAccessAttempts ": 5
    }
  }
}
```

上述代码展示了锁定设置的示例配置，包括允许新用户锁定、默认锁定时长和最大登录失败次数。


> 该文档由Chat-GPT 翻译
