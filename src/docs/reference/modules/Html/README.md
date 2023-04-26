# Body (`OrchardCore.Html`)

## 主题

### Shapes

当将`HtmlBodyPart`附加到内容类型时，将呈现以下形状：

| 名称 | 显示类型 | 默认位置 | 模型类型 |
| ------| ------------ |----------------- | ---------- |
| `HtmlBodyPart` | `Detail` | `Content:5` | `HtmlBodyPartViewModel` |
| `HtmlBodyPart` | `Summary` | `Content:10` | `HtmlBodyPartViewModel` |

### HtmlBodyPartViewModel

`HtmlBodyPartViewModel`类上可用以下属性：

| 属性 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Body` | `string` | 已编辑的内容。它可能包含令牌。 |
| `Html` | `string` | 处理所有令牌后的HTML内容。 |
| `ContentItem` | `ContentItem` | 部分的内容项。 |
| `HtmlBodyPart` | `HtmlBodyPart` | `HtmlBodyPart`实例。 |
| `TypePartSettings` | `HtmlBodyPartSettings` | 部分的设置。 |

### HtmlBodyPart

`HtmlBodyPart`上可用以下属性：

| 名称 | 类型 | 描述 |
| -----| ---- |------------ |
| `Body` | `string` | 正文中的HTML内容。它可以包含Liquid标记，因此直接使用它可能会导致意外结果。最好渲染`HtmlBodyPart`形状。 |
| `Content` | 部分的原始内容。 |
| `ContentItem` | 包含此部分的内容项。 |

## 消毒

默认情况下，保存`HtmlBodyPart`时会对所有HTML输入进行消毒。

您可以通过取消选中`Sanitize HTML`设置或进一步配置[HTML Sanitizer](../../core/Sanitizer/README.md)来禁用此功能。

## 编辑器

__HtmlBody Part__编辑器可以针对每个内容类型不同。在内容类型的__HtmlBody Part__设置中，只需选择要使用的编辑器即可。

有三个预定义的编辑器名称：

- `Default`是默认使用的编辑器。
- `Wysiwyg`是提供所见即所得体验的编辑器。
- `Monaco`是提供源代码体验的编辑器。

#### 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/lnjdRildsL8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 自定义编辑器

自定义编辑器可以意味着使用不同的体验替换预定义的编辑器，或者为用户提供新的选择选项。

要创建新的自定义编辑器，需要提供两个形状模板，一个用于提供编辑器名称（如果要覆盖现有名称，则为可选项），另一个用于呈现编辑器的实际HTML的形状。

#### 声明

要声明新编辑器，请创建名为`HtmlBodyPart_Option__{Name}`的形状，其中`{Name}`是您选择的值。这将由名为`HtmlBodyPart-{Name}.Option.cshtml`的文件表示。

示例内容：

```csharp
@{
    string currentEditor = Model.Editor;
}
<option value="Wysiwyg" selected="@(currentEditor == "Wysiwyg")">@T["Wysiwyg editor"]</option>
```

#### HTML编辑器

要定义在从设置中选择编辑器时呈现的HTML，可以创建一个名为`HtmlBodyPart_Edit__{Name}`的形状，对应于文件`HtmlBodyPart-{Name}.Edit.cshtml`。

示例内容：

```csharp
@using OrchardCore.Html.ViewModels
@model HtmlBodyPartViewModel

<fieldset class="mb-3">
    <label asp-for="Body">@T["Body"]</label>
    <textarea asp-for="Body" rows="5" class="form-control"></textarea>
    <span class="hint">@T["The body of the content item."]</span>
</fieldset>
```

### 覆盖预定义的编辑器

您可以通过创建名为`HtmlBodyPart.Edit.cshtml`的形状文件来覆盖`Default`编辑器的HTML编辑器。所使用的Wysiwyg编辑器是通过使用名为`HtmlBodyPart-Wysiwyg.Edit.cshtml`的文件定义的。
