# 菜单 (`OrchardCore.Menu`)

## Shapes

### `Menu`

`Menu` 形状用于呈现菜单。

| 属性 | 描述 |
| --------- | ------------ |
| `Model.ContentItemId` | 如果定义，则包含要呈现的菜单的内容项标识符。 |
| `Model.Items` | 菜单的菜单项形状列表。这些是 `MenuItem` 类型的形状。 |
| `Model.Differentiator` | 如果定义，则包含菜单的格式化名称（标题）。例如 `MainMenu`。 |

#### 菜单备用

| 定义 | 模板 | 文件名 |
| ---------- | --------- | ------------ |
| `Menu__[Differentiator]` | `Menu__MainMenu` | `Menu-MainMenu.cshtml` |

#### 菜单示例

=== "Liquid"

    ``` liquid
    <nav>
        <ul class="nav navbar-nav {{ Model.Classes | join: " " }}">
            {% for item in Model.Items %}
                {{ item | shape_render }}
            {% endfor %}
        </ul>
    </nav>
    ```

=== "Razor"

    ``` html
    @{
        TagBuilder tag = Tag(Model, "ul");
        tag.AddCssClass("nav navbar-nav");

        foreach (var item in Model.Items)
        {
            tag.InnerHtml.AppendHtml(await DisplayAsync(item));
        }
    }

    @tag
    ```

### `MenuItem`

`MenuItem` 形状用于呈现菜单项。

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Menu` | 拥有此项的 `Menu` 形状。 |
| `Model.ContentItem` | 表示此菜单项的内容项。 |
| `Model.Level` | 菜单项的级别。顶级菜单项为 `0`。 |
| `Model.Items` | 子菜单项形状列表。这些是 `MenuItem` 类型的形状。 |
| `Model.Differentiator` | 如果定义，则包含菜单的格式化名称。例如 `MainMenu`。 |

#### 菜单项备用

| 定义 | 模板 | 文件名 |
| ---------- | --------- | ------------ |
| `MenuItem__level__[level]` | `MenuItem__level__2` | `MenuItem-level-2.cshtml` |
| `MenuItem__[ContentType]` | `MenuItem__HtmlMenuItem` | `MenuItem-HtmlMenuItem.cshtml` |
| `MenuItem__[ContentType]__level__[level]` | `MenuItem__HtmlMenuItem__level__2` | `MenuItem-HtmlMenuItem-level-2.cshtml` |
| `MenuItem__[MenuName]` | `MenuItem__MainMenu` | `MenuItem-MainMenu.cshtml` |
| `MenuItem__[MenuName]__level__[level]` | `MenuItem__MainMenu__level__2` | `MenuItem-MainMenu-level-2.cshtml` |
| `MenuItem__[MenuName]__[ContentType]` | `MenuItem__MainMenu__HtmlMenuItem` | `MenuItem-MainMenu-HtmlMenuItem.cshtml` |
| `MenuItem__[MenuName]__[ContentType]__level__[level]` | `MenuItem__MainMenu__HtmlMenuItem__level__2` | `MenuItem-MainMenu-HtmlMenuItem-level-2.cshtml` |

#### 菜单项示例

=== "Liquid"

    ``` liquid
    <li class="nav-item{% if Model.HasItems %} dropdown{% endif %}">
        {% shape_clear_alternates Model %}
        {% shape_type Model "MenuItemLink" %}
        {{ Model | shape_render }}
        {% if Model.HasItems %}
        <div class="dropdown-menu">
            {% for item in Model.Items %}
            {{ item | shape_render }}
            {% endfor %}
        </div>
        {% endif %}
    </li>
    ```

=== "Razor"

    ``` html
    @{
        TagBuilder tag = Tag(Model, "li");

        if ((bool)Model.HasItems)
        {
            tag.AddCssClass("dropdown");
        }

        // Morphing the shape to keep Model untouched
        Model.Metadata.Alternates.Clear();
        Model.Metadata.Type = "MenuItemLink";

        tag.InnerHtml.AppendHtml(await DisplayAsync(Model));

        if ((bool)(Model.HasItems))
        {
            TagBuilder parentTag = Tag(Model, "div");
            parentTag.AddCssClass("dropdown-menu");

            foreach (var item in Model.Items)
            {
                item.ParentTag = parentTag;
                parentTag.InnerHtml.AppendHtml(await DisplayAsync(item));
            }

            tag.InnerHtml.AppendHtml(parentTag);
        }
    }

    @tag
    ```

### `MenuItemLink`

`MenuItemLink` 形状用于呈现菜单项链接。
通过将 `MenuItem` 形状变形为 `MenuItemLink` 来创建此形状。因此，所有属性
在 `MenuItem` 形状上可用。

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Menu` | 拥有此项的 `Menu` 形状。 |
| `Model.ContentItem` | 表示此菜单项的内容项。 |
| `Model.Level` | 菜单项的级别。顶级菜单项为 `0`。 |
| `Model.Items` | 子菜单项形状列表。这些是 `MenuItem` 类型的形状。 |
| `Model.Differentiator` | 如果定义，则包含菜单的格式化名称。例如 `MainMenu`。 |

