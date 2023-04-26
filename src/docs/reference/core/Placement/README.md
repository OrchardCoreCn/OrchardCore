# OrchardCore.DisplayManagement

本文介绍了显示管理和放置文件。

## 放置文件

任何模块或主题都可以包含一个可选的 `placement.json` 文件，提供自定义放置逻辑。

!!! 注意
    `placement.json` 文件必须添加到模块或主题的根目录中。

### 格式

`placement.json` 文件包含一个对象，其属性是形状类型。 这些属性中的每一个都是放置规则的数组。

在以下示例中，我们描述了 `TextField` 和 `Parts_Contents_Publish` 形状的放置。

```json
{
  "TextField": [ ... ],
  "Parts_Contents_Publish" : [ ... ]
}
```

放置规则包含两组数据：

- **过滤器** - 定义要针对哪些特定形状进行定位。
- **放置信息** - 匹配过滤器时要应用的放置信息。

目前，您可以按以下方式过滤形状：

- 它们的原始类型，即放置规则的属性名称，例如 `TextField` 或 `ContentPart`。
- `displayType`（可选）：显示类型，例如 `Summary` 和 `Detail` 是最常见的。
- `differentiator`（可选）：区分用于多个元素重用的形状类型的差异器，例如字段名称。

!!! 注意
    形状类型（placement.json 属性名称）不一定与您的部件类型对齐。 例如，如果您创建了一个没有部件驱动程序的内容部件 `GalleryPart`，则其形状类型将是 `ContentPart`，差异器为 `GalleryPart`。 因此，您的 placement.json 将如下所示
```json
{
  "ContentPart": [{
  "place":"SomeZone"
  "differentiator":"GalleryPart"
  }],
  "GalleryPart": [{...}], //这不起作用，除非您为该部分注册了驱动程序
}
```

可以通过实现 `IPlacementNodeFilterProvider` 来添加其他自定义过滤器提供程序。

对于从内容项构建的形状，您可以通过以下内置过滤器提供程序进行过滤：

- `contentType`（可选）：单个 ContentType 或 Stereotype，或与前面的值开头匹配的 ContentTypes 和/或 Stereotypes 的数组。 可以使用 `*` 匹配以前面的值开头的所有内容类型，例如 `Art*`。
- `contentPart`（可选）：应该包含从中构建形状的内容项的单个 ContentPart 或 ContentParts 的数组。
- `path`（可选）：应该匹配请求路径的单个路径或路径的数组。

放置信息包括：

- `place`（可选）：呈现区域中形状的实际位置。 值为 `-` 将隐藏形状，以 `/` 开头的值将将形状移动到布局区域。
- `alternates`（可选）：要添加到当前形状元数据的备用形状类型数组。
- `wrappers`（可选）：用于当前形状的包装器形状类型数组。
- `shape`（可选）：替换形状类型。

```json
{
  "TextField": [
    {
      "displayType": "Detail",
      "differentiator": "Article-MyTextField",
      "contentType": [ "Page", "BlogPost" ],
      "contentPart": [ "HtmlBodyPart" ],
      "path": [ "/mypage" ],
      "place": "Content",
      "alternates": [ "TextField_Title" ],
      "wrappers": [ "TextField_Title" ],
      "shape": "AnotherShape"
    }
  ],
}
```

### 放置优先级

形状选择的放置信息基于以下顺序：

1. 主启动项目（这可以充当超级主题）
2. 活动主题（如果您正在查看前端，则为活动前端主题；如果您正在查看管理，则为活动管理主题）
3. 模块（按依赖项排序）

### 放置字段

字段具有自定义差异器，因为它们的形状在许多地方使用。 它是使用它所包含的 `Part` 的名称和 `Field` 的名称构建的。 例如，如果将名为 `MyField` 的字段添加到 `Article` 内容类型中，则其差异器将为 `Article-MyField`。 如果将名为 `City` 的字段添加到 `Address` 部分中，则其差异器将为 `Address-City`。

