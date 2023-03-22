# 安装 (`OrchardCore.Setup`)

当您开始使用空站点时，启动屏幕允许您设置不同的参数，如标题、选择的数据库或将用于生成站点的配方。这是通过安装模块完成的。

## 配方参数

在安装过程中，所有配方都可以使用这些参数访问设置屏幕值：

| 参数 | 描述 |
| --- | --- |
| `SiteName` | 站点名称。 |
| `AdminUserId` | 超级用户的用户ID。 |
| `AdminUsername` | 超级用户的用户名。 |
| `AdminEmail` | 超级用户的电子邮件。 |
| `AdminPassword` | 超级用户的密码。 |
| `DatabaseProvider` | 数据库提供程序。 |
| `DatabaseConnectionString` | 连接字符串。 |
| `DatabaseTablePrefix` | 数据库表前缀。 |

这些参数可以在配方中使用脚本化值，例如`[js: parameters('AdminUserId')]`。

### 配方配置键

还可以使用自定义配置键在配方中使用脚本化键值，例如`[js: configuration('CustomConfigurationKey')]`。

该键将从当前的[IShellConfiguration](../../core/Configuration/README.md)中检索。

例如，为租户提供密钥

```json
    {
        "ConnectionString": "...",
        "DatabaseProvider": "Sqlite",
        "TablePrefix": "Test",
        "CustomConfigurationKey": "Custom Configuration Value"
    }
```

还可以使用其他配置键，例如来自主机的`appsettings.json`

`[js: configuration('OrchardCore_Admin:AdminUrlPrefix', 'Admin')]`

在此示例中，我们还提供了一个默认值，如果未找到该键，则将使用该默认值。

```json
    {
        "OrchardCore_Admin" : {
            "AdminUrlPrefix" : "MyAdmin"
        }
    }
```

## 安装配置

默认情况下使用以下配置值，可以进行自定义：

```json
    "OrchardCore_Setup": {
      "DefaultCulture": "", // 当使用""时，将使用系统OS文化
      "SupportedCultures": [
        "ar", 
        "cs", 
        "da", 
        "de", 
        "el", 
        "en", 
        "es", 
        "fa", 
        "fi", 
        "fr", 
        "he", 
        "hr", 
        "hu", 
        "id", 
        "it", 
        "ja", 
        "ko", 
        "lt", 
        "mk", 
        "nl", 
        "pl", 
        "pt", 
        "ru", 
        "sk", 
        "sl", 
        "sr-cyrl-rs", 
        "sr-latn-rs", 
        "sv", 
        "tr", 
        "uk", 
        "vi", 
        "zh-CN", 
        "zh-TW"
      ] // ""值（InvariantCulture）不支持这些
    }
```

| 键 | 描述 |
| --- | --- |
| `DefaultCulture` | 将用于设置屏幕的默认文化。 |
| `SupportedCultures` | 设置屏幕支持的文化列表。 |

## CDN默认禁用

默认情况下禁用了在“配置->设置->常规”部分配置的`UseCdn`选项。
这是为了在没有互联网连接或在像中国这样的国家中无法访问CDN时允许访问资源。

!!! 注意
    建议在安装后启用CDN设置。

## 其他信息
有关设置的其他信息，请参阅单独的部分：

- [自动设置-如何在部署空站点时预定义设置参数](../AutoSetup/README.md)

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/usjGbjwbmNo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
