# Razor 语法帮助器

在Razor中，有许多扩展方法可用于`@Orchard`。

## Razor扩展

| 方法 | 模块 | 描述 |
| ------ | ------ | ----------- |
| `DisplayAsync(ContentItem content, string displayType = "")` | OrchardCore.ContentManagement.Display | 使用相应的显示类型呈现内容项。 |
| `GetContentCultureAsync(ContentItem contentItem)` | OrchardCore.ContentLocalization | 返回给定ContentItem的文化。 |
| `CultureDir()` | OrchardCore.DisplayManagement | 返回当前文化方向。 |
| `CultureName()` | OrchardCore.DisplayManagement | 返回当前文化名称。 |
| `ResourceUrl(string resourcePath, bool? appendVersion = null)` | OrchardCore.ResourceManagement | 将Cdn Base URL前缀添加到指定的资源路径。 |
| `GetContentItemIdByAliasAsync(string alias)` | OrchardCore.Alias | 从其别名返回内容项ID。例如：`carousel` |
| `GetContentItemIdBySlugAsync(string slug)` | OrchardCore.Autoroute | 从其slug返回内容项ID。例如：`myblog/my-blog-post` |
| `GetContentItemIdByHandleAsync(string handle)` | OrchardCore.Contents | 从其句柄返回内容项ID。例如：`alias:carousel`，`slug:myblog/my-blog-post` |
| `GetContentItemByAliasAsync(string alias, bool latest = false)` | OrchardCore.Alias | 通过其别名加载内容项，寻找最新版本或不寻找。例如：`carousel` |
| `GetContentItemBySlugAsync(string slug, bool latest = false)` | OrchardCore.Autoroute | 通过其slug加载内容项，寻找最新版本或不寻找。例如：`slug:myblog/my-blog-post`|
| `GetContentItemByHandleAsync(string handle, bool latest = false)` | OrchardCore.Contents | 通过其句柄加载内容项，寻找最新版本或不寻找。例如：`alias:carousel`，`slug:myblog/my-blog-post`|
| `GetContentItemByIdAsync(string contentItemId, bool latest = false)` | OrchardCore.Contents | 通过其ID加载内容项。 |
| `GetContentItemsByIdAsync(IEnumerable<string> contentItemIds, bool latest = false)` | OrchardCore.Contents | 通过其ID加载一组内容项。 |
| `GetContentItemByVersionIdAsync(string contentItemVersionId)` | OrchardCore.Contents | 通过其版本ID加载内容项。 |
| `QueryContentItemsAsync(Func<IQuery<ContentItem, ContentItemIndex>, IQuery<ContentItem>> query)` | OrchardCore.Contents | 查询内容项。 |
| `GetRecentContentItemsByContentTypeAsync(string contentType, int maxContentItems = 10)` | OrchardCore.Contents | 加载特定类型的内容项。 |
| `LiquidToHtmlAsync(string liquid)` | [OrchardCore.Liquid](../../modules/Liquid/README.md#razor-helpers) | 将liquid字符串解析为HTML。 |
| `LiquidToHtmlAsync(string liquid, object model)` | [OrchardCore.Liquid](../../modules/Liquid/README.md#razor-helpers) | 将liquid字符串解析为HTML。 |
| `SanitizeHtml(string html)` | [OrchardCore.Infrastructure](../Sanitizer/README.md#razor-helper) | 清理HTML字符串。 |
| `QueryListItemsCountAsync(string listContentItemId, Expression<Func<ContentItemIndex, bool>> itemPredicate = null)` | OrchardCore.Lists | 返回列表计数。 |
| `QueryListItemsAsync(string listContentItemId, Expression<Func<ContentItemIndex, bool>> itemPredicate = null)` | [OrchardCore.List](../../modules/Lists/README.md#orchard-helpers) | 返回列表项。 |
| `MarkdownToHtmlAsync(string listContentItemId, Expression<Func<ContentItemIndex, bool>> itemPredicate = null)` | [OrchardCore.Markdown](../../modules/Markdown/README.md#razor-helper) | 将Markdown字符串转换为HTML。 |
| `AssetUrl(string assetPath, int? width = null, int? height = null, ResizeMode resizeMode = ResizeMode.Undefined, bool appendVersion = false)` | [OrchardCore.Media](../../modules/Media/README.md#razor-helpers) | 返回具有可选调整大小参数的指定资产路径的相对URL。 |
| `ImageResizeUrl(string imagePath, int? width = null, int? height = null, ResizeMode resizeMode = ResizeMode.Undefined)` | [OrchardCore.Media](../../modules/Media/README.md#razor-helpers) | 返回具有自定义调整大小参数的现有图像路径的URL。 |
| `ContentQueryAsync(string queryName)` | [OrchardCore.Queries](../../modules/Queries/README.md#razor-helpers) | 返回Content项列表 |
| `ContentQueryAsync(string queryName, IDictionary<string, object> parameters)` | [OrchardCore.Queries](../../modules/Queries/README.md#razor-helpers) | 返回Content项列表 |
| `QueryAsync(string liquid, object model)` | [OrchardCore.Queries](../../modules/Queries/README.md#razor-helpers) | 返回对象列表 |
| `QueryAsync(string queryName, IDictionary<string, object> parameters)` | [OrchardCore.Queries](../../modules/Queries/README.md#razor-helpers) | 返回对象列表 |
| `ShortcodesToHtmlAsync(string html, object model = null)` | [OrchardCore.Shortcodes](../../modules/Shortcodes/README.md#rendering-shortcodes) | 渲染短代码。 |
| `GetTaxonomyTermAsync(string taxonomyContentItemId, string termContentItemId)` | [OrchardCore.Taxonomies](../../modules/Taxonomies/README.md#orchard-helpers) | 从其内容项ID和分类法返回术语。 |
| `GetInheritedTermsAsync(string taxonomyContentItemId, string termContentItemId)` | [OrchardCore.Taxonomies](../../modules/Taxonomies/README.md#orchard-helpers) | 返回包括其父项在内的术语列表。 |
| `QueryCategorizedContentItemsAsync(string taxonomy(Func<IQuery<ContentItem, TaxonomyIndex>, IQuery<ContentItem>> query)` | [OrchardCore.Taxonomies](../../modules/Taxonomies/README.md#orchard-helpers) | 查询内容项。 |

## 如何使用

如果您想在视图中使用扩展方法，可以在文件顶部注入名为`Orchard`的`IOrchardHelper`：

```csharp
@inject OrchardCore.IOrchardHelper Orchard
```

在`OrchardCore.DisplayManagement.Razor`中，有一个RazorPage，它已经有一个名为`Orchard`的公共属性，您可以使用它来调用扩展方法或当前的`HttpContext`。

如果您想在控制器中使用Orchard助手，可以在构造函数中注入一个实例：

```csharp
private IOrchardHelper _orchard;

public MyClass(IOrchardHelper orchard)
{
	_orchard = orchard;
}
```

!!! 注意
    如果您想要使用的扩展方法找不到（例如在主题中），请不要忘记引用相应的模块。

    
> 该文档由ChatGPT 4 翻译
