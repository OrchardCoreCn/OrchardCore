# 导航 (`OrchardCore.Navigation`)

## 目的

提供 `Navigation`、`Pager` 和 `PagerSlim` 形状。

## 主题

可以通过将适当的局部视图文件添加到主题的视图文件夹来为导航设置主题。
[`TheAdmin` 主题项目](https://github.com/OrchardCMS/OrchardCore/tree/dev/src/OrchardCore.Themes/TheAdmin)提供了一个很好的示例。

该主题创建了标准的垂直导航菜单，该菜单可以在任何 OrchardCore 应用程序的管理仪表板上找到。
`TheAdmin` 主题提供了以下替代默认提供的 `Navigation` 模块的选项：

- `Navigation-admin.cshtml`
- `NavigationItem-admin.cshtml`
- `NavigationItemLink-admin.cshtml`

主题开发人员可以完全控制导航在其 OrchardCore 应用程序中的显示位置和方式。


### 分页


这是一个多用途的分页组件，用于呈现到特定页面编号的链接。
它可以选择性地呈现“第一个”和“最后一个”链接。

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Page` | `int` | 活动页面编号。 |
| `PageSize` | `int` | 每页的项目数。 |
| `TotalItemCount` | `double` | 项目的总数（用于计算最后一页的编号）。 |
| `Quantity` | `int?` | 要显示的页面数，如果未指定，则为 7。 |
| `FirstText` | `object` | “第一个”链接的文本，默认值：`S["<<"]`。|
| `PreviousText` | `object` | “上一页”链接的文本，默认值：`S["<"]`。 |
| `NextText` | `object` | “下一页”链接的文本，默认值：`S[">"]`。|
| `LastText` | `object` | “最后一页”链接的文本，默认值：`S[">>"]`。 |
| `GapText` | `object` | “间隔”元素的文本，默认值：`S["..."]`。 |
| `PagerId` | `string` | 分页器的标识符。用于创建类似 `Pager__[PagerId]` 的替代。 |
| `ShowNext` | `bool` | 如果为 true，则始终显示“下一页”链接。 |

从 `List` 形状继承的属性：

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `ItemTagName` | `string` | 用于页面的 HTML 标记，默认值：`li`。 |
| `ItemClasses` | `List<string>` | 分配给页面的类，默认值：_none_。 |
| `ItemAttributes` | `Dictionary<string, string>` | 分配给页面的属性。 |
| `FirstClass` | `string` | 用于第一页的 HTML 类，默认值：`first`。 |
| `LastClass` | `string` | 用于最后一页的 HTML 标记，默认值：`last`。 |

从基本 Shape 类继承的属性：

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Id` | `string` | 用于分页器的 HTML id，默认值：_none_。 |
| `TagName` | `string` | 用于分页器的 HTML 标记，默认值：`ul`。 |
| `Attributes` | `Dictionary<string, string>` | 分配给主容器的属性。 |
| `Classes` | `Dictionary<string, string>` | 要添加到主 Tag 元素的 CSS 类。 |

`PagerId` 属性用于为特定实例创建模板。例如，将值 `MainBlog` 分配给 `PagerId`，然后呈现分页器将查找名为 `Pager-MainBlog.cshtml` 的模板。

可以通过为以下形状定义模板来进一步自定义分页器：

- `Pager_Gap`
- `Pager_First`
- `Pager_Previous`
- `Pager_Next`
- `Pager_Last`
- `Pager_CurrentPage`

这些形状中的每一个最终都会变形为 `Pager_Link`。
每个这些形状的替代品都使用 `PagerId` 创建，例如 `Pager_Previous__[PagerId]`，它将依次查找模板 `Pager-MainBlog.Previous.cshtml`。

### `PagerSlim`

此形状呈现由两个链接组成的分页器：_Previous_ 和 _Next_。

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `PreviousClass` | `string` | 用于“上一页”链接的 HTML 类，默认值：_none_。 |
| `NextClass` | `string` | 用于“下一页”链接的 HTML 类，默认值：_none_。 |
| `PreviousText` | `object` | “上一页”链接的文本，默认值：`S["<"]`。 |
| `NextText` | `object` | “下一页”链接的文本，默认值：`S[">"]`。 |
| `UrlParams` | `Dictionary<string, string>` | 要传递给分页器的查询字符串参数。参数名称和值按顺序排列 |

从 `List` 形状继承的属性：

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `ItemTagName` | `string` | 用于页面的 HTML 标记，默认值：`li`。 |
| `ItemClasses` | `List<string>` | 分配给页面的类，默认值：_none_。 |
| `ItemAttributes` | `Dictionary<string, string>` | 分配给页面的属性。 |
| `FirstClass` | `string` | 用于第一页的 HTML 类，默认值：`first`。 |
| `LastClass` | `string` | 用于最后一页的 HTML 标记，默认值：`last`。 |

从基本 Shape 类继承的属性：

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Id` | `string` | 用于分页器的 HTML id，默认值：_none_。 |
| `TagName` | `string` | 用于分页器的 HTML 标记，默认值：`ul`。 |
| `Attributes` | `Dictionary<string, string>` | 分配给主容器的属性。 |
| `Classes` | `Dictionary<string, string>` | 要添加到主 Tag 元素的 CSS 类。 |

可以通过为以下形状定义模板来进一步自定义紧凑型分页器：

- `Pager_Previous`
- `Pager_Next`

`Pager_Next` 和 `Pager_Previous` 的 Liquid 替代品或模板示例：

```liquid
{% shape_clear_alternates Model %}
{% shape_type Model "Pager_Link" %}
{% shape_add_classes Model "page-link" %}
{{ Model | shape_render }}
```


这些形状中的每一个最终都会变形为 `Pager_Link`。
每个这些形状的替代品都使用 `PagerId` 创建，例如 `Pager_Previous` `[PagerId]`，它将依次查找模板 `Pager-MainBlog.Previous.cshtml`。

## SEO

为了阻止搜索引擎爬取所有分页器链接，可以使用“no-follow”覆盖分页器锚点的“rel”属性。为此，您可以简单地执行以下操作：

=== "Liquid"

    ``` liquid
    {% shape_pager Model.Pager attributes: "{\"rel\": \"no-follow\"}" %}
    ```

=== "C#"

    ``` html
    Model.Pager.Attributes["rel"] = "no-follow;
    @await DisplayAsync(Model.Pager)
    ```

## 扩展导航

可以通过实现 `INavigationProvider` 并在扩展模块（或主题）的 `Startup.cs` 文件中注册它来扩展导航。

以下是用于扩展站点“主”导航部分的 `INavigationProvider` 的示例实现。

```csharp
public class MainMenu : INavigationProvider
    {
        private readonly IStringLocalizer S;

        public MainMenu(IStringLocalizer<MainMenu> localizer)
        {
            S = localizer;
        }

        public async Task BuildNavigation(string name, NavigationBuilder builder)
        {
            //仅在此处与“main”导航菜单交互。
            if (!String.Equals(name, "main", StringComparison.OrdinalIgnoreCase))
            {
                return;
            }

            builder
                .Add(S["Notifications"], S["Notifications"], layers => layers
                    .Action("Index", "Template", new { area = "CRT.Client.OrchardModules.CommunicationTemplates", groupId = 1 })
                    .LocalNav()
                );
        }
    }
```  

只要站点使用包含类似于以下行的主题，该提供程序就会被调用，该行导致导航菜单在您的主题中的位置呈现：
`@await DisplayAsync(await New.Navigation(MenuName: "main", RouteData: @ViewContext.RouteData))`

在各种 OrchardCore 模块中可以找到扩展管理导航的示例。在 OrchardCore git 存储库中搜索 `AdminMenu` 将定位各种设置。以下是部分列表：

- `OrchardCore.Modules/OrchardCore.Admin/AdminFilter.cs`
- `OrchardCore.Modules/OrchardCore.Media/AdminMenu.cs`

目前，管理菜单是唯一在 OrchardCore git 存储库中动态添加项目的导航。但是，如上面的示例所示，该模式可用于控制任何命名的导航。

## 分页器代码示例

=== "Liquid"

    ``` liquid
    {% assign previousText = "← Newer Posts" | t %}
    {% assign nextText = "Older Posts →" | t %}
    {% assign previousClass = "previous" | t %}
    {% assign nextClass = "next" | t %}
    {% assign itemClasses = "itemclass1 itemclass2" | split: " " %}

    {% shape_pager Model.Pager previous_text: previousText, next_text: nextText,
        previous_class: previousClass, next_class: nextClass, tag_name: "div", item_tag_name: "div", attributes: "{\"key1\": \"value1\",\"key2\":\"value2\"}", item_attributes: "{\"key1\": \"value1\",\"key2\":\"value2\"}", classes: "class1 class2", item_classes: itemClasses %}

    {{ Model.Pager | shape_render }}
    ```

=== "C#"

    ``` html
    public async Task<IActionResult> List(MyViewModel viewModel, PagerParameters pagerParameters)
    {
        var siteSettings = await _siteService.GetSiteSettingsAsync();
        var pager = new Pager(pagerParameters, siteSettings.PageSize);
        var query = _session.Query<ContentItem, ContentItemIndex>();
        var maxPagedCount = siteSettings.MaxPagedCount;
        
        if (maxPagedCount > 0 && pager.PageSize > maxPagedCount)
            pager.PageSize = maxPagedCount;
                    
        var pagerShape = (await New.Pager(pager)).TotalItemCount(maxPagedCount > 0 ? maxPagedCount : await query.CountAsync()).RouteData(routeData).TagName("div").ItemTagName("div").Classes("class1 class2").ItemClasses(new List<string>(){ "itemclass1", "itemclass2" }).Attributes(new Dictionary<string, string>() { { "attribute", "value" } }).ItemAttributes(new Dictionary<string, string>() { { "itemattribute", "value" } });

        // Or you can also set the Shape base properties this way too :
        pagerShape.Id = "myid";
        pagerShape.TagName = "span";
        pagerShape.Attributes.Add("myattribute", "value");
        pagerShape.Classes.Add("myclassname");

        model.Pager = pagerShape;
        return View(viewModel);
    }
    ```
> 该文档由ChatGPT 4 翻译
