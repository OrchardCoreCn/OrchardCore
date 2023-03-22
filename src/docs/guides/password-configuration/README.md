# 如何修改密码要求

密码限制是通过配置ASP.NET Identity中的设置来设置的。  
这些选项用于在设置用户密码时定义所需的密码强度。您可以配置这些要求，以指定属性，例如最小密码长度或处理是否期望数字、大写字母或非字符的属性。

## 在ConfigureServices中配置密码设置

最简单的方法是在`Startup`类的`ConfigureServices`方法中添加以下代码以更改这些设置：

```cs
 services.Configure<IdentityOptions>(options =>
 {
   options.Password.RequireDigit = false;
   options.Password.RequireLowercase = true;
   options.Password.RequireUppercase = true;
   options.Password.RequireNonAlphanumeric = false;
   options.Password.RequiredUniqueChars = 3;
   options.Password.RequiredLength = 6;
 });
```

!!! note
    这只是一个示例。您需要选择符合您安全要求的值。

有关ASP.NET Core Identity中PasswordOptions的文档，请参见以下链接：
https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.passwordoptions?view=aspnetcore-3.1

## 从外部配置文件中读取配置

更好的方法是从配置源读取配置，例如设置文件、环境变量、命令行参数。有关默认配置提供程序使用的默认配置源列表以及如何自定义其内容的完整列表，请参见相关的ASP.NET Core文档：
https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1

例如，使用 `appsettings.{Environment}.json` 文件（如 `appsettings.json`、`appsettings.Production.json` 和 `appsettings.Development.json`），可以根据环境指定不同的设置，并在部署时转换配置部分。

为此，在`Startup`类中添加一个 `Configuration` 属性：

```csharp
public IConfiguration Configuration { get; }
```
为了初始化`Configuration`，在构造函数中设置属性，如下所示：

```csharp
public Startup(IConfiguration configuration)
{
    Configuration = configuration;
}
```

然后，用从配置文件中读取的代码替换硬编码的配置：

```csharp
services.Configure<IdentityOptions>(options =>
{
    Configuration.GetSection("IdentityOptions").Bind(options);
});
```
 
最后，创建一个名为`appsettings.json`的文件，其中包含以下json配置：


```json
{
  "IdentityOptions": {
    "Password": {
      "RequireDigit": false,
      "RequireLowercase": true,
      "RequireUppercase": true,
      "RequireNonAlphanumeric": false,
      "RequiredUniqueChars": 3,
      "RequiredLength": 6
    }
  }
}
```

## 概述

您刚刚学习了如何从 `appsettings.json` 文件中配置密码设置。
# 如何更改Orchard Core网站的密码要求

在默认情况下，Orchard Core需要密码至少包含8个字符，其中必须包含一个大写字母，一个小写字母和一个数字。但是，有时您需要更改这些要求。

在这篇文章中，我们将学习如何更改Orchard Core网站的密码要求。

## 1. 创建自定义密码验证器

要更改密码要求，我们需要创建一个自定义密码验证器。这个自定义验证器将覆盖默认验证器并允许我们更改密码要求。

为此，请创建一个C#类，并从`OrchardCore.Users.Services.IPasswordValidator`接口继承它。实现必需的接口方法并添加自己的密码验证逻辑，如下所示：

```csharp
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.Localization;
using OrchardCore.Users.Models;
using OrchardCore.Users.Services;

public class CustomPasswordValidator : IPasswordValidator
{
    private readonly IStringLocalizer S;

    public CustomPasswordValidator(IStringLocalizer<CustomPasswordValidator> localizer)
    {
        S = localizer;
    }

    public Task<PasswordValidationResult> ValidatePasswordAsync(User user, string password)
    {
        var errors = new List<ValidationResult>();

        // perform your custom password validation and add errors to the errors list, if any

        if (errors.Count > 0)
        {
            return Task.FromResult(
                new PasswordValidationResult { Succeeded = false, Errors = errors });
        }

        return Task.FromResult(
            new PasswordValidationResult { Succeeded = true, Errors = errors });
    }
}
```

## 2. 注册自定义密码验证器

在Orchard Core中，您可以使用依赖注入（DI）容器注册服务。我们需要将我们的自定义密码验证器注册到DI容器中以供使用。

要注册我们的自定义验证器，请在您的模块的`Startup.cs`文件中添加以下代码：

```csharp
services.AddScoped<IPasswordValidator, CustomPasswordValidator>();
```

## 3. 更改配置

我们还需要更改Orchard Core的密码长度和要求配置。要更改这些配置，请在你的应用程序的`appsettings.json`文件中添加以下设置：

```json
"OrchardCore": {
  "Users": {
    "PasswordOptions": {
      "RequiredLength": 10,
      "RequiredUniqueChars": 3,
      "RequireNonAlphanumeric": false,
      "RequireLowercase": true,
      "RequireUppercase": true,
      "RequireDigit": true
    }
  }
}
```

在上面的配置中，您可以更改所需的密码长度，所需的唯一字符数，以及是否需要特殊字符、小写字母、大写字母和数字。

现在，当用户尝试更改密码时，将使用我们的自定义密码验证器。

如果您需要更改密码要求，请按照上述步骤创建自定义密码验证器、注册服务并更改配置。


> 该文档由Chat-GPT 翻译
