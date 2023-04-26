# 资源 (`OrchardCore.Resources`)


## 目的

`Resources` 模块提供了常用的资源，如 JavaScript 库和 CSS 文件。它还启用了资源管理器，因此任何模块都可以描述任何页面或组件所需的资源。当完整页面被呈现时，所有必需的资源都会被计算，并相应地呈现自定义 `<script>` 和 `<link>` 标记。您还可以注册自定义 `<meta>` 标记。

## 资源位置

`Resources` 将通过模块或主题的 `wwwroot` 文件夹从 `StaticFileMiddleware` 提供。当定义资源时，命名约定是 `~/ThemeName/scripts/path-to-file.js` 或 `~/Module.Name/styles/path-to-file.css`

波浪号（~）是用于指示相对路径的约定，例如租户基本路径。
所有脚本或样式表资源都应以 `~` 字符为前缀。

## 资源设置

资源设置通过站点管理配置。

### `AppendVersion`

启用 `AppendVersion` 或资源缓存破坏将自动在所有本地脚本和样式表上附加版本哈希。默认情况下启用此功能。

### `UseCdn`

启用 UseCdn 将配置 `IResourceManager` 以从配置的 CDN 提供任何脚本或样式，例如 `jQuery`。

### `ResourceDebugMode`

启用时，将从本地服务器以非最小化格式提供具有 CDN 配置或 debug-src 的脚本或样式。  
这也将禁用 `CdnBaseUrl` 前缀。

### `CdnBaseUrl`

当提供时，这将在通过 `IResourceManager` 或标记助手提供的本地资源之前使用提供的绝对 URL 进行前缀。在 `ResourceDebugMode` 中将禁用此功能。

## 命名资源

命名资源是在模块或主题中描述的众所周知的脚本和样式表。  
它们有一个名称、一个类型（脚本、样式表）和可选的版本。  
`OrchardCore.Resources` 模块提供了一些常用的资源：<|im_end|>

| Name                  | Type   | Versions      | Dependencies   |
| --------------------- | ------ | ------------- | -------------- |
| jQuery                | Script | 3.6.1         | -              |
| jQuery.slim           | Script | 3.6.1         | -              |
| jQuery-ui             | Script | 1.12.1        | jQuery         |
| jQuery-ui-i18n        | Script | 1.7.2         | jQuery-ui      |
| jquery.easing         | Script | 1.4.1         | -              |
| jquery-resizable-dom  | Script | 0.35.0        | -              |
| js-cookie             | Script | 3.0.1         | jQuery         |
| popper                | Script | 1.16.1        | -              |
| popperjs              | Script | 2.11.6        | -              |
| bootstrap             | Script | 4.6.1         | popper         |
| bootstrap             | Script | 5.1.3         | popperjs       |
| bootstrap             | Style  | 4.6.1, 5.1.3  | -              |
| bootstrap-select      | Script | 1.14.0-beta2  | -              |
| bootstrap-select      | Style  | 1.14.0-beta2  | -              |
| codemirror            | Script | 5.65.7        | -              |
| codemirror            | Style  | 5.65.7        | -              |
| font-awesome          | Style  | 6.2.1         | -              |
| font-awesome          | Script | 6.2.1         | -              |
| font-awesome-v4-shims | Script | 6.2.1         | -              |
| Sortable              | Script | 1.10.2        | -              |
| trumbowyg             | Style  | 2.26.0        | -              |
| trumbowyg             | Script | 2.26.0        | -              |
| vue-multiselect       | Script | 2.1.6         | -              |
| vuedraggable          | Script | 2.24.3        | Sortable       |
| monaco-loader         | Script | 0.35.0        | -              |
| monaco                | Script | 0.35.0        | monaco-loader  |
| nouislider            | Script | 15.6.1        | -              |
| nouislider            | Style  | 15.6.1        | -              |

### 注册资源清单

通过配置 `ResourceManagementOptions` 选项来注册命名资源。

此示例来自 `TheBlogTheme`。

