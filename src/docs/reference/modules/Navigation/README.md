# Navigation (`OrchardCore.Navigation`)

## 目的

提供了 `Navigation`, `Pager` 和 `PagerSlim`形状。

## 支持主题

通过将适当的局部视图文件添加到主题的“视图”文件夹中，可以对导航进行主题化。
一个很好的例子可以在该项目找到： [`TheAdmin` theme project](https://github.com/OrchardCMS/OrchardCore/tree/dev/src/OrchardCore.Themes/TheAdmin).

这个主题创建了标准的垂直导航菜单，可以在任何OrchardCore应用程序的管理仪表板上找到。
“TheAdmin”主题与“Navigation”模块中提供的默认主题相比提供了以下备选方案：

- `Navigation-admin.cshtml`  
- `NavigationItem-admin.cshtml`  
- `NavigationItemLink-admin.cshtml`  

主题开发人员可以完全控制如何以及在他们的OrchardCore应用程序中显示导航。

### 分页


这是一个多用途分页组件，它呈现指向特定页码的链接。
它可以选择性地呈现首个链接和最后一个链接。

| 参数 | 类型 | 说明 |
| --------- | ---- |------------ |
| `Page` | `int` | 页码。 |
| `PageSize` | `int` | 每页的项目数。 |
| `TotalItemCount` | `double` |项目总数（用于计算最后一页的数量）。 |
| `Quantity` | `int?` | 要显示的页数，如果未指定，则为7。 |
| `FirstText` | `object` | “第一个”链接的文本，默认值： `S["<<"]` .|
| `PreviousText` | `object` |“上一个”链接的文本，默认值：`S["<"]`. |
| `NextText` | `object` |“下一个”链接的文本，默认值： `S[">"]` .|
| `LastText` | `object` | 最后一个链接的文本，默认值： `S[">>"]`. |
| `GapText` | `object` | “间隙”元素的文本，默认值：`S["..."]`. |
| `PagerId` | `string` | 分页控件标识符。可以这样使用： `Pager__[PagerId]`. |
| `ShowNext` | `bool` | 如果为true，则始终显示“Next”链接。 |

从`List`形状继承的属性：

| 参数 | 类型 | 说明 |
| --------- | ---- |------------ |
| `ItemTagName` | `string` | 用于页面的HTML标记，默认值： `li`. |
| `ItemClasses` | `List<string>` | 分配给页面的类，默认值： _none_. |
| `ItemAttributes` | `Dictionary<string, string>` |分配给页面的属性。 |
| `FirstClass` | `string` | 用于第一页的HTML类，默认值： `first`. |
| `LastClass` | `string` | 用于最后一页的HTML标记，默认值： `last`. |

从基形状类继承的属性：

|参数|类型|说明|
| --------- | ---- |------------ |
| `Id` | `string` | 用于寻呼机的HTML id，默认值：_none_. |
| `TagName` | `string` | 用于寻呼机的HTML标记，默认值： `ul`. |
| `Attributes` | `Dictionary<string, string>` | 分配给主容器的属性。|
| `Classes` | `Dictionary<string, string>` | 要添加到主标记元素的CSS类。 |

`PagerId`属性用于为特定实例创建模板。
例如，将值`MainBlog` 赋值给 `PagerId`，然后呈现分页器将查找并使用这个模板：`Pager-MainBlog.cshtml`

通过为以下形状定义模板，可以进一步自定义分页：

- `Pager_Gap`
- `Pager_First`
- `Pager_Previous`
- `Pager_Next`
- `Pager_Last`
- `Pager_CurrentPage`

每一个形状最终都会变形成 `Pager_Link`.
这些形状的替换项都是使用 `PagerId` 创建的，比如`Previous\uuyu[PagerId]`，将查找模板`Pager-MainBlog.Previous.cshtml`。

### `PagerSlim`

此形状呈现由两个链接组成的分页控件：“上一个”和“下一个”。

|参数|类型|说明|
| --------- | ---- |------------ |
| `PreviousClass` | `string` | 用于上一个链接的HTML类，默认值：_none_. |
| `NextClass` | `string` | 用于“下一个链接”的HTML类，默认值： _none_. |
| `PreviousText` | `object` | “上一个”链接的文本，默认值： `S["<"]`. |
| `NextText` | `object` | “下一个”链接的文本，默认值： `S[">"]`. |
| `UrlParams` | `Dictionary<string, string>` | 查询要传递给寻呼机的参数。按顺序排列的参数名称和值 |


从`List`形状继承的属性：

|参数|类型|说明|
| --------- | ---- |------------ |
| `ItemTagName` | `string` | 用于页面的HTML标记，默认值： `li`. |
| `ItemClasses` | `List<string>` | 分配给页面的类，默认值： _none_. |
| `ItemAttributes` | `Dictionary<string, string>` | 分配给页面的属性。 |
| `FirstClass` | `string` | 用于第一页的HTML类，默认值： `first`. |
| `LastClass` | `string` | 用于最后一页的HTML标记，默认值： `last`. |

从基形状类继承的属性：

|参数|类型|说明|
| --------- | ---- |------------ |
| `Id` | `string` |用于分页器的HTML id，默认值：_none_. |
| `TagName` | `string` | 用于寻呼机的HTML标记，默认值： `ul`. |
| `Attributes` | `Dictionary<string, string>` | 分配给主容器的属性。 |
| `Classes` | `Dictionary<string, string>` | 要添加到主标记元素的CSS类。 |

通过为以下形状定义模板，可以进一步自定义简单分页：

- `Pager_Previous`
- `Pager_Next`

`Pager_Next` 和 `Pager_Previous` Liquid 模板:

```liquid
{% shape_clear_alternates Model %}
{% shape_type Model "Pager_Link" %}
{% shape_add_classes Model "page-link" %}
{{ Model | shape_render }}
```

每一个形状最终都会变形成`Pager_Link`.
Alternates for each of these shapes are created using the `PagerId` like `Pager_Previous` `[PagerId]` which would in turn look for the template `Pager-MainBlog.Previous.cshtml`.
这些形状的替换项是使用`PagerId`创建的，比如`Pager_Previous` `[PagerId]`，后者将依次查找模板`Pager-MainBlog.Previous.cshtml`.

## SEO

In order to block search engines from crawling all your pagers links, it is possible to override the Pager anchors "rel" attributes with "no-follow". To achieve this, you can simply do this: 

=== "Liquid"

    ``` liquid
    {% shape_pager Model.Pager attributes: "{\"rel\": \"no-follow\"}" %}
    ```

=== "C#"

    ``` html
    Model.Pager.Attributes["rel"] = "no-follow;
    @await DisplayAsync(Model.Pager)
    ```

## Extending Navigation

导航可以通过代码进行扩展，方法是实现`INavigationProvider`并将其注册到扩展模块（或主题）中`Startup.cs`文件。

Below is a sample implementation of an `INavigationProvider` used to extend the "main" navigation section of the site.
下面是一个`INavigationProvider`的示例实现，用于扩展站点的“main”导航部分。

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
            //只与这里的“主”导航菜单交互。
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

只要网站使用的主题包含类似于以下内容的行，则将调用此提供程序，该主题将在指定位置呈现导航菜单：
`@await DisplayAsync(await New.Navigation(MenuName: "main", RouteData: @ViewContext.RouteData))`

扩展管理导航的例子可以在不同的Orchard Core模块中找到。在存储库中搜索“AdminMenu”将找到各种设置。以下是部分列表：

- `OrchardCore.Modules/OrchardCore.Admin/AdminFilter.cs`
- `OrchardCore.Modules/OrchardCore.Media/AdminMenu.cs`

此时，“管理菜单”是唯一一个在OrchardCoreGit存储库中动态添加项目的代码导航。但是，正如上面的示例所示，模式可以用于控制任何命名导航。

## 分页代码示例

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
