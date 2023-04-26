# Recipes (`OrchardCore.Recipes`)

## Recipe file

一个recipe是一个json文件，用于执行不同的导入和配置步骤。

您可以将其添加到`Recipes`文件夹中，名称为`{RecipeName}.recipe.json`，然后它将在Configuration > Recipes管理页面中可用。

## Recipe格式

一个recipe文件应该长这样：

```json
{
  "name": "Name",
  "displayName": "Display Name of the recipe",
  "description": "Description of the recipe",
  "author": "Author",
  "website": "https://website.net",
  "version": "2.0",
  "issetuprecipe": true|false,
  "categories": [ "default" ],
  "tags": [ "tag" ],
  "variables": {
    "variable1": "[js:uuid()]",
    ...
  },
  "steps": [
      ...
  ]
}
```

!!! note
    如果`issetuprecipe`等于true，则在安装期间，该recipe将在Recipes列表中可用。

!!! note
    Recipe虽然是JSON文件，但可能包含注释：
    ```json
    // This is a comment.
    ```

## Recipe步骤

一个recipe可以执行多个步骤。

为了创建新的Recipe步骤，您需要实现`IRecipeStepHandler`接口和`ExecuteAsync`方法：
`public async Task ExecuteAsync(RecipeExecutionContext context)`

以下是可用的recipe步骤：

### Feature Step

Feature步骤允许您禁用/启用某些功能。

```json
    {
        "name": "feature",
        "disable": [],
        "enable": [
            "OrchardCore.Admin",
            "YourTheme",
            "TheAdmin"
        ]
    }
```

!!! warning
    如果要使用自己的主题（例如`YourTheme`），请不要忘记启用其功能，否则，主题布局在recipe执行后将无法工作。

### Themes Step

Themes步骤允许您设置管理和站点主题。

```json
    {
      "name": "themes",
      "admin": "TheAdmin",
      "site": "YourTheme"
    }
```

### Settings Step

Settings步骤允许您设置多个设置。

```json
    {
      "name": "settings",
      "HomeRoute": {
        "Action": "Display",
        "Controller": "Item",
        "Area": "OrchardCore.Contents",
        "ContentItemId": "[js: variables('blogContentItemId')]"
      },
      "LayerSettings": {
        "Zones": [ "Content", "Footer" ]
      }
    }
```

### ContentDefinition Step

Content步骤允许您导入某些内容类型。

```json
    {
        "name": "ContentDefinition",
        "ContentTypes": [
        {
            "Name": "YourContentType",
            ...
        }
    }
```

### Lucene Step

Lucene索引步骤允许您运行内容类型的Lucene索引。  
您还可以设置默认的Lucene设置。

```json
    {
      // Create the indices before the content items so they are indexed automatically.
      "name": "lucene-index",
      "Indices": [
        {
          "Search": {
            "AnalyzerName": "standardanalyzer",
            "IndexLatest": false,
            "IndexedContentTypes": [
              "Blog",
              "BlogPost"
            ]
          }
        }
      ]
    },
    {
      // Create the search settings.
      "name": "Settings",
      "LuceneSettings": {
        "SearchIndex": "Search",
        "DefaultSearchFields": [
          "Content.ContentItem.FullText"
        ]
      }
    }
```

### Reset Lucene Search Index Step

此Reset Lucene Index Step重置lucene索引。
从头开始重新启动索引过程，以更新当前内容项。
它不会删除索引中的现有条目。

`includeAll`属性指示是否包括所有可用的Lucene索引。当设置为`true`时，可以省略`Indices`属性。

```json
    {
      "name": "lucene-index-reset",
      "includeAll": false,
      "Indices": [
        "IndexName1", "IndexName2"
      ]
    }
```

```json
    {
      "name": "lucene-index-reset",
      "includeAll": true
    }
```

### Rebuild Lucene Search Index Step

此Rebuild Lucene Index Step重建lucene索引。
删除并重新创建完整的索引内容。

`includeAll`属性指示是否包括所有可用的Lucene索引。当设置为`true`时，可以省略`Indices`属性。

```json
    {
      "name": "lucene-index-rebuild",
      "includeAll": false,
      "Indices": [
        "IndexName1", "IndexName2"
      ]
    }
```

```json
    {
      "name": "lucene-index-rebuild",
      "includeAll": true
    }
```

### Content Step

Content步骤允许您创建内容项。

```json
     {
      "name": "content",
      "Data": [
        {
          "ContentType": "Menu",
          ...
        },
        ...
      ]
    }
```

