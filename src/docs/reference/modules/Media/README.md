# 媒体 (`OrchardCore.Media`)

媒体模块提供了一个UI来上传和组织二进制文件，这些文件可以在创建内容时使用。

媒体处理Liquid过滤器还可以创建自定义大小的图像。

## HTML 过滤器

以下过滤器允许进行媒体操作：

### `asset_url`

根据媒体库中的位置，返回媒体文件的URL。

#### 输入

`{{ 'animals/kittens.jpg' | asset_url }}`

或者当使用您添加的内容时

`{{ Model.ContentItem.Content.YourContentType.YourMediaField.Paths.first | asset_url }}`

#### 输出

`/media/animals/kittens.jpg`

### `img_tag`

呈现一个`<img src />` HTML 标签。

#### img_tag 输入

`{{ 'animals/kittens.jpg' | asset_url | img_tag }}`

#### img_tag 输出

`<img src="~/media/animals/kittens.jpg" />`

#### 选项

您可以使用img_tag添加任意数量的html属性。
`{{ 'animals/kittens.jpg' | asset_url | img_tag: alt: 'kittens', class: 'kittens black', data_order: some_var }}`

## 图像调整大小过滤器

### `resize_url`

将输入URL转换为使用指定大小参数创建调整大小的图像。

#### resize_url 输入

`{{ 'animals/kittens.jpg' | asset_url | resize_url: width:100, height:240 | img_tag }}`

#### resize_url 输出

`<img src="~/media/animals/kittens.jpg?width=100&height=240" />`

#### 参数

