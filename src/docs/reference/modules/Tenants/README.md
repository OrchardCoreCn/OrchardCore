# 租户 (`OrchardCore.Tenants`)

`Tenants` 模块允许从管理界面管理租户。

## 静态文件提供程序功能

此功能为每个租户注册一个文件提供程序，以便为每个租户提供自定义文件，即使它们具有相同的名称。

启用租户后，在 `App_Data\Sites\[Tenant]` 文件夹中创建一个 `wwwroot` 文件夹。将任何文件放置在此文件夹中，将在与租户相同的域和前缀下提供服务。

任何放置在网站内容根文件夹中的静态文件都将首先提供服务。

## 租户的 robots.txt

静态文件提供程序允许您为每个租户设置 `robots.txt`。
要为每个租户创建 `robots.txt`，请将 `robots.txt` 放置在 `App_Data\Sites\[Tenant]\wwwroot` 文件夹下

例如

`App_Data\Sites\Tenant1\wwwroot\robots.txt`

`App_Data\Sites\Tenant2\wwwroot\robots.txt`

## 功能配置文件

此功能允许 `Default` 租户创建功能配置文件，可以使用功能规则限制可用于具有功能规则的租户的功能。

### 创建功能配置文件

1. 在 `Default` 租户上启用 _Tenant Feature Profiles_ 功能。
2. 转到 _Configuration -> Tenant Feature Profiles_ 菜单。
3. 选择 _Add Feature Profile_。
4. 添加一个 _Name_ 和一组 _Rules_。

#### 规则配置

规则是 Rule Expressions 的 JSON 数组。

规则由规则名称和表达式组成，支持简单匹配，即字符或 `*` 或 `?` 修饰符。

默认情况下，可用规则为 `Exclude` 和 `Include`

考虑以下

``` json
[
  {
    "Rule": "Exclude",
    "Expression": "OrchardCore.AdminTemplates"
  },
  {
    "Rule": "Exclude",
    "Expression": "TheAgencyTheme"
  }
]
```

在此规则中，我们排除了 `OrchardCore.AdminTemplates` 和 `TheAgencyTheme` 功能

或者我们可以使用通配符匹配

``` json
[
  {
    "Rule": "Exclude",
    "Expression": "MyModules.Custom.*"
  },
  {
    "Rule": "Include",
    "Expression": "MyModules.Custom.IncludedModule"
  }
]
```

在此示例中，我们排除以 `MyModules.Custom.` 开头的所有功能，然后特别包括 `MyModules.Custom.IncludedModule`

规则按提供的顺序处理，因此反转上述顺序将导致应用最后一个规则，即 `Exclude` 规则，否定 `Include` 规则。

#### 在配方中定义功能配置文件

通过使用 `FeatureProfiles` 配方步骤，您也可以从配方定义配置文件（不要忘记在配方中还启用 `OrchardCore.Tenants.FeatureProfiles` 功能）：

```json
{
  "name": "FeatureProfiles",
  "FeatureProfiles": {
    "my-profile": {
      "FeatureRules": [
        {
          "Rule": "Exclude",
          "Expression": "OrchardCore.Contents.FileContentDefinition"
        },
        {
          "Rule": "Exclude",
          "Expression": "OrchardCore.MiniProfiler"
        },
        {
          "Rule": "Exclude",
          "Expression": "OrchardCore.Placements.FileStorage"
        },
        {
          "Rule": "Exclude",
          "Expression": "OrchardCore.Tenants.FileProvider"
        },
        {
          "Rule": "Exclude",
          "Expression": "OrchardCore.Workflows.Session"
        }
      ]
    }
  }
}
```

### 选择功能配置文件

1. 创建功能配置文件。
2. 转到 _Configuration -> Tenants_ 菜单。
3. 编辑租户。
4. 从下拉列表中选择功能配置文件。

只有在至少配置了一个功能配置文件时，才会提供 _Feature Profile_ 下拉列表。

还可以在通过 Web API 创建租户时设置功能配置文件。

如果您使用 [自动设置](../AutoSetup/README.md)，则还可以为租户指定 `FeatureProfile` 属性；有关详细信息，请参阅自动设置文档。

## 租户删除

允许删除尚未设置或处于禁用状态的租户。默认情况下，不允许此功能。

可以从任何配置源（例如 `appsettings.json`）允许租户删除，在 `OrchardCore` 部分下。有关详细信息，请参见[配置文档](../../core/Configuration/README.md)。

```json
{
  "OrchardCore": {
    "OrchardCore_Tenants": {
      "TenantRemovalAllowed": false // 是否允许删除租户。默认值为 false。
    }
  }
}
```

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/aQAjTG2ma64" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
