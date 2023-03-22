# 内容字段 (`OrchardCore.ContentFields`)

该模块提供了常见的内容字段。  
一些字段在其特定模块中可用。

## 可用字段

| 名称 | 属性 |
| --- | --- |
| `BooleanField` | `bool Value` |
| `ContentPickerField` | `string[] ContentItemIds` |
| `DateField` | `DateTime? Value` |
| `DateTimeField` | `DateTime? Value` |
| `HtmlField` | `string Html` |
| `LinkField` | `string Url, string Text` |
| `LocalizationSetContentPickerField` | `string[] LocalizationSets` |
| `MarkdownField` | `string Markdown` |
| `MediaField` | `string[] Paths` |
| `MultiTextField` | `string[] Values` |
| `NumericField` | `decimal? Value` |
| `GeoPointField` | `decimal Latitude, decimal Longitude` |
| `TaxonomyField` | `string TaxonomyContentItemId, string[] TaxonomyContentItemId` |
| `TextField` | `string Text` |
| `TimeField` | `TimeSpan? Value` |
| `UserPickerField` | `string[] UserIds` |
| `YoutubeField` | `string EmbeddedAddress, string RawAddress` |

!!! note
    每个字段都由相应的“形状类型”呈现，该类型使用自己的显示视图模型。  
    例如：`BooleanField`由名为`BooleanField`的形状类型呈现，该类型具有`DisplayBooleanFieldViewModel`。

## 用法

从“内容”模板中，您可以像这样引用字段的值
（如果内容类型为“Article”，并且具有名为“MyField”的文本字段）：

=== "Liquid"

    ``` liquid
    {{ Model.ContentItem.Content.Article.MyField.Text }}
    ```

=== "Razor"

    ``` html
    var fieldValue = Model.ContentItem.Content.Article.MyField.Text;
    ```

从字段形状（请参见列出所有字段的表中的形状类型）中，您还可以访问特定于每个视图模型的属性。

### 常见字段属性

字段视图模型的约定也是公开这些属性：

| 属性 | 描述 |
| --- | --- |
| `Field` | ContentField。 |
| `Part` | 包含字段的ContentPart。 |
| `PartFieldDefinition` | 包含部分的Content Part Field Definition。这也可以访问内容类型 |

一些视图模型具有从实际字段数据计算的特殊属性，这些属性对于模板更有用。

### `HtmlField`

#### `DisplayHtmlFieldViewModel`

| 属性 | 描述 |
| --- | --- |
| `Html` | 处理后的HTML，一旦处理了所有Liquid标记。 |

#### Html字段示例

``` liquid
{{ Model.Html }}
```

或者，在将标记转换之前显示原始内容：

``` liquid
{{ Model.Field.Html }}
```

### `DateTimeField`

#### `DisplayDateTimeFieldViewModel`

| 属性 | 描述 |
| --- | --- |
| `LocalDateTime` | 站点时区中的日期时间。 |

#### DateTime字段示例

``` liquid
{{ Model.LocalDateTime }}
```

或者，在将其转换之前显示UTC值：

``` liquid
{{ Model.Value }}
```

### `ContentPickerField`

#### ContentPicker字段示例

=== "Liquid"

    ``` liquid
    {% assign contentItems = Model.ContentItemIds | content_item_id %}
    {% for contentItem in contentItems %}
        {{ contentItem.DisplayText }}
    {% endfor %}
    ```

=== "Razor"

    ```html
    @foreach (var contentItem in await Orchard.GetContentItemsByIdAsync(Model.ContentItemIds))
    {
        @contentItem.DisplayText
    }
    ```

或者渲染引用的内容项：

=== "Liquid"

    ``` liquid
    {% assign contentItems = Model.ContentItemIds | content_item_id %}
    {% for contentItem in contentItems %}
        {{ contentItem | shape_build_display: "Detail" | shape_render }}
    {% endfor %}
    ```

=== "Razor"

    ``` html
    @foreach (var contentItem in await Orchard.GetContentItemsByIdAsync(Model.ContentItemIds))
    {
        @await Orchard.DisplayAsync(contentItem, "Detail")
    }
    ```

