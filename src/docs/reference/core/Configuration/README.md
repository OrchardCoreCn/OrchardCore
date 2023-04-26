# 配置

Orchard Core通过将`IShellConfiguration`与ASP.NET Core `IConfiguration`扩展，允许在应用程序范围之上进行特定于租户的配置。

要了解有关ASP.NET Core `IConfiguration`的更多信息，请访问<https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration>。

请注意，虽然此文档页面在`OrchardCore.Cms.Web.csproj`的示例中解释了根Web应用程序项目中发生的配置，但如果您在自己的Web应用程序中使用Orchard NuGet包，则同样可在该Web应用程序项目中使用。

## 配置源

Orchard Core支持配置源的层次结构

* `Startup` ASP.NET Core项目，例如`OrchardCore.Cms.Web.csproj`，`appsettings.json`或通过环境`appsettings.Development.json`。
* 全局租户配置`App_Data/appsettings.json`，或通过环境`App_Data/appsettings.Development.json`。
* 位于每个租户文件夹中的单个租户配置文件`App_Data/Sites/{tenant_name}/appsettings.json`。 **注意：**这些是可变文件，不支持环境版本。
* 环境变量，或通过Azure作为环境变量的AppSettings。

配置源按上述顺序加载，并且层次结构较低的设置将覆盖较高的设置，即全局租户值始终会被环境变量覆盖。

!!! 注意
    `appsettings.json`示例中的`IShellConfiguration`模式仅适用于专门支持此类配置的模块。您可以查看给定模块的代码或文档，以确定是否是这种情况。

### `OrchardCore.Cms.Web.csproj`启动项目中的`IShellConfiguration`

Orchard Core将所有配置数据存储在`appsettings.json`文件的`OrchardCore`部分下：

```json
{
  "OrchardCore": {
      ... 模块配置 ...
  }
}
```

每个Orchard Core模块都有自己的配置部分，在`OrchardCore`部分下：

```json
{
  "OrchardCore": {
    "OrchardCore_Media": {
      ... 单个模块配置 ...
    }
  }
}
```

有关更多示例，请参见`appsettings.json`文件。

### 租户预配置

要在创建租户之前预配置租户的设置值，可以指定一个以租户命名的部分，其中`State`值为`Uninitialized`

```json
{
  "OrchardCore": {
    "MyTenant": {
      "State": "Uninitialized",
      "RequestUrlPrefix": "mytenant",
      "ConnectionString": "...",
      "DatabaseProvider": "SqlConnection"
    }
  }
}
```

预配置的租户将出现在管理中的`Tenants`列表中，并且在设置租户时将使用这些值。

### 租户后配置

要在创建租户后为租户配置值，可以指定一个以租户命名的部分，而无需提供状态值。

```json
{
  "OrchardCore": {
    "Default": {
      "OrchardCore_Media": {
        ... 特定租户配置 ...
      }
    }
  }
}
```

### 全局租户数据访问配置

如果您希望所有租户都访问同一个数据库，则可以将相应的配置保留在单个位置，而不是逐个设置所有租户的相同连接字符串，如下所示：

```json
{
  "OrchardCore": {
    "ConnectionString": "...",
    "DatabaseProvider": "SqlConnection",
    "Default" : {
      "State": "Uninitialized",
      "TablePrefix": "Default"
    }
  }
}
```

有关上述配置的说明：

- 请注意，虽然您可以为租户使用相同的配置键，如前面所示，但这是在`OrchardCore`部分的根目录中进行的。
- 将数据库的连接字符串添加到要由所有租户使用的数据库中。
- `DatabaseProvider`应与使用的数据库引擎相对应，示例是SQL Server的引擎之一。
- `TablePrefix`需要配置为默认租户使用的前缀，以便可以将表分开为每个租户（否则只有默认租户的表将缺少前缀）。然后应使用不同的前缀设置其他租户。

这样，可以通过在给定环境中配置相应数据库的设置来轻松地将应用程序移动到不同的环境（例如暂存和生产环境）。租户的shell设置不会包含此信息，所有租户都将使用相同的全局配置。

相关主题是[Shells Configuration Providers](../Shells/README.md)。特别是有关如何将所有shell配置保存在数据库中的Database Shells Configuration Provider部分。

### `IOptions`配置

您还可以在Web项目的`Startup`类中从代码中配置`IOptions`，如[ASP.NET文档](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options)中所述。

许多Orchard Core功能通过在数据库中存储站点设置和/或通过`IOptions`公开配置来配置。如果要覆盖站点设置或默认设置，则可以使用自己的配置代码来执行此操作。

