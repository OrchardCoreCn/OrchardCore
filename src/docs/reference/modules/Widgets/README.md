# Widgets (`OrchardCore.Widgets`)

Widgets是特定类别（刻板印象）的内容项，可以在页面的自定义位置中呈现。Widgets模块提供了一个“Widget”刻板印象和一些模板来呈现它。Widgets由需要呈现专业内容片段的不同模块使用，例如图层或表单。

## 创建自定义小部件

可以创建许多类型的小部件，并且默认配方确实创建了一些自定义小部件，例如“段落”，“块引用”和“MediaWidget”，您可以将其用作示例。

小部件是内容项，因此它们可以由内容字段和内容部分组成。例如，包含在“TheBlogTheme”配方中的“段落”小部件由带有所见即所得编辑器的HTML字段组成。

然后可以在站点的生命周期内从管理UI中组合小部件，从迁移文件中将它们包含为自定义模块的一部分，或者在设置站点时从配方文件中将它们包含在内。创建小部件的唯一要求是使用“Widget”刻板印象标记内容类型。通过这样做，查找小部件的不同服务将相应地处理此内容项。这是“图层”模块或页面编辑器显示可用小部件类型列表的方式。

## 主题

因为“Widget”是一个刻板印象，所以所有小部件内容项都从名为“Widget”的主要形状呈现。此主要形状的模板可以访问这些属性：

| 属性 | 描述 |
| --------- | ------------ |
| `Model.ContentItem` | 小部件内容项。 |
| `Model.Content` | 要显示的内部形状列表。它由小部件组成的所有字段和部分填充。 |

它还包含这些特定区域，大多数情况下不使用，可以在创建网站前端的自定义模板时忽略它们。

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Header` | 要呈现在小部件标题中的形状。 |
| `Model.Meta` | 要呈现在小部件元数据区域中的形状。 |
| `Model.Footer` | 要呈现在小部件页脚中的形状。 |

该形状还包含所有形状通用的属性，例如“Classes”，“Id”和“Attributes”。

### 自定义“Widget”模板

“Widget”形状用于呈现小部件。默认模板将呈现以下内容：

```html
<div class="widget-container">
    <div class="widget-container-title">
        <h2>A Paragraph</h2>
    </div>
    <div class="widget widget-html-widget">
        <div class="widget-body">
            <p>This is a paragraph</p>
        </div>
    </div>
</div>
```

如果HTML包含`<div class="widget-container">`，则您的小部件已由“图层”模块呈现，它将自动添加此内容，因为它需要能够呈现标题，并将其用作标题和小部件的实际内容的容器。

“Widget”形状的实际模板可以在`src/OrchardCore.Modules/OrchardCore.Widgets/Views/Widgets.cshtml`中找到，但可以简化为：

=== “Liquid”

    ``` liquid
    <div class="{{ Model.Classes | join " "}}">
        <div class="widget-body">
        {{ Model.Content | shape_render }}
        </div>
    </div>
    ```

=== “Razor”

    ``` html
    <div class="@String.Join(" ", Model.Classes.ToArray())">
        <div class="widget-body">
        @await DisplayAsync(Model.Content)
        </div>
    </div>
    ```

每个内容类型都有可用的备用。

| 定义 | 模板 | 文件名|
| ---------- | --------- | ------------ |
| `Widget__[ContentType]` | `Widget__Paragraph` | `Widget-Paragraph.cshtml` |

### 自定义“Widget_Wrapper”模板

如前一节所述，“图层”模块使用模板来包装它呈现的小部件并插入每个小部件的自定义标题。

此包装器形状的实际模板可以在`src/OrchardCore.Modules/OrchardCore.Layers/Views/Widget.Wrapper.cshtml`中找到。

常见要求是删除这些标记，可以通过创建此模板来完成：

=== “liquid”

    ``` liquid
    {{ Model.Content | shape_render }}
    ```

=== “Razor”

    ``` html
    @await DisplayAsync(Model.Content)
    ```

可以选择更改这些备用项：

| 定义 | 模板 | 文件名|
| ---------- | --------- | ------------ |
| `Widget_Wrapper__[ContentType]` | `Widget_Wrapper__Paragraph` | `Widget-Paragraph.Wrapper.cshtml` |
| `Widget_Wrapper__Zone__[ContentZone]` | `Widget_Wrapper__Zone__Footer` | `Widget-Zone-Footer.Wrapper.cshtml` |

> 该文档由ChatGPT 4 翻译