## 形状差异器

您可以在[模板文档](../../modules/Templates/README.md#content-field-differentiator)中找到有关形状差异器的信息。

## 形状

### 什么是形状？

有关形状的所有信息都在[此视频](https://youtu.be/gKLjtCIs4GU)中。

### 渲染形状

您可以使用 `<shape>` 标记助手来呈现任何形状，甚至传递属性。

=== "Razor"

    ``` html
    @{
        var intValue = 1;
        var stringValue = "a";
    }

    @await DisplayAsync(await New.MyShape(Foo: 1, Bar: "a"))

    <shape type="MyShape" foo="1" bar="a" />

    <shape type="MyShape" prop-foo="1" bar="a" />

    <shape type="MyShape" prop-foo="@intValue" prop-bar="@stringValue" />
    ```

=== "Liquid"

    ``` liquid
    {% assign customShape = "MyShape" | shape_new %}
    {% shape_add_properties customShape my_string: "String Test 3", my_int: 1 %}
    {{ customShape | shape_render }}

    {% "MyShape" | shape_new | shape_properties: my_int: 3, my_string: "String Test 3" | shape_render %}
    ```

要呈现内容项，您还可以使用以下标记助手。
注意：您需要将 `@addTagHelper *，OrchardCore.Contents.TagHelpers` 添加到 `_ViewImports.cshtml` 文件中，以加载此标记助手。 确保您的项目文件还引用了 OrchardCore.Contents.TagHelpers。

=== "Razor"

    ``` html
    <contentitem alias="alias:main-menu" display-type="Detail" />
    ```

=== "Liquid"

    ``` liquid
    {% contentitem alias: "alias:main-menu", display_type: "Detail" %}
    ```

#### 操作形状元数据

可以使用 `metadata` 标记助手作为形状标记助手的子级来操作形状的元数据。 元数据标记助手允许您：

- 更改显示类型
- 添加、删除或清除备用
- 添加、删除或清除包装器

元数据标记助手示例：

```xml
<menu alias="alias:main-menu">
    <metadata display-type="summary">
        <clear-alternates />
        <add-alternate name="Menu_Alternate1" />
        <add-alternate name="Menu_Alternate2" />
        <remove-alternate name="Menu_Alternate1" />
        <clear-wrappers />
        <add-wrapper name="Menu_Wrapper1" />
        <add-wrapper name="Menu_Wrapper2" />
        <remove-wrapper name="Menu_Wrapper2" />
    </metadata>
</menu>
```

### 日期时间形状

#### `DateTime`

使用请求的时区呈现 `Date` 和 `Time` 值。

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Utc` | `DateTime?` | 要呈现的日期和时间。 如果未指定，将使用当前时间。 |
| `Format` | `string` | .NET 格式字符串。 如果未指定，则使用长格式 `dddd，MMMM d，yyyy h:mm:ss tt`。 可以在 <https://msdn.microsoft.com/en-us/library/8kb3ddd4(v=vs.110).aspx> 中找到接受的格式。 |

标记助手示例：

```html
<datetime utc="@contentItem.CreatedUtc" />
```

#### `TimeSpan`

呈现 `Date` 和 `Time` 间隔的相对文本表示形式。

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Utc` | `DateTime?` | 初始日期和时间。 如果未指定，将使用当前时间。 |
| `Origin` | `DateTime?` | 当前日期和时间。 如果未指定，将使用当前时间。 |

标记助手示例：

```html
<timespan utc="@contentItem.CreatedUtc" />
```

结果：

```text
3 天前
```

## 编辑器形状放置

编辑器形状支持分组放置，这允许您分组编辑器形状，以创建各种内容编辑器布局。

### 支持的分组

- 选项卡
- 卡片
- 列

每个分组都可以单独工作，也可以是渐进的，因此选项卡可以支持卡片和/或列，卡片可以支持列。

通过应用修饰符和组名来创建分组。

### 修饰符

- 选项卡修饰符为 `#`
- 卡片修饰符为 `%`
- 列修饰符为 `|`

每个修饰符都支持组的位置修饰符，格式为 `;`，列支持列宽度的附加修饰符，为 `_`。

要应用位置修饰符或列宽度修饰符，请将适当的值应用于每个组名。

没有分组的字段或部分将在其他字段应用分组时落入默认的 `Content` 组中。

### 示例

在以下示例中，我们将 `MediaField_Edit` 形状放置在名为 `Media` 的选项卡中，并将 `Media` 选项卡放在第一位，将 `Content` 选项卡放在第二位。

``` json

{
    "MediaField_Edit" : [
        {
            "place" : "Parts:0#Media;0",
            "contentType": [
                "Article"
            ]
        }
    ],
    "HtmlField_Edit" : [
        {
            "place": "Parts:0#Content;1",
            "contentType": [
                "Article"
            ]
        }

    ]
}
```

在以下示例中，我们将 `MediaField_Edit` 形状放置在名为 `Media` 的卡片中，并将 `Media` 卡片放在第一位，将 `Content` 卡片放在第二位。

``` json

{
    "MediaField_Edit" : [
        {
            "place" : "Parts:0%Media;0",
            "contentType": [
                "Article"
            ]
        }
    ],
    "HtmlField_Edit" : [
        {
            "place": "Parts:0%Content;1",
            "contentType": [
                "Article"
            ]
        }

    ]
}
```

在以下示例中，我们将 `MediaField_Edit` 形状放置在名为 `Media` 的列中，并将 `Media` 列放在第一位，将 `Content` 列放在第二位。
我们还指定 `Content` 列将占用默认 12 列网格的 9 列。

``` json

{
    "MediaField_Edit" : [
        {
            "place" : "Parts:0|Media;0",
            "contentType": [
                "Article"
            ]
        }
    ],
    "HtmlField_Edit" : [
        {
            "place": "Parts:0|Content_9;1",
            "contentType": [
                "Article"
            ]
        }

    ]
}
```

!!! 注意
    默认情况下，列将在 `md` 断点处响应式断开，并将修饰符解析为 `col-md-9`。
    如果要更改断点，则还可以指定 `Content_lg-9`，它将解析为 `col-lg-9`。
    
### 动态部分放置

在以下示例中，我们将动态部分（没有驱动程序的部分，即在 json 中创建的部分） `GalleryPart` 放置在名为 `MyGalleryZone` 的区域中。 在显示该部分内的内容模板时，我们将执行：

=== Content-Product.Detail.html
``` html
@await DisplayAsync(Model.MyGalleryZone)
```

动态部分使用带有 Part 名称的不同分化器进行详细显示，因此放置文件如下所示：
``` json
{
  "ContentPart": [
    {
      "place": "MyGalleryZone",
      "differentiator": "GalleryPart"
    }
  ]
}
```

此设置将在调用 `DisplayAsync` 的位置显示您的模板（例如 `GalleryPart.cshtml` 或 `GalleryPart.Detail.cshtml`）。

如果我们想在摘要显示内容模板（或任何其他不是 `Detail` 显示类型的模板）中显示相同的部分：

=== Content-Product.Summary.html
``` html
@await DisplayAsync(Model.MyGalleryZone)
```

我们的放置将如下所示（请注意，ContentPart 名称的后缀为 `_Summary`；相应更改您的后缀）：

``` json
{
  "ContentPart_Summary": [
    {
      "place": "MyGalleryZone",
      "differentiator": "GalleryPart"
    }
  ]
}
```

此设置将在调用 `DisplayAsync` 的位置显示您的模板（例如 `GalleryPart.cshtml` 或 `GalleryPart.Summary.cshtml`）。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/h0lZMQkUApo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


> 该文档由ChatGPT 4 翻译