请参阅[查询字符串标记](#query-string-tokens)以了解宽度或高度命令的有效值以及查询字符串何时必须使用令牌。

!!! 注意
    您不能混合使用命名和索引参数。如果任何参数是命名的，则所有参数都必须命名。

#### `width` (或第一个参数)

新图像的宽度。允许的值之一。

#### `height` (或第二个参数)

新图像的高度。允许的值之一。

#### `mode` (或第三个参数)

调整大小模式。

##### `pad`

将调整大小的图像填充到适合其容器的边界。  
如果只传递一个维度，则将保持原始宽高比。

##### `boxpad`

将图像填充到容器的边界，而不调整原始源。  
当缩小时，执行与`pad`相同的功能。

##### `max` (默认)

将调整大小的图像限制为适合其容器的边界，保持原始宽高比。

##### `min`

调整图像大小，直到最短边达到给定的尺寸。在此模式下禁用放大，并且如果尝试，则将返回原始图像。

##### `stretch`

将调整大小的图像拉伸到适合其容器的边界。

##### `crop`

使用与`max`相同的功能调整图像大小，然后删除任何落在其容器边界之外的图像区域。

#### mode 输入

`{{ 'animals/kittens.jpg' | asset_url | resize_url: width:100, height:240, mode:'crop' }}`

#### mode 输出

`<img src="~/media/animals/kittens.jpg?width=100&height=240&rmode=crop" />`

#### `quality` (或第四个参数)

压缩图像时使用的质量。

!!! 注意
    质量参数仅支持`JPG`图像，但可以与`format`参数结合使用以转换为`JPG`

#### `format` (或第五个参数)

处理图像输出时要使用的图像格式。

支持的格式包括`bmp`、`gif`、`jpg`、`png`、`tga`。

可以与`quality`参数结合使用，将图像转换为`JPG`并降低质量。

#### quality/format 输入

`{{ 'animals/kittens.jpg' | asset_url | resize_url: width:100, height:240, mode:'crop', quality: 50, format:'jpg' }}`

#### quality/format 输出

`<img src="~/media/animals/kittens.jpg?width=100&height=240&rmode=crop&quality=50&format=jpg" />`

### `anchor` (或第六个参数)

新图像的锚点。

#### anchor 输入

```
{% assign anchor = Model.ContentItem.Content.Blog.Image.Anchors.first %}
{{ 'animals/kittens.jpg' | asset_url | resize_url: width:100, height:240, mode:'crop', anchor:anchor }}
```

#### anchor 输出

`<img src="~/media/animals/kittens.jpg?width=100&height=240&rmode=crop&rxy=0.5,0.5" />`

### `bgcolor` (或第七个参数)

当`mode`为`pad`或`boxpad`时，新图像的背景颜色。有效值的示例：`white`、`ffff00`、`ffff0080`、`128,64,32`和`128,64,32,16`。

#### bgcolor 输入

```
{{ 'animals/kittens.jpg' | asset_url | resize_url: width:100, height:240, mode:'pad', bgcolor:'white' }}
```

#### bgcolor 输出

`<img src="~/media/animals/kittens.jpg?width=100&height=240&rmode=pad&bgcolor=white" />`

### `profile` (命名参数)

可以指定[媒体配置文件](#media-profiles)作为命名参数，以提供预设的格式化命令。

#### `profile` 输入

`{{ 'animals/kittens.jpg' | asset_url | resize_url: profile : 'medium' }}`

#### `profile` 输出

`<img src="~/media/animals/kittens.jpg?width=240&height=240" />`

### `append_version`

为资产附加版本哈希。可以将其与其他媒体过滤器一起使用。

#### version 输入

`{{ 'animals/kittens.jpg' | asset_url | append_version | img_tag }}`

#### version 输出

`<img src="~/media/animals/kittens.jpg?v=Ailxbj_jQtYc9LRXKa21DygRzmQqc3OfN1XxSaQ3UWE" />`

## Razor 帮助程序

要获取资产的正确URL，请在视图的基本`Orchard`属性上使用`AssetUrl`帮助程序扩展方法，例如：

`@Orchard.AssetUrl(Model.Paths[0])`

要获取调整大小的资产的正确URL，请使用带有可选的宽度、高度和resizeMode参数的`AssetUrl`，例如：

`@Orchard.AssetUrl(Model.Paths[0], width: 100 , height: 240, resizeMode: ResizeMode.Crop)`

要获取调整大小的资产的正确URL，请使用带有可选的宽度、高度、resizeMode、quality和format参数的`AssetUrl`，例如：

`@Orchard.AssetUrl(Model.Paths[0], width: 100 , height: 240, resizeMode: ResizeMode.Crop, quality: 50, format: Format.Jpg)`

要获取调整大小的资产的正确URL，请使用带有可选的宽度、高度、resizeMode和bgcolor的`AssetUrl`，例如：

`@Orchard.AssetUrl(Model.Paths[0], width: 100 , height: 240, resizeMode: ResizeMode.Pad, bgcolor: "white")`

要附加资产的版本哈希，请使用带有附加版本参数的`AssetUrl`，例如：

`@Orchard.AssetUrl(Model.Paths[0], appendVersion: true)`

或者也可以使用调整大小选项，注意版本哈希基于源图像

`@Orchard.AssetUrl(Model.Paths[0], width: 100 , height: 240, resizeMode: ResizeMode.Crop, appendVersion: true)`

要使用[媒体配置文件](#media-profiles)，请在视图的基本`Orchard`属性上使用`AssetProfileUrlAsync`帮助程序扩展方法，例如：

`@await Orchard.AssetProfileUrlAsync(Model.Paths[0], "medium")`

要使用[图像锚](#image-anchors)，请在媒体字段上使用`GetAnchors`帮助程序扩展方法，例如：

`@await Orchard.AssetUrl(Model.Paths[0], , width: 100 , height: 240, resizeMode: ResizeMode.Crop, @Model.Field.GetAnchors()[0])`

### Razor 调整大小标记助手

要使用图像标记助手，请在`_ViewImports.cshtml`中添加`@addTagHelper *, OrchardCore.Media`，并直接引用`OrchardCore.Media` nuget包。

`asset-src`用于获取资产的正确URL并设置`src`属性。可以使用`img-width`、`img-height`、`img-resize-mode`、`img-quality`和`img-format`分别设置宽度、高度、调整大小模式、质量和格式。例如：

`<img asset-src="Model.Paths[0]" alt="..." img-width="100" img-height="240" img-resize-mode="Crop" img-quality="50" img-format="Jpg" />`

或者可以独立解析资产URL并使用`src`属性：

`<img src="@Orchard.AssetUrl(Model.Paths[0])" alt="..." img-width="100" img-height="240" img-resize-mode="Crop" img-quality="50" img-format="Jpg" />`

当调整大小模式为`pad`或`boxpad`时，可以使用`img-bgcolor`设置背景颜色。例如：

`<img asset-src="Model.Paths[0]" alt="..." img-width="100" img-height="240" img-resize-mode="Pad" img-bgcolor="white" />`

要使用[媒体配置文件](#media-profiles)，请设置`asset-src`属性和`img-profile`属性。

`<img asset-src="Model.Paths[0]" alt="..." img-profile="medium" />`

您可以选择包含更多格式信息，或覆盖配置文件属性。

`<img asset-src="Model.Paths[0]" alt="..." img-profile="medium" img-quality="50" img-format="Jpg" />`

要使用[媒体文本](#media-text)，请设置`alt`属性。

`<img asset-src="Model.Paths[0]" alt="@Model.MediaTexts[0]" />`

要使用[图像锚](#image-anchors)，请设置`asset-src`属性和`img-anchor`属性。

`<img asset-src="Model.Paths[0]" alt="..." img-width="100" img-height="240" img-profile="medium" img-anchor="@Model.GetAnchors()[0]" />`

### Razor 追加版本

`asp-append-version`支持OrchardCore标记助手和MVC标记助手。

`<img asset-src="Model.Paths[0]" alt="..." asp-append-version="true" />`

或者也可以使用调整大小选项，注意版本哈希基于源图像

`<img src="@Orchard.AssetUrl(Model.Paths[0], width: 100 , height: 240, resizeMode: ResizeMode.Crop, appendVersion: true)" alt="..." />`

或者当使用MVC标记助手并且图像是从静态资产（即wwwroot）解析时：

`<img src="/favicon.ico" asp-append-version="true"/>`

!!! 注意
    如果视图使用Orchard Core的Razor基类，则可以在`Orchard`属性上访问Razor Helper，否则可以在所有其他情况下注入`OrchardCore.IOrchardHelper`。

## 部署步骤编辑器

在使用部署步骤编辑器时，请记住以下几点：

- 选择“包括所有媒体。”将确保在执行此部署计划时将所有媒体添加到包中，而不管您现在看到什么。
- 选择文件将确保在执行此部署计划时仅将该文件添加到包中，而不管您现在看到什么。
- 选择目录将确保在执行此部署计划时将该目录中的所有文件添加到包中，而不管您现在看到什么。
- 选择目录中的所有文件将确保在执行此部署计划时仅将这些文件添加到包中，即使在那时，该目录具有比您现在看到的更多的文件。

## 配置

默认情况下使用以下配置值，并且可以自定义：

```json
    "OrchardCore_Media": {

      // 自定义宽度和高度的接受大小。
      // 当“UseTokenizedQueryString”为True（默认值）时，所有大小都有效。
      "SupportedSizes": [ 16, 32, 50, 100, 160, 240, 480, 600, 1024, 2048 ],

      // 在浏览器缓存中存储图像的天数。
      // 注意：要控制模块静态资产的缓存标头，请参阅Orchard Core模块部分。
      "MaxBrowserCacheDays": 30,

      // 在请求时重新构建缓存的调整大小的媒体项的天数。
      "MaxCacheDays": 365,

      // 上传文件的最大大小（以字节为单位）。
      // 注意：您可能仍需要在IIS中配置限制（https://docs.microsoft.com/en-us/iis/configuration/system.webserver/security/requestfiltering/requestlimits/）
      "MaxFileSize": 30000000,

      // 在提供图像时要添加到请求路径的CDN基本URL。
      "CdnBaseUrl": "https://your-cdn.com",

      // 用于提供媒体资产的路径。
      "AssetsRequestPath": "/media",

      // 用于存储媒体资产的路径。路径可以相对于租户的App_Data文件夹或绝对路径。
      "AssetsPath": "Media",

      // 是否在查询字符串中使用令牌以防止磁盘填充。
      "UseTokenizedQueryString": true,

      // 允许的文件扩展名。
      "AllowedFileExtensions": [
            // Images
            ".jpg",
            ".jpeg",
            ".png",
            ".gif",
            ".ico",
            ".svg",

            // Documents
            ".pdf", // Portable Document Format; Adobe Acrobat
            ".doc", // Microsoft Word Document
            ".docx",
            ".ppt", // Microsoft PowerPoint Presentation
            ".pptx",
            ".pps",
            ".ppsx",
            ".odt", // OpenDocument Text Document
            ".xls", // Microsoft Excel Document
            ".xlsx",
            ".psd", // Adobe Photoshop Document

            // Audio
            ".mp3",
            ".m4a",
            ".ogg",
            ".wav",

            // Video
            ".mp4", // MPEG-4
            ".m4v",
            ".mov", // QuickTime
            ".wmv", // Windows Media Video
            ".avi",
            ".mpg",
            ".ogv", // Ogg
            ".3gp", // 3GPP
        ],

      // 应用于从媒体库提供的资产的内容安全策略。
      "ContentSecurityPolicy" : "default-src 'self'; style-src 'unsafe-inline'"
    }
```

要更改配置，请在`Startup.cs`中添加以下代码：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MediaOptions>(options =>
    {
        options.MaxBrowserCacheDays = 365;
    });
}
```

要更改配置，请在`appsettings.json`中添加以下代码：

```json
{
  "OrchardCore_Media": {
    "MaxBrowserCacheDays": 365
  }
}
```

## 静态文件

要使用静态文件，请在`Startup.cs`中添加以下代码：

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseStaticFiles();
}
```

要使用静态文件，请在`appsettings.json`中添加以下代码：

```json
{
  "StaticFileOptions": {
    "FileProvider": {
      "Type": "Physical",
      "Properties": {
        "Root": "C:\\Files"
      }
    },
    "RequestPath": "/files",
    "OnPrepareResponse": [
      {
        "Action": "SetCacheControl",
        "Duration": "365.00:00:00"
      }
    ]
  }
}
```

## 事件

以下事件可用于在媒体库中执行自定义逻辑：

- `MediaCreating` - 在创建媒体项之前。
- `MediaCreated` - 在创建媒体项之后。
- `MediaUpdating` - 在更新媒体项之前。
- `MediaUpdated` - 在更新媒体项之后。
- `MediaDeleting` - 在删除媒体项之前。
- `MediaDeleted` - 在删除媒体项之后。

要订阅事件，请在`Startup.cs`中添加以下代码：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IEventHandler<MediaCreated>, MyMediaCreatedEventHandler>();
}
```

要发布事件，请使用以下代码：

```csharp
await _eventBus.PublishAsync(new MediaCreated { ContentItem = media.ContentItem });
```

## 模板

以下模板可用于在Liquid中访问媒体库：

```liquid
{% assign media = Model.ContentItem | media %}
{% assign media = Model.ContentItem | media_property:'MyMediaField' %}
{% assign media = 'animals/kittens.jpg' | asset_url | media %}
{% assign media = 'animals/kittens.jpg' | asset_url | append_version | media %}
{% assign media = 'animals/kittens.jpg' | asset_url | append_version | media_property:'MyMediaField' %}

{% if media %}
  <img src="{{ media.Paths[0] | asset_url }}" alt="{{ media.MediaTexts[0] }}" />
{% endif %}
```

以下模板可用于在Razor中访问媒体库：

```html
@using OrchardCore.Media
@inject IOrchardHelper Orchard

@{
    var media = Model.ContentItem | media;
    var media = Model.ContentItem | media_property:'MyMediaField';
    var media = Orchard.AssetUrl("animals/kittens.jpg") | media;
    var media = Orchard.AssetUrl("animals/kittens.jpg", appendVersion: true) | media;
    var media = Orchard.AssetUrl("animals/kittens.jpg", appendVersion: true) | media_property:'MyMediaField';
}

@if (media != null)
{
    <img src="@Orchard.AssetUrl(media.Paths[0])" alt="@media.MediaTexts[0]" />
}
```

## 调整大小

要调整大小，请使用以下Liquid代码：

```liquid
{% assign media = 'animals/kittens.jpg' | asset_url | media %}
{% assign url = media.Paths[0] | resize_url: width:100, height:240, mode:'crop' %}
<img src="{{ url }}" alt="{{ media.MediaTexts[0] }}" />
```

要调整大小，请使用以下Razor代码：

```html
@using OrchardCore.Media
@inject IOrchardHelper Orchard

@{
    var media = Orchard.AssetUrl("animals/kittens.jpg") | media;
    var url = Orchard.ResizeUrl(media.Paths[0], width:100, height:240, resizeMode:ResizeMode.Crop);
}

@if (media != null)
{
    <img src="@url" alt="@media.MediaTexts[0]" />
}
```

## 媒体配置文件

媒体配置文件允许您定义预设的图像调整大小和格式化命令。

您可以从“配置->媒体->媒体配置文件”菜单中创建媒体配置文件。

使用Liquid、Razor助手或标记助手指定媒体配置文件时，您提供配置文件名称和要应用于媒体项的任何其他命令。

=== "Liquid"

    ``` liquid
    {% resize_url profile: 'medium' %}
    {% resize_url profile: 'medium', mode: 'crop' %}
    ```

=== "Razor"

    ``` html
    @await Orchard.AssetProfileUrlAsync(Model.Paths[0], "medium");
    @await Orchard.AssetProfileUrlAsync(Model.Paths[0], "medium", resizeMode: ResizeMode.Crop);
    ```

=== "标记"

    ``` html
    <img asset-src="Model.Paths[0]" img-profile="medium" />
    <img asset-src="Model.Paths[0]" img-profile="medium" img-resize-mode="Crop"/>
    ```

!!! 注意
    媒体配置文件仅在[预览源](../../../getting-started/preview-package-source)中可用

## 媒体文本

媒体文本是“媒体字段”的可选设置，默认情况下打开。

当提供时，它允许字段的编辑器为每个选定的媒体项包括文本值。

这可用于图像的“alt”标记。

启用设置后，模板必须读取并提供值给“img”标记。

`MediaTexts []`与`Paths []`数组保持同步，给定路径的索引表示`MediaText`值的索引。

## 图像锚点

图像锚点是“媒体字段”的可选设置，默认情况下关闭。

启用后，它们允许媒体字段提供锚点或用于裁剪或填充图像时的x和y值。

提供的锚点值可用于指定裁剪或填充的中心点。

启用设置后，模板必须读取并提供值给调整大小的帮助程序或过滤器。

`Anchors []`是`MediaField`的一个不太知名的属性，可以通过`GetAnchors（）`扩展或直接访问。

=== "Liquid"

    ``` liquid
    {% assign anchor = Model.ContentItem.Content.Blog.Image.Anchors.first %}
    ```

=== "Razor"

    ``` html
    var anchors = @Model.Field.GetAnchors();
    var anchors = (Anchor[])Model.ContentItem.Content.Blog.Image.Anchors.ToObject<Anchor[]>();
    ```

`Anchors []`与`Paths []`数组保持同步，给定路径的索引表示`Anchor`值的索引。

!!! 注意
    锚点仅在[预览源](../../../getting-started/preview-package-source)中可用

## 查询字符串令牌

调整图像大小时，默认情况下，查询字符串命令值使用HMAC签名进行签名，该签名对于租户是唯一的。

这可以防止恶意客户端创建太多相同图像的变体。

如果将“UseTokenizedQueryString”设置为“false”，则将删除以下功能。

- 缓存破坏或查询字符串版本控制。
- 锚点。
- 宽度或高度必须与“SupportedSizes”配置中的值匹配。
- 背景颜色。

当查询字符串使用令牌签名时，可以使用任何宽度、高度值。

`<img src="/media/kittens.jpg?width=101&height=241&token=0J3hyv6jIPEsSdlvTCrf30fIdygkpmrF6mphqgYQyas%3D">`

!!! 注意
    令牌仅在[预览源](../../../getting-started/preview-package-source)中可用
    在此之前，宽度或高度值限于`16`、`32`、`50`、`100`、`160`、`240`、`480`、`600`、`1024`、`2048`。

## 媒体索引

如果通过媒体字段引用文件，则可以选择将媒体索引用于搜索。对于从媒体字段引用的每个文件，可以为以下数据进行索引：

- 媒体文本
- PDF文件的文本内容

!!! 注意
    独立文件，即仅上传到媒体库但从未通过媒体字段引用的文件，无法进行索引。

!!! 注意
    您需要启用索引实现才能使用媒体索引和搜索。下面的指南假定您正在使用[Lucene](../Lucene/README.md)。

要设置媒体索引，请执行以下操作：

1. 对于每个媒体字段，从给定内容类型或内容部分的编辑器中打开字段的编辑器，并选中“将此元素包含在索引中”，并选中“存储”和“分析”。
2. 下次发布该类型的内容时，媒体内容也将被索引。检查新Lucene字段的名称。您可以通过运行Lucene查询来执行此操作，以获取给定内容类型的名称（可以从管理界面的搜索下运行Lucene查询）：您应该能够看到两个名为“ContentPart.FieldTechnicalName.MediaText”和“ContentPart.FieldTechnicalName.FileText”的新字段，例如“BlogPost.File.MediaText”和“BlogPost.File.FileText”。
3. 配置要用于搜索的新字段。您可以从管理界面的搜索下进行此操作，设置，搜索，并在“默认搜索字段”下添加新字段的名称（到达类似“Content.ContentItem.FullText，BlogPost.File.MediaText，BlogPost.File.FileText”的内容）。
4. 尝试仅在所选媒体文件的媒体文本或引用的PDF文件中搜索内容。您应该看到相应的结果。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/BQHUlvPFRR4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/K0_i4vj00yM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/bDxL2LPJPzk" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Nb5GUqM7ZzI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 信用

要索引PDF文件，使用[PdfPig库](https://github.com/UglyToad/PdfPig/)。
> 该文档由ChatGPT 4 翻译