#### `MenuItemLink` 备用

| 定义 | 模板 | 文件名 |
| ---------- | --------- | ------------ |
| `MenuItemLink__level__[level]` | `MenuItemLink__level__2` | `MenuItemLink-level-2.cshtml` |
| `MenuItemLink__[ContentType]` | `MenuItemLink__HtmlMenuItem` | `MenuItemLink-HtmlMenuItem.cshtml` |
| `MenuItemLink__[ContentType]__level__[level]` | `MenuItemLink__HtmlMenuItem__level__2` | `MenuItemLink-HtmlMenuItem-level-2.cshtml` |
| `MenuItemLink__[MenuName]` | `MenuItemLink__MainMenu` | `MenuItemLink-MainMenu.cshtml` |
| `MenuItemLink__[MenuName]__level__[level]` | `MenuItemLink__MainMenu__level__2` | `MenuItemLink-MainMenu-level-2.cshtml` |
| `MenuItemLink__[MenuName]__[ContentType]` | `MenuItemLink__MainMenu__HtmlMenuItem` | `MenuItemLink-MainMenu-HtmlMenuItem.cshtml` |
| `MenuItemLink__[MenuName]__[ContentType]__level__[level]` | `MenuItemLink__MainMenu__HtmlMenuItem__level__2` | `MenuItemLink-MainMenu-HtmlMenuItem-level-2.cshtml` |

#### `MenuItemLink` 示例

=== "Liquid"

    ``` liquid
    {% assign link = Model.ContentItem.Content.LinkMenuItemPart %}

    {% if Model.HasItems %}
        <a href="{{ link.Url | href }}" class="nav-link dropdown-toggle">{{ link.Name }}<b class="caret"></b></a>
    {% else %}
        <a href="{{ link.Url | href }}" class="nav-link">{{ link.Name }}</a>
    {% endif %}
    ```

=== "Razor"

    ``` html
    @using OrchardCore.ContentManagement

    @{
        ContentItem contentItem = Model.ContentItem;
        var link = contentItem.Content["LinkMenuItemPart"];
    }

    if ((bool)(Model.HasItems))
    {
        <a class="nav-link dropdown-toggle" data-bs-toggle="dropdown" href="@Url.Content((string)link.Url)">@link.Name<b class="caret"></b></a>
    }
    else
    {
        <a class="nav-link" href="@Url.Content((string)link.Url)">@link.Name</a>
    }
    ```

## 标记菜单中的活动项

有时需要将菜单项设置为活动状态，如果它是当前显示的菜单项。
因为菜单被缓存，所以必须从 javascript 中完成。OrchardCore.Menu 模块提供了一个脚本来帮助您完成此操作。

``` javascript
function activateLinks(options,cb)
```

| 参数 | 描述 |
| -------- | ----------- |
| `options: {class:'active',selector:null,traverse:0}` | 使用 `class` 定义要添加到具有当前 url 作为 href 的锚标记的父级的类。如果要应用于子级，请设置 `selector` 属性。使用 `traverse` 来查找菜单树中比当前显示的菜单更不具体的 url 的正数。例如 如果在菜单中有指向 '/todo-items' 的链接，并且您已导航到 '/todo-items/Create'，则会尝试查找菜单树中比当前显示的菜单更少的项目，并将其激活。 |
| `cb: function( items )` | 如果找到活动项，则会调用回调进行额外配置。例如 展开嵌套的菜单项，如果活动的菜单项是嵌套的。|

### `activateLinks` 在 `Layout` 文件中的用法

=== "Liquid"

    ``` liquid
    ...
    {% script at:"Foot", src:"~/OrchardCore.Menu/Scripts/activate-links.min.js", debug_src:"~/OrchardCore.Menu/Scripts/activate-links.js" %}
    ...
        <resources type="Footer" />
    ...
        <script>
            (function ($) {
                $('#mainNav').activateLinks({ selector: 'a', traverse: 2 }, function (items) {
                    var parents = $(items).closest(".has-treeview")
                    parents.addClass('menu-open');
                });
            })(jQuery);
        </script>
    ...
    ```

=== "Razor"

    ``` html
    ...
    <script at="Foot" asp-src="~/OrchardCore.Menu/Scripts/activate-links.min.js" debug-src="~/OrchardCore.Menu/Scripts/activate-links.js"></script>
    ...
    <resources type="Footer" />
    ...
    <script>
        (function ($) {
            $('#mainNav').activateLinks({ selector: 'a', traverse: 2 }, function (items) {
                var parents = $(items).closest(".has-treeview")
                parents.addClass('menu-open');
            });
        })(jQuery);
    </script>
    ...
    ```



## Video

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/mOhbqHKd_CI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
## CREDITS

### nestedSortable jQuery plugin

<https://github.com/ilikenwf/nestedSortable>  
License: MIT
> 该文档由ChatGPT 4 翻译