### `LocalizationSetContentPickerField`

当引用不应指向内容项的特定文化时，此字段允许您存储`ContentItem`的`LocalizationSet`。  
这简化了在前端获取正确文化的内容项。

以下示例使用`localization_set`Liquid过滤器，该过滤器根据请求文化返回每个集合中的单个ContentItem，
如果未指定文化，则返回。

#### LocalizationSet ContentPicker字段示例

=== "Liquid"

    ```liquid
    {% assign contentItems = Model.LocalizationSets | localization_set %}
    {% for contentItem in contentItems %}
        {{ contentItem.DisplayText }}
    {% endfor %}
    ```

=== "Razor"

    ``` html
    @model OrchardCore.ContentFields.ViewModels.DisplayLocalizationSetContentPickerFieldViewModel
    @using Microsoft.AspNetCore.Localization

    @inject OrchardCore.ContentLocalization.IContentLocalizationManager ContentLocalizationManager;

    @{
        var currentCulture = Context.Features.Get<IRequestCultureFeature>().RequestCulture.Culture.Name;
        var contentItems = await ContentLocalizationManager.GetItemsForSetsAsync(Model.LocalizationSets, currentCulture);
    }
    foreach (var contentItem in contentItems)
    {
        <span class="value">@contentItem.DisplayText</span>
        if (contentItem != contentItems.Last())
        {
            <span>,</span>
        }
    }
    ```

### `UserPicker Field`

用户选择器字段允许您将用户与内容项相关联。

将字段添加到内容类型时，请使用设置指定是否要

- 列出所有用户，
- 列出特定角色的用户。


#### UserPicker字段示例

=== "Liquid"

    ```liquid
    {% assign users = Model.UserIds | users_by_id %}
    {% for user in users %}
        {{ user.UserName }} - {{ user.Email }}
    {% endfor %}
    ```

=== "Razor"

    ``` html
    @model OrchardCore.ContentFields.ViewModels.DisplayUserPickerFieldViewModel
    @using OrchardCore.Mvc.Utilities

    @{
        var name = (Model.PartFieldDefinition.PartDefinition.Name + "-" + Model.PartFieldDefinition.Name).HtmlClassify();
        var users = await @Orchard.GetUsersByIdsAsync(Model.UserIds);
    }

    <div class="field field-type-userpickerfield field-name-@name">
        <span class="name">@Model.PartFieldDefinition.DisplayName()</span>
        @if (users.Any())
        {
            foreach (var user in users)
            {
                <span class="value">@user.UserName</span>
                if (user != users.Last())
                {
                    <span>,</span>
                }
            }
        }
        else
        {
            <span class="value">@T["No users."]</span>
        }
    </div>

    ```

#### 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/vqXwK69vtMw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### `MultiText Field`

#### 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/WfP_rXz1id0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 创建自定义字段

### 要扩展什么

在创建新字段之前，解决方案可能是提供自定义编辑器和格式化程序。

字段应表示某些特定的物理数据和逻辑数据。可以使用编辑器和格式化程序自定义相同的字段
以不同的方式进行编辑和呈现。编辑器是可以用于编辑字段的形状，例如WYSIWYG HTML编辑器是HTML的自定义编辑器
字段。格式化程序是可以用于在前端呈现字段的替代形状，例如
“Link”字段可以呈现为Youtube视频播放器。

### 模型类

创建一个从`ContentField`继承的类，该类将表示字段的状态。
其内容将作为内容项的一部分进行序列化。
可以使用Json.NET类来自定义序列化。

例如：

```csharp
public class TextField : ContentField
{
    public string Text { get; set; }
}
```

此类需要像这样在DI中注册：

```csharp
services.AddContentField<TextField>();
```

### Display Driver

显示驱动程序是驱动字段在前端上显示、在管理中编辑、更新和验证的组件。

创建一个从`ContentFieldDisplayDriver<TextField>`继承的类，并通过查看此模块的示例实现三个方法
`Display`、`Edit`和`DisplayAsync`。

此类需要像这样在DI中注册：

```csharp
services.AddContentField<TextField>()
    .UseDisplayDriver<TextFieldDisplayDriver>();
```