```csharp
public class ResourceManagementOptionsConfiguration : IConfigureOptions<ResourceManagementOptions>
{
    private static ResourceManifest _manifest;

    static ResourceManagementOptionsConfiguration()
    {
        _manifest = new ResourceManifest();

        _manifest
            .DefineScript("TheBlogTheme-vendor-jQuery")
            .SetUrl("~/TheBlogTheme/vendor/jquery/jquery.min.js", "~/TheBlogTheme/vendor/jquery/jquery.js")
            .SetCdn("https://code.jquery.com/jquery-3.4.1.min.js", "https://code.jquery.com/jquery-3.4.1.js")
            .SetCdnIntegrity("sha384-vk5WoKIaW/vJyUAd9n/wmopsmNhiy+L2Z+SBxGYnUkunIxVxAv/UtMOhba/xskxh", "sha384-mlceH9HlqLp7GMKHrj5Ara1+LvdTZVMx4S1U43/NxCvAkzIo8WJ0FE7duLel3wVo")
            .SetVersion("3.4.1");
    }

    public void Configure(ResourceManagementOptions options)
    {
        options.ResourceManifests.Add(_manifest);
    }
}

```

在此示例中，我们定义了一个名为 `TheBlogTheme-vendor-jQuery` 的脚本。
我们使用一个对 `TheBlogTheme` 唯一的名称，以防止在多个主题处于活动状态时发生冲突。

我们为缩小版本和未缩小版本设置了一个 URL，这将在 `ResourceDebugMode` 中使用。
出于同样的原因，我们定义了两个 CDN URL，如果站点管理员中的 `UseCdn` 设置，则将优先使用这些 URL 而不是本地 URL。
我们设置了 Cdn 完整性哈希和版本为 `3.4.1`

然后，此脚本将可供标记助手或 API 按名称注册。

此外，我们可以使用 `SetDependencies` 方法来确保在其依赖项之后加载脚本或样式。

```csharp
public class ResourceManagementOptionsConfiguration : IConfigureOptions<ResourceManagementOptions>
{
    private static ResourceManifest _manifest;

    static ResourceManagementOptionsConfiguration()
    {
        _manifest = new ResourceManifest();

        _manifest
            .DefineStyle("ModuleName-Bootstrap-Select")
            .SetUrl("~/ModuleName/bootstrap-select.min.css", "~/ModuleName/bootstrap-select.css")
            .SetDependencies("bootstrap:4");
    }

    public void Configure(ResourceManagementOptions options)
    {
        options.ResourceManifests.Add(_manifest);
    }
}

```

