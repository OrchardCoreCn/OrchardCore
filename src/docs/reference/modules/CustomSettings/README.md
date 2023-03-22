# Custom Settings (`OrchardCore.CustomSettings`)
# 自定义设置 (`OrchardCore.CustomSettings`)

自定义设置允许网站管理员创建一组全局属性。这些设置在标准设置部分中进行编辑，并可以使用特定权限进行保护。

## 创建自定义设置

自定义设置按部分组织。每个部分由具有`CustomSettings`原型的内容类型表示。  
创建此类部分时，请记住禁用`Creatable`、`Listable`、`Draftable`和`Securable`元数据，因为它们不适用。

!!! 警告
    不要将任何现有的内容类型标记为此`CustomSettings`原型，因为这将破坏此类型的现有内容项。

自定义设置由部分和字段组成，就像其他任何内容类型一样。  
创建后，打开设置菜单项，每个部分都应出现在模块提供的部分旁边。

### 权限

每个自定义设置部分都会获得专用权限，以允许特定用户对其进行编辑。

要编辑此权限，请打开角色编辑器并转到`OrchardCore.CustomSettings`功能组。

## 用法

### Liquid

自定义设置（如其他设置）在`{{Site.Properties}}`对象中可用。  
每个部分都可以使用其名称。

例如，名为`BlogSettings`的自定义设置部分的`HtmlBodyPart`将可通过`{{Site.Properties.BlogSettings.HtmlBodyPart}}`访问。

### 代码

自定义设置是ContentItem，通过将其访问为`ContentItem`，您可以访问其部分和元数据。

!!! 注意
    您需要在[ContentTypes文档](../ContentTypes/README.md)中演示的依赖注入中注册您的`ContentPart`。

以下是获取名为`BlogSettings`的自定义设置部分的`HtmlBodyPart`的示例：

!!! 警告
    这些类型可能会在CMS中进行修改。在消耗它们的代码中，确保这些类型不会在开发周期之外进行修改。

```csharp
public class MyController : Controller
{
    private readonly ISiteService _siteService;
    public MyController(ISiteService siteService)
    {
        _siteService = siteService;
    }
    public async Task<IActionResult> Index()
    {
        var siteSettings = await _siteService.GetSiteSettingsAsync();
        var blogSettings = siteSettings.As<ContentItem>("BlogSettings");
        var blogHtml = blogSettings.As<HtmlBodyPart>();
 View();
    }
}
```

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/RuDsBx4wdT0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/RuDsBx4wdT0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
> 该文档由ChatGPT 4 翻译