!!! note
    还有`QueryBasedContentDeploymentStep`，它产生与Content Step完全相同的输出，但基于提供的查询。

### Media Step

Media步骤允许您将媒体文件导入到租户媒体文件夹中。

```json
    {
      "name": "media",
      "Files": [
        {
          "TargetPath": "home-bg.jpg",
          "SourcePath": "../wwwroot/img/home-bg.jpg"
        },
        {
          "TargetPath": "post-bg.jpg",
          "SourcePath": "../wwwroot/img/post-bg.jpg"
        }
      ]
    }
```

### Layers Step

Layers步骤允许您创建多个图层。

```json
    {
      "name": "layers",
      "Layers": [
        {
          "Name": "Always",
          "Rule": "true",
          "Description": "The widgets in this layer are displayed on any page of this site."
        },
        {
          "Name": "Homepage",
          "Rule": "isHomepage()",
          "Description": "The widgets in this layer are only displayed on the homepage."
        }
      ]
    }
```

### Queries Step

Queries步骤允许您创建多个Lucene或SQL查询。

```json
    {
      "name": "queries",
      "Queries": [
        {
          "Source": "Lucene",
          "Name": "RecentBlogPosts",
          "Index": "Search",
          "Template": "[file:text('Snippets/recentBlogPosts.json')]",
          "Schema": "[js:base64('ew0KICAgICJ0eXBlIjogIkNvbnRlbnRJdGVtL0Jsb2dQb3N0Ig0KfQ==')]",
          "ReturnContentItems": true
        }
      ]
    }
```

### AdminMenu Step

AdminMenu步骤允许您创建多个管理菜单。

```json
    {
      "name": "AdminMenu",
      "data": [
        {
          "Id": "[js:uuid()]",
          "Name": "Admin menu",
          "Enabled": true,
          "MenuItems": [
              ...
          ]
        }
      ]
    }
```

### Roles Step

Roles步骤允许您为特定角色设置权限。

```json
    {
      "name": "Roles",
      "Roles": [
        {
          "Name": "Anonymous",
          "Description": "Anonymous role",
          "Permissions": [
            "ViewContent",
            "QueryLuceneSearchIndex"
          ]
        }
      ]
    }
```

!!! warning
    从版本1.6开始，默认角色不再自动创建。Setup recipe必须定义要使用的默认角色。`Roles`功能将在每次启用功能时自动将所有已知权限映射到定义的角色。

### Template and AdminTemplate Step

Template和AdminTemplate步骤允许您创建Liquid模板。

```json
    {
      "name": "Templates",
      "Templates": {
        "Content__LandingPage": {
          "Description": "A template for the Landing Page content type",
          "Content": "[file:text('Snippets/landingpage.liquid')]"
        }
      }
    }
```

### Workflow Step

WorkflowType步骤允许您创建工作流。

```json
    {
      "name": "WorkflowType",
      "data": [
        {
          "WorkflowTypeId": "[js: variables('workflowTypeId')]",
          "Name": "User Registration",
          ...
        }
      ]
    }
```

### Deployment Step

Deployment步骤允许您创建具有部署步骤的部署计划。另请参见[Deployment](../Deployment/README.md)。

```json
    {
    "name": "deployment",
    "Plans": [
    {
        "Name": "Export",
        "Steps": [
            {
                "Type": "CustomFileDeploymentStep",
                "Step": {
                "FileName": "Export",
                "FileContent": "Export",
                "Id": "[js: uuid()]",
                "Name": "CustomFileDeploymentStep"
                }
            },
            {
                "Type": "AllContentDeploymentStep",
                "Step": {
                "Id": "[js: uuid()]",
                "Name": "AllContent"
                }
            }
        ]
    }
```

### CustomSettings Step

CustomSettings步骤允许您使用初始值填充自定义设置。

```json
    {
      "name": "custom-settings",
      "MyCustomSettings": {
        "ContentItemId": "400d6c7pwj8675crzacd6gyywt",
        "ContentItemVersionId": null,
        "ContentType": "MyCustomSettings",
        "DisplayText": "",
        "Latest": false,
        "Published": false,
        "ModifiedUtc": null,
        "PublishedUtc": null,
        "CreatedUtc": null,
        "Owner": "",
        "Author": "",
        "MyCustomSettingsPart": {
          "MyTextField": {
            "Text": "My custom text"
          }
        }
      }
    }
```

### Recipes Step