这将注册显示驱动程序以供所有显示模式和编辑器使用。

## 创建自定义显示模式

对于每个字段，约定是创建一个可以针对不同显示模式的替代品。要提供
字段的可用编辑器列表中的新选择，请创建一个新的形状模板，该模板与此匹配
`TextField-Header.DisplayOption`
模板：`{FIELDTYPE}_DisplayOption__{DISPLAYMODE}`
此形状类型将匹配名为`{FIELDTYPE}-{DISPLAYMODE}.DisplayOption.cshtml`的模板文件

此模板需要呈现一个`<option>`标记。以下是文本字段上标题显示模式选项的示例：

``` html
@{
    string currentDisplayMode = Model.DisplayMode;
}
<option value="Header" selected="@(currentDisplayMode == "Header")">@T["Header"]</option>
```

然后，您可以通过添加名为`{FIELDTYPE}_Display__{DISPLAYMODE}`的文件来创建显示模式形状，
该文件由模板文件`{FIELDTYPE}-{DISPLAYMODE}.Display.cshtml`表示。

例如，文本字段上标题显示模式的文件名为`TextField-Header.Display.cshtml`。

## 创建自定义编辑器

对于每个字段，约定是创建一个可以针对不同编辑器的替代品。要提供
字段的可用编辑器列表中的新选择，请创建一个新的形状模板，该模板与此匹配
模板：`{FIELDTYPE}_Option__{EDITORNAME}`
此形状类型将匹配名为`{FIELDTYPE}-{EDITORNAME}.Option.cshtml`的模板文件

此模板需要呈现一个`<option>`标记。以下是HTML字段上的Wysiwyg选项的示例：

``` html
@{
    string currentEditor = Model.Editor;
}
<option value="Wysiwyg" selected="@(currentEditor == "Wysiwyg")">@T["Wysiwyg editor"]</option>
```

然后，您可以通过添加名为`{FIELDTYPE}_Edit__{EDITORNAME}`的文件来创建编辑器形状，
该文件由模板文件`{FIELDTYPE}-{EDITORNAME}.Edit.cshtml`表示。

例如，HTML字段上的Wysiwyg编辑器的文件名为`HtmlField-Wysiwyg.Edit.cshtml`。

### 自定义显示驱动程序注册

对于显示模式和编辑器，您还可以自定义将为特定模式解析的显示驱动程序。

这使您可以创建可能返回与标准显示驱动程序不同的ViewModel的自定义显示驱动程序。

在您的模块的`Startup.cs`中更改现有字段类型或驱动程序类型的注册

```csharp
services.AddContentField<TextField>()
    .ForDisplayMode<TextFieldDisplayDriver>(d => String.IsNullOrEmpty(d))
    .ForDisplayMode<MyCustomTextFieldDisplayDriver>(d => d == "MyCustomDisplayMode");
```

此示例将更改`TextFieldDisplayDriver`的注册以仅解析标准（null）显示模式，
并注册`MyCustomTextFieldDisplayDriver`以仅解析自定义显示模式。

```csharp
services.AddContentField<TextField>()
    .ForEditor<TextFieldDisplayDriver>(d => d != "MyCustomEditor")
    .ForEditor<MyCustomTextFieldDisplayDriver>(d => d == "MyCustomEditor");
```

此示例将更改`TextFieldDisplayDriver`的注册以解析除自定义编辑器之外的所有编辑器，
并注册`MyCustomTextFieldDisplayDriver`以仅解析自定义编辑器。

!!! note
    在注册自定义显示模式或编辑器驱动程序时，必须更改现有驱动程序的注册。
    您还应该在您的模块的`Manifest.cs`中取决于字段所在的模块。
    这将使您的模块的`Startup.cs`运行得更晚，并允许您的注册覆盖原始模块。 
```
REWRITTEN_SELECTED_BLOCK:
<|START|>
```D:\SourceCodes\hyzx86\OC\OCCN\OrchardCoreCn\OrchardCore\src\docs\reference\modules\ContentFields\README.md
    ```
    该文档由ChatGPT 4 翻译
    