在此示例中，我们定义了一个依赖于 Bootstrap 版本 4 的样式。在这种情况下，将添加可用的最新的 Bootstrap 版本 4 的次要版本。或者，您可以设置您选择的特定版本或最新版本。[有关版本使用的更多详细信息，请参见内联定义](#inline-definition)。

!!! 注意 "注册"
    确保在 `Startup` 或您的主题或模块中注册此 `IConfigureOptions<ResourceManagementOptions>`。
    `serviceCollection.AddTransient<IConfigureOptions<ResourceManagementOptions>, ResourceManagementOptionsConfiguration>();`
  
## 用法

有两种调用资源的方法：使用 `IResourceManager` API 或标记助手。  
如果需要从代码中注入资源，则需要使用 API，但建议在视图内部使用标记助手。

### 使用 API

从您的模块中，添加对 `OrchardCore.Resources.Abstractions` 项目的引用。  
从要在其中使用 API 的类中，注入 `OrchardCore.ResourceManagement.IResourceManager` 接口。

#### 注册命名资源

```csharp
var settings = _resourceManager.RegisterResource("script", "bootstrap")
```

此调用的结果是一个 `RequireSettings` 类型的对象，用于将更多参数传递给所需的资源。

##### 将资源放置在 HTML 文档的开头

```csharp
settings.AtHead();
```

##### 将资源放置在 HTML 文档的结尾

```csharp
settings.AtFoot();
```

##### 设置要使用的版本

```csharp
settings.UseVersion("3.4");
```

这将使用 `3.4` 和 `3.5` 之间最新的可用版本。如果版本不可用，则会抛出异常。

##### 添加版本

```csharp
settings.UseAppendVersion(true);
```

这将在运行时附加一个版本字符串，该字符串是文件的SHA256哈希值，计算被缓存，并作为查询字符串的一部分附加到URL中，例如`my-script.js?v=eER9OO6zWGKaIq1RlNjImsrWN9y2oTgQKg2TrJnDUWk`

#### 注册自定义脚本

在HTML文档的开头：

```csharp
resourceManager.RegisterHeadScript(new HtmlString("<script>alert('Hello')</script>"));
```

在HTML文档的结尾：

```csharp
resourceManager.RegisterFootScript(new HtmlString("<script>alert('Hello')</script>"));
```

### 添加自定义元标记

```csharp
resourceManager.RegisterMeta(new MetaEntry { Content = "Orchard", Name = "generator" });
```

您还可以像这样向现有标记添加更多内容：

```csharp
resourceManager.AppendMeta(new MetaEntry { Name = "keywords", Content = "orchard" }, ",");
```

### 使用标记助手

从您的模块，在`_ViewImports.cshtml`或您的视图中，添加`@addTagHelper *，OrchardCore.ResourceManagement`，并直接引用`OrchardCore.ResourceManagement` nuget包。


#### 注册命名的脚本或样式表

此示例注册名为`bootstrap`的脚本及其所有依赖项（jquery）。

=== "Liquid"

    ``` liquid
    {% script name:"bootstrap" %}
    ```

=== "Razor"

    ``` html
    <script asp-name="bootstrap"></script>
    ```

对于样式表：

=== "Liquid"

    ``` liquid
    {% style name:"bootstrap" %}
    ```

=== "Razor"

    ``` html
    <style asp-name="bootstrap"></style>
    ```

##### 强制使用CDN

您可以强制使用CDN中的资源。默认情况下，行为由配置定义。

=== "Liquid"

    ``` liquid
    {% script name:"bootstrap", use_cdn:"true" %}
    ```

=== "Razor"

    ``` html
    <script asp-name="bootstrap" use-cdn="true"></script>
    ```

##### 使用特定版本

此示例将使用具有主版本为`3`的最新可用版本，例如`3.4.0`。如果未指定版本，则始终使用最新版本。

=== "Liquid"

    ``` liquid
    {% script name:"bootstrap", version:"4" %}
    ```

=== "Razor"

    ``` html
    <script asp-name="bootstrap" version="3"></script>
    ```

##### 添加版本哈希

您可以附加一个版本哈希，该哈希将被计算并缓存计算，并以格式`?v=eER9OO6zWGKaIq1RlNjImsrWN9y2oTgQKg2TrJnDUWk`附加

=== "Liquid"

    ``` liquid
    {% script name:"bootstrap", append_version:"true" %}
    ```

=== "Razor"

    ``` html
    <script asp-name="bootstrap" asp-append-version="true"></script>
    ```

##### 指定位置

使用`at`指定脚本应加载的位置，例如`Foot`以在`FootScript`助手所在的位置呈现或`Head`以与`HeadScript`一起呈现[请参见Foot Resources]（＃foot-resources）。如果未指定位置或指定为`Inline`，则脚本将插入到其放置的任何位置（内联）。

=== "Liquid"

    ``` liquid
    {% script name:"bootstrap", at:"Foot" %}
    ```

=== "Razor"

    ``` html
    <script asp-name="bootstrap" at="Foot"></script>
    ```

链接和样式标记助手始终注入到HTML文档的标题部分中，除非将`at`位置设置为`Inline`。

#### 内联定义

您可以直接从视图中声明新资源，即使多次调用视图，它也只会被注入一次。

=== "Liquid"

    ``` liquid
    {% script name:"foo", src:"~/TheTheme/js/foo.min.js", debug_src:"~/TheTheme/js/foo.js", depends_on:"jQuery", version:"1.0" %}
    {% script name:"bar", src:"~/TheTheme/js/bar.min.js", debug_src:"~/TheTheme/js/bar.js", depends_on:"foo:1.0", version:"1.0" %}
    ```

=== "Razor"

    ``` html
    <script asp-name="foo" asp-src="~/TheTheme/js/foo.min.js?v=1.0" debug-src="~/TheTheme/js/foo.js?v=1.0" depends-on="jQuery" version="1.0"></script>
    <script asp-name="bar" asp-src="~/TheTheme/js/bar.min.js?v=1.0" debug-src="~/TheTheme/js/bar.js?v=1.0" depends-on="foo:1.0" version="1.0"></script>
    ```

我们定义了一个名为 `foo` 的脚本，它依赖于 `jQuery` 的版本 `1.0`。

然后，我们定义了一个名为 `bar` 的脚本，它还依赖于 `foo` 脚本的版本 `1.0`。

如果未设置版本，则使用数字最高的版本。

在呈现脚本时，资源管理器将根据依赖项对输出进行排序，而不考虑它们的书写顺序：

1. `jQuery`
2. `foo`
3. `bar`


您还可以为样式表执行相同的操作：

=== "Liquid"

    ``` liquid
    {% style name:"bar", src:"~/TheTheme/css/bar.min.css", debug_src:"~/TheTheme/css/bar.css", depends_on:"foo" %}
    {% style name:"foo", src:"~/TheTheme/css/foo.min.css", debug_src:"~/TheTheme/css/foo.css", depends_on:"bootstrap" %}
    ```

=== "Razor"

    ``` html
    <style asp-name="bar" asp-src="~/TheTheme/css/bar.min.css" debug-src="~/TheTheme/css/bar.css" depends-on="foo"></style>
    <style asp-name="foo" asp-src="~/TheTheme/css/foo.min.css" debug-src="~/TheTheme/css/foo.css" depends-on="bootstrap"></style>
    ```

在此示例中，定义了一个名为 `bar` 的样式，其依赖于名为 `foo` 的样式

然后我们定义名为 `foo` 的样式

在呈现脚本时，资源管理器将根据依赖项对输出进行排序，而不考虑它们编写的顺序：

1. `bootstrap`
2. `foo`
3. `bar`

!!! 注意
    您不必为脚本或样式定义名称，除非您想将其作为依赖项引用或将其声明为 `Inline`。因此，上面的所有内联示例都包括名称。

#### 自定义脚本

以下示例演示了如何在页脚部分注入自定义脚本。

=== "Liquid"

    ``` liquid
    {% scriptblock at: "Foot" %}
        document.write('<!-- some script -->');
    {% endscriptblock %}
    ```

=== "Razor"

    ``` html
    <script at="Foot">
        document.write('<!-- some script -->');
    </script>
    ```

您还可以注入命名的自定义脚本。

=== "Liquid"

    ``` liquid
    {% scriptblock name: "Carousel", at: "Foot", depends_on:"jQuery" %}
        document.write('<!-- some script -->');
    {% endscriptblock %}
    ```

=== "Razor"

    ``` html
    <script asp-name="Carousel" at="Foot" depends-on="jQuery">
        document.write('<!-- some script -->');
    </script>
    ```

命名脚本将仅注入一次，并且可以选择指定依赖项。

#### 自定义样式

以下示例演示了如何在页眉部分注入自定义样式表。
样式块将在所有样式表资源之后注入。

=== "Liquid"

    ``` liquid
    {% styleblock at: "Head" %}
        .my-class {
            /* some style */
        }
    {% endstyleblock %}
    ```

=== "Razor"

    ``` html
    <style at="Head">
        .my-class {
            /* some style */
        }
    </style>
    ```

您还可以注入命名的样式块。
样式块将根据其名称仅注入一次，并且可以选择指定依赖项。

=== "Liquid"

    ``` liquid
    {% styleblock name: "my-style", depends_on:"the-theme" %}
        .my-class {
            /* some style */
        }
    {% endstyleblock %}
    ```

=== "Razor"

    ``` html
    <style asp-name="my-style" depends-on="the-theme">
        .my-class {
            /* some style */
        }
    </style>
    ```
#### 链接标签

链接标签用于定义当前文档与外部资源（如网站图标或样式表）之间的关系。但是，对于样式表，请使用[style helper](#register-a-named-script)。

=== "Liquid"

    ``` liquid
    {% link rel:"icon", type:"image/png", sizes:"16x16", src:"~/MyTheme/favicon/favicon-16x16.png" %}
    ```

=== "Razor"

    ``` html
    <link asp-src="~/MyTheme/favicon/favicon-16x16.png" rel="icon" type="image/png" sizes="16x16" />
    ```

输出

```text
<link href="/MyTheme/favicon/favicon-16x16.png" rel="icon" sizes="16x16" type="image/png" />
```

##### 使用媒体库中的文件
如果您希望在使用链接标签助手时使用媒体库中包含的文件，则可以直接在razor中使用`AssetUrl`助手，但是在Liquid中，您需要首先将过滤器结果分配给变量，如下所示以生成正确的URL：

=== "Liquid"

    ``` liquid
    {% assign image_url = 'favicon/favicon-16x16.png' | asset_url %}
    {% link rel:"icon", type:"image/png", sizes:"16x16", src:image_url %}
    ```

=== "Razor"

    ``` html
    <link asp-src=@Orchard.AssetUrl("favicon/favicon-16x16.png") rel="icon" type="image/png" sizes="16x16" />
    ```

#### 元标签

=== "Liquid"

    ``` liquid
    {% meta name:"description", content:"This is a website" %}
    ```

=== "Razor"

    ``` html
    <meta asp-name="description" content="This is a website" />
    ```

这些属性可用：

| 名称                         | 描述                                                           |
| ---------------------------- | --------------------------------------------------------------------- |
| `name` (`asp-name` in Razor) | 标记的`name`属性                                       |
| `content`                    | 标记的`content`属性                                    |
| `httpequiv`                  | 标记的`http-equiv`属性                                 |
| `charset`                    | 标记的`charset`属性                                    |
| `separator`                  | 在为相同名称定义多个标记时使用的分隔符 |

### 渲染

您的`Layout.cshtml`或`Layout.liquid`必须调用资源管理器以呈现已注册的资源。

#### Head Resources

这些通常在`<head>`部分的底部呈现。

=== "Liquid"

    ``` liquid
    <head>
        ...
        {% resources type: "Meta" %}
        {% resources type: "HeadLink" %}
        {% resources type: "HeadScript" %}
        {% resources type: "Stylesheet" %}
    </head>
    ```

=== "Razor"

    ``` html
    <head>
        ...
        <resources type="Meta" />
        <resources type="HeadLink" />
        <resources type="HeadScript" />
        <resources type="Stylesheet" />
    </head>
    ```

#### Foot Resources

这些应在`<body>`部分的底部呈现。

=== "Liquid"

    ``` liquid
    <body>
        ...
        {% resources type: "FootScript" %}
    </body>    
    ```

=== "Razor"

    ``` html
    <body>
        ...
        <resources type="FootScript" />
    </body>
    ```

!!! note
    在Razor中使用标记助手时，必须在每个使用标记助手的主题或模块中直接引用`OrchardCore.ResourceManagement` nuget包。在使用Liquid时不需要。

### 日志记录

如果按名称注册资源但未找到该资源，则会将其记录为错误并保存在`App_Data/Logs`文件夹中。

## 默认情况下禁用CDN

`UseCdn`选项在“配置->设置->常规”部分中配置，默认情况下禁用。
这是为了在没有互联网连接或在像中国这样的国家中，CDN不总是可访问时访问资源。

!!! note
    建议在设置后启用CDN设置。


























 