Recipes步骤允许您从当前recipe执行其他recipes。您可以使用此功能将您的recipes模块化。例如，您可以将内容放入多个较小的recipes中，并从setup recipe中执行它们。

```json
    {
      "name": "recipes",
      "Values": [
        {
          "executionid": "MyApp",
          "name": "MyApp.Pages"
        },
        {
          "executionid": "MyApp",
          "name": "MyApp.Blog"
        }
      ]
    }
```

使用自定义标识符作为`executionid`以区分这些recipe执行与其他执行。使用给定recipe头中的`name`字段（导出到recipes时留空）作为`name`。

### Other settings Step

这里有其他可用的步骤：

- `Command`
- `FacebookLoginSettings`
- `FacebookSettings`
- `GitHubAuthentication`
- `GoogleAnalyticsSettings`
- `GoogleAuthenticationSettings`
- `AzureADSettings`
- `MicrosoftAccountSettings`
- `OpenIdApplication`
- `OpenIdClientSettings`
- `OpenIdServerSettings`
- `Twitter`

## Recipe helpers

Recipes可以使用脚本助手，如下所示：

```json
{
    "ContentItemId": "[js: uuid()]"
}
```

| 名称 | 描述 |
| --- | --- |
| `uuid()` | 为内容项生成唯一标识符。 |
| `base64(string)` | 从Base64编码中解码指定的字符串。使用https://www.base64-image.de/将文件转换为base64。 |
| `html(string)` | 从HTML编码中解码指定的字符串。 |
| `gzip(string)` | 从gzip/base64编码中解码指定的字符串。使用http://www.txtwizard.net/compression对字符串进行gzip。 |

## Recipe Migrations

recipe migration是通过recipe文件执行更新的一种方式。最常见的用途是更新元数据，如内容类型或工作流，但可以更新任何可以通过recipe进行更新的内容。

让我们考虑一个简单的场景：添加新资产。现在可以通过管理UI界面来完成此操作，但是这里的目的是演示涉及recipe迁移的所有移动部件。

在您的模块或主题项目中，创建一个类，该类继承自`OrchardCore.Data.Migration.DataMigration`（在`OrchardCore.Data.Abstractions`包中找到）。向此类添加对`IRecipeMigrator`服务的依赖项。提供`CreateAsync()`和/或`UpdateAsync()`方法，这些方法返回`Task<int>`以提供迁移步骤。该类可以放置在项目的任何位置，但是recipe JSON文件必须放置在名为`Migrations`的文件夹中。

以下是如何编写初始和后续迁移的示例。使用`CreateAsync()`方法提供第一个运行的迁移，并确保此方法始终返回1。使用`UpdateFrom<version>Async()`提供后续迁移；在此示例中，我们有一个从版本1更新到2的迁移。方法名称区分大小写，必须遵循命名约定才能发现和执行迁移。

```csharp
public class Migrations : DataMigration
{
    private readonly IRecipeMigrator _recipeMigrator;

    public Migrations(IRecipeMigrator recipeMigrator)
    {
        _recipeMigrator = recipeMigrator;
    }

    public async Task<int> CreateAsync()
    {
        await _recipeMigrator.ExecuteAsync("migration.recipe.json", this);

        return 1;
    }

    public async Task<int> UpdateFrom1Async()
    {
        await _recipeMigrator.ExecuteAsync("migrationV2.recipe.json", this);

        return 2;
    }
}
```

以下是上面代码中引用的迁移recipe：

**Migrations/migration.recipe.json**

```json
{
    "steps": [
        {
          "name": "media",
          "Files": [
            {
                "TargetPath": "about/1.jpg",
                "SourcePath": "../wwwroot/img/about/1.jpg"
            },
            {
                "TargetPath": "about/2.jpg",
                "SourcePath": "../wwwroot/img/about/2.jpg"
            }
          ]
        }
    ]
}
```

**Migrations/migrationV2.recipe.json**

```json
{
    "steps": [
        {
          "name": "media",
          "Files": [
            {
                "TargetPath": "about/1.jpg",
                "SourcePath": "../wwwroot/img/about/1.jpg"
            },
            {
                "TargetPath": "about/2.jpg",
                "SourcePath": "../wwwroot/img/about/2.jpg"
            },
            {
                "TargetPath": "about/3.jpg",
                "SourcePath": "../wwwroot/img/about/3.jpg"
            }
          ]
        }
    ]
}
```

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/uJobH9izfLI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/qPCBgHQYz1g" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/A13Li0CblK8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
