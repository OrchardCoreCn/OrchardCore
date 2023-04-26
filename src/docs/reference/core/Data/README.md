# 数据 (`OrchardCore.Data`)

## 配置数据库

大多数数据库配置都是自动处理的，但有一些有限的选项可能会影响数据库的工作方式。

### Sqlite

#### `UseConnectionPooling` (布尔值)

在 `.NET 6` 中，默认情况下，`Microsoft.Data.Sqlite` 对数据库进行连接池化。它通过锁定数据库文件并保留连接以供重用来实现这一点。如果锁定正在阻止备份等任务，则可以禁用此功能。

禁用连接池可能会带来性能损失。

有关更多详细信息，请参见 [`Microsoft.Data.Sqlite` 文档](https://docs.microsoft.com/en-us/dotnet/standard/data/sqlite/connection-strings#pooling)。

##### `appsettings.json`

```json
{
    "OrchardCore_Data_Sqlite": {
        "UseConnectionPooling": false
    }
}
```

## 配置 YesSql

OrchardCore 使用 `YesSql` 库与配置的数据库提供程序进行交互。`YesSql` 随附了适用于大多数用例的配置。但是，您可以通过配置 `YesSqlOptions` 更改这些设置。`YesSqlOptions` 提供以下可配置选项。

| 设置 | 描述 |
| --- | --- |
| `CommandsPageSize` | 获取或设置命令页大小。如果在一个命令中有太多查询，则 `YesSql` 将把大型命令拆分为多个命令。 |
| `QueryGatingEnabled` | 获取或设置 `YesSql` 中的 `QueryGatingEnabled` 选项。 |
| `IdGenerator` | 您可以提供自己的实现来生成 ID。 |
| `IdentifierAccessorFactory` | 您可以提供自己的值访问器工厂。 |
| `VersionAccessorFactory` | 您可以提供自己的版本访问器工厂。 |
| `ContentSerializer` | 您可以提供自己的内容序列化器。 |

例如，您可以通过将以下代码添加到启动代码中将默认命令页大小从 `500` 更改为 `1000`。

```C#
services.Configure<YesSqlOptions>(options =>
{
    options.CommandsPageSize = 1000;
});
```

## 数据库表

以下数据库表设置仅在设置给定租户之前用作预设，可以从任何配置源提供。

| 设置 | 描述 |
| --- | --- |
| `DefaultDocumentTable` | 文档表名称，默认为“Document”。 |
| `DefaultTableNameSeparator` | 表名分隔符，一个或多个“_”，“NULL”表示没有分隔符，默认为“_”。 |
| `DefaultIdentityColumnSize` | 标识列大小，“Int32”或“Int64”，默认为“Int64”。 |

##### `appsettings.json`

```json
  "OrchardCore_Data_TableOptions": {
    "DefaultDocumentTable": "Document",
    "DefaultTableNameSeparator": "_",
    "DefaultIdentityColumnSize": "Int64"
}
```

## 运行 SQL 查询

### 创建 `DbConnection` 实例

要获取指向运行站点相同数据库的新 `DbConnection`，请使用 `Orchard.Data.Abstractions` 包中的 `OrchardCore.Data` 命名空间中的 `IDbConnectionAccessor`。

### 编写数据库提供程序不可知的查询

创建连接后，可以从 `YesSql.Abstractions` 包中的 `YesSql` 命名空间中的 `IStore` 中获取自定义 `ISqlDialect`。此服务提供了构建 SQL 查询的方法，可以使用底层连接的语法。

### 处理前缀表

Orchard Core 应用程序中的每个租户都可以有一个表前缀。在构建自定义查询时，需要考虑到它。可以通过解析 `ShellSettings` 并访问 `TablePrefix` 设置来获得它。它可从 `OrchardCore.Abstractions` 包中的 `OrchardCore.Environment.Shell` 命名空间中获得。

## 示例

以下示例使用 Dapper 执行 SQL 查询。

```csharp
using Dapper;
using OrchardCore.Data;
using OrchardCore.Environment.Shell

public class AdminController : Controller
{
    private readonly IDbConnectionAccessor _dbAccessor;
    private readonly IStore _store;
    private readonly string _tablePrefix;

    public AdminController(IDbConnectionAccessor dbAccessor, IStore store, ShellSettings settings)
    {
        _dbAccessor = dbAccessor;
        _store = store;
        _tablePrefix = settings["TablePrefix"];
    }

    public async Task<ActionResult> Index()
    {
       using (var connection = _dbAccessor.CreateConnection())
       {
           using(var transaction = connection.BeginTransaction())
           {
                var dialect = _store.Configuration.SqlDialect;
                var customTable = dialect.QuoteForTableName($"{_tablePrefix}CustomTable");

                var selectCommand = $"SELECT * FROM {customTable}";

                var model = connection.QueryAsync<CustomTable>(selectCommand);

                // 如果发生异常，则事务将被处理并回滚
                transaction.Commit();

                return View(model);
            }
        }
    }
}