例如，电子邮件模块允许通过`SmtpSettings`类配置SMTP，该类默认情况下从给定租户的站点设置中填充，如在管理中设置的那样。 
但是，您可以像这样从`Startup`类覆盖站点设置（请注意，我们使用`PostConfigure`来覆盖站点设置值，但如果模块不使用站点设置，则可以使用`Configure`）：

```csharp
services
    .AddOrchardCms()
    .ConfigureServices(tenantServices =>
        tenantServices.PostConfigure<SmtpSettings>(settings =>
        {
            settings.Port = 255;
        }));

// 或者，如果您想使用上面看到的IShellConfiguration：
services
    .AddOrchardCms()
    .ConfigureServices((tenantServices, serviceProvider) =>
    {
        // 在这里，您可以从注入的IConfiguration实例中获取配置值，而不是使用IShellConfiguration。虽然这也允许您访问标准的ASP.NET Core配置键，但它不支持上面详细介绍的所有分层源。
        var shellConfiguration = serviceProvider.GetRequiredService<IShellConfiguration>();
        var password = shellConfiguration.GetValue<string>("SmtpSettings:Password");

        tenantServices.PostConfigure<SmtpSettings>(settings =>
        {
            settings.Password = password;
        });
    });
```

!!! 注意
    `SmtpSettings`的此类配置已经通过`ConfigureEmailSettings`扩展方法可用，有关详细信息，请参见[电子邮件配置](../../modules/Email/README.md)。

这将使SMTP端口使用此配置，而不管在站点设置中定义的任何其他值。第二个示例的配置值可以来自例如下面的`appsettings.json`文件：

```json
{
  "OrchardCore": {
    "SmtpSettings": {
      "Password":  "password"
    }
  }
}
```

!!! 注意
    在管理中，不会有任何指示表明发生了此覆盖，显示的值仍将是在站点设置中配置的值，因此如果选择执行此操作，则需要让用户知道。

### `ORCHARD_APP_DATA`环境变量

`App_Data`文件夹的位置可以通过设置`ORCHARD_APP_DATA`环境变量进行配置。 
路径可以是相对于应用程序路径（./App_Data），绝对（/path/from/root）或完全限定（D:\Path\To\App_Data）。 
如果文件夹不存在，则应用程序将尝试创建它。

### 全局租户配置`App_Data/appsettings.json`中的`IShellConfiguration`

这些设置也可以位于`App_Data/appsettings.json`文件中（默认情况下未创建），并且指定的任何设置都将覆盖来自`Startup`项目的设置。

### 单个租户文件夹中的`IShellConfiguration`

这些设置是可变的，并在设置租户期间编写。因此，不支持从环境名称读取。此外，这些`appsettings.json`文件不需要`OrchardCore`部分

```json
{
  "OrchardCore_Media": {
    ... 特定租户配置 ...
  }
}
```

### 通过环境变量的`IShellConfiguration`

环境变量也会转换为`IShellConfiguration`，例如

```
OrchardCore__OrchardCore_Media__MaxFileSize

OrchardCore__Default__OrchardCore_Media__MaxFileSize

OrchardCore__MyTenant__OrchardCore_Media__MaxFileSize
```

!!! 注意
    为了支持Linux，使用下划线`_`作为分隔符，例如`OrchardCore_Media`
    `OrchardCore.Media`支持向后兼容性，但用户应迁移到`_`模式。

### 层次结构的顺序

默认情况下，Orchard Core网站将使用`Program.cs`中的`CreateDefaultBuilder`加载以下顺序的`IConfiguration`

1. 启动项目`appsettings.json`
2. 启动项目`appsettings.{environment}.json`
3. 用户秘密（如果环境为__Development__）
4. 环境变量
5. 命令行参数
6. `IShellConfiguration`然后添加这些
    1. `App_Data/appsettings.json`
    2. 特定租户的`App_Data/Sites/{tenant_name}/appsettings.json`

!!! 注意
    加载后的具有相同键的配置优先于先前加载的配置（最后获胜）。

### 部署期间的配置

Azure App设置在Windows环境或Linux环境中作为环境变量受支持。

Azure DevOps或其他CI / CD管道在所有平台上都受支持，并且可以使用Json Path转换来转换`appsettings.json`文件并从管道变量或Azure Key Vault等秘密密钥存储中提供应用程序秘密。

如果使用预览包feed中的夜间dev构建进行构建，则CI / CD管道将需要使用具有`MyGet`包feed的`NuGet.Config`的位置。

```xml
<configuration>
  <packageSources>
    <add key="nuget" value="https://api.nuget.org/v3/index.json"/>
    <add key="preview" value="https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json" />
  </packageSources>
</configuration>
```

### 替代位置

存储在`App_Data`文件夹中的`IShellConfiguration`值以及单个租户的`appsettings.json`文件也可以存储在替代位置。

有关此的更多详细信息，请参见[Shells部分](../Shells/README.md)。
