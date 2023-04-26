# Shells 配置提供程序

Azure Shells 配置和 Database Shells 配置提供程序允许在 Orchard Core 主机外部的环境中托管 shell / 租户 `tenants.json` 文件和相关租户 `appsettings.json` 配置设置。

默认情况下，`Default` shell 和任何配置的租户的 `tenants.json` 和相关的 `appsettings.json` 存储在 `App_Data` 文件夹中。

`App_Data` 文件夹中的此配置适用于大多数站点，但对于无状态多租户环境的高级配置，其中多个主机需要对这些共享配置设置进行写访问，您可以选择使用 Azure Shells 配置或 Database Shells 配置提供程序之一。

Shell 配置提供程序的主要目的是为多租户提供共享的外部环境，在无状态主机的实时操作期间需要创建租户并改变其设置。

它不旨在支持本地开发和生产环境之间的共享配置。

## Azure Shells 配置提供程序

Azure Shells 配置提供程序使用 Azure Blob 存储容器来存储 `tenants.json` 和相关租户 `appsettings.json` 文件，其层次结构类似于默认的 `App_Data` 配置。

Azure Blob 容器的根目录包括一个 `tenants.json` 文件，还可以包括一个 `appsettings.json` 文件。

每个 shell 或租户在 `Sites` 文件夹下都有一个目录，以租户命名，并带有一个单独的 `appsettings.json` 文件。

层次结构分为单个文件，并且如果您需要独立于 Orchard Core 管理租户的 `appsettings.json`，则非常有用。例如，当使用 Azure 媒体存储功能时，您可能更喜欢为每个租户提供不同的 Azure Blob 存储密钥。

Azure Shells 配置支持根 `appsettings.json` 和 `appsettings.Environment.json` 文件。

!!! 注意
    单个租户不支持 `appsettings.Environment.json` 文件。

### 启用 Azure Shells 配置

Azure Shells 配置由单独的 NuGet 包提供：`OrchardCore.Shells.Azure`

Azure Shells 配置通过 web 主机项目中的 `appsettings.json` 部分进行配置。

``` json
{
  "OrchardCore": {
    "OrchardCore_Shells_Azure": {
      "ConnectionString": "", // 设置为 Azure 存储帐户连接字符串。
      "ContainerName": "hostcontainer", // 设置为 Azure Blob 容器名称。
      "BasePath": "some/base/path", // 可选地，设置为容器内的子目录。
      "MigrateFromFiles": true // 可选地，启用以自动将现有的 App_Data 文件迁移到 Blob。
    }
  }
}
```

在 web 主机 `Startup.cs` 中，它通过 Orchard Core Builder 上的扩展方法启用。

``` csharp
namespace OrchardCore.Cms.Web
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddOrchardCms()
                .AddAzureShellsConfiguration();
        }
    }
}
```

!!! 注意
    必须在使用 Azure Shells 配置提供程序之前创建容器。
    确保此容器是安全的，并且对密钥的访问受到限制。

## Database Shells 配置提供程序

Database Shells 配置提供程序使用任何支持的数据库将所有与租户相关的配置存储为单个 json 文档的一部分。

Database Shells 配置提供程序不支持站点 `appsettings.json` 和 `appsettings.Environment.json` 文件。

### 启用 Database Shells 配置

Database Shells 配置通过 web 主机项目中的 `appsettings.json` 部分进行配置。

``` json
{
  "OrchardCore": {
    "OrchardCore_Shells_Database": {
      "DatabaseProvider": "SqlConnection", // 设置为受支持的数据库提供程序。
      "ConnectionString": "", // 设置为数据库连接字符串。
      "TablePrefix": "", // 可选地，配置表前缀。
      "MigrateFromFiles": true // 可选地，启用以自动将现有的 App_Data 文件迁移到数据库。
    },
  }
}
```

在 web 主机 `Startup.cs` 中，它通过 Orchard Core Builder 上的扩展方法启用。

``` csharp
namespace OrchardCore.Cms.Web
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddOrchardCms()
                .AddDatabaseShellsConfiguration();
        }
    }
}
```

!!! 注意
    必须在使用 Database 配置提供程序之前创建数据库。
    确保此数据库是安全的，并且对其的访问受到限制。

## 从文件迁移

Azure Shells 和 Database Shells 配置提供程序都提供了 `MigrateFromFiles` 选项，以帮助从现有的 `App_Data` 配置迁移。

启用时，Shell 配置提供程序将首先检查给定租户的配置是否存在于所选存储平台（Database 或 Azure Blob Storage）中。

如果配置不存在，则提供程序将尝试从 `App_Data` 文件夹中加载它，并将其迁移到所选的存储平台。

## 环境选项

要在开发或不同环境中禁用提供程序，请注入 `IHostEnvironment` 

``` csharp
namespace OrchardCore.Cms.Web
{
    public class Startup
    {
        private readonly IHostEnvironment _env;

        public Startup(IHostEnvironment env)
        {
            _env = env;
        }

        public void ConfigureServices(IServiceCollection services)
        {
            var builder = services.AddOrchardCms();
            if (!_env.IsDevelopment())
            {
                builder.AddDatabaseShellsConfiguration();
            }
        }
    }
}
```

该文档由ChatGPT 4 翻译
