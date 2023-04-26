# Markdown (`OrchardCore.Markdown`)

## 主题

### Shapes

当将`MarkdownBodyPart`附加到内容类型时，将呈现以下形状：

| 名称 | 显示类型 | 默认位置 | 模型类型 |
| ------| ------------ |----------------- | ---------- |
| `MarkdownBodyPart` | `Detail` | `Content:5` | `MarkdownBodyPartViewModel` |
| `MarkdownBodyPart` | `Summary` | `Content:10` | `MarkdownBodyPartViewModel` |

### `BodyPartViewModel`

`MarkdownBodyPartViewModel`类上可用以下属性。

| 属性 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Markdown` | `string` | 所有令牌处理后的Markdown值。 |
| `Html` | `string` | Markdown源生成的HTML内容。 |
| `ContentItem` | `ContentItem` | 部分的内容项。 |
| `MarkdownBodyPart` | `MarkdownBodyPart` | `MarkdownBodyPart`实例。 |
| `TypePartSettings` | `MarkdownBodyPartSettings` | 部分的设置。 |

### `MarkdownBodyPart`

`MarkdownBodyPart`上可用以下属性：

| 名称 | 类型 | 描述 |
| -----| ---- |------------ |
| `Markdown` | Markdown内容。它可以包含Liquid标记，因此直接使用它可能会导致意外结果。最好使用`MarkdownBodyPart`形状呈现。 |
| `Content` | 部分的原始内容。 |
| `ContentItem` | 包含此部分的内容项。 |

### `MarkdownField`

当将`MarkdownField`附加到内容部分时，将呈现此形状。
形状基类的类型为`MarkdownFieldViewModel`。

`MarkdownFieldViewModel`类上可用以下属性。

| 属性 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Markdown` | `string` | 所有令牌处理后的Markdown值。 |
| `Html` | `string` | Markdown源生成的HTML内容。 |
| `Field` | `MarkdownField` | `MarkdownField`实例。 |
| `Part` | `ContentPart` | 附加到此字段的部分。 |
| `PartFieldDefinition` | `ContentPartFieldDefinition` | 部分字段定义。 |

## 消毒

在使用显示管理呈现内容期间，Markdown输出会进行消毒。

您可以通过取消选中`Sanitize HTML`设置或进一步配置[HTML Sanitizer]（../../core/Sanitizer/README.md）来禁用此功能。

在直接呈现内容时，可以通过将布尔值传递给助手来禁用消毒。

## 编辑器

__Markdown Part__编辑器可以针对每个内容类型不同。在__Markdown Part__设置中的内容类型中，只需选择需要使用的内容类型即可。

有两个预定义的编辑器名称：

- `Default`是默认使用的编辑器。
- `Wysiwyg`是提供WYSIWYG体验的编辑器。

### 自定义编辑器

自定义编辑器可以意味着使用不同的预定义体验替换预定义的体验，或者为用户提供新的选项。

要创建新的自定义编辑器，需要提供两个形状模板，一个用于提供编辑器名称（如果要覆盖现有名称，则为可选），另一个用于呈现编辑器的实际HTML。

#### 声明

要声明新编辑器，请创建名为`Markdown_Option__{Name}`的形状，其中`{Name}`是您选择的值。这将由名为`Markdown-{Name}.Option.cshtml`的文件表示。

示例内容：

```csharp
@{
    string currentEditor = Model.Editor;
}
<option value="Wysiwyg" selected="@(currentEditor == "Wysiwyg")">@T["Wysiwyg editor"]</option>
```

#### HTML编辑器

要定义在从设置中选择编辑器时呈现的HTML，请创建名为`Markdown_Edit__{Name}`的形状，对应于文件`Markdown-{Name}.Edit.cshtml`。

示例内容：

```csharp
@using OrchardCore.Markdown.ViewModels
@model MarkdownBodyPartViewModel

<fieldset class="mb-3">
    <label asp-for="Markdown">@T["Markdown"]</label>
    <textarea asp-for="Markdown" rows="5" class="form-control"></textarea>
    <span class="hint">@T["The markdown of the content item."]</span>
</fieldset>
```

### 覆盖预定义的编辑器

您可以通过创建名为`Markdown.Edit.cshtml`的形状文件来覆盖`Default`编辑器的HTML编辑器。使用文件定义WYSIWYG编辑器
`Markdown-Wysiwyg.Edit.cshtml`。

## Razor助手

要在Razor中将Markdown字符串呈现为HTML，请使用视图的基本`Orchard`属性上的`MarkdownToHtmlAsync`助手扩展方法，例如：

```csharp
@await Orchard.MarkdownToHtmlAsync((string)Model.ContentItem.Content.MarkdownParagraph.Content.Markdown)
```

在此示例中，我们假设`Model.ContentItem.Content.MarkdownParagraph.Content`表示`MarkdownField`，`Markdown`是字段值，并将其强制转换为字符串，因为扩展方法不支持动态调度。

此助手还将解析Markdown中包含的任何Liquid。

默认情况下，此助手还将消毒Markdown。

要禁用消毒：

```csharp
@await Orchard.MarkdownToHtmlAsync((string)Model.ContentItem.Content.MarkdownParagraph.Content.Markdown, false)
```

## Markdown配置

默认情况下使用以下配置值，并且可以自定义：

```json
    "OrchardCore_Markdown": {
      "Extensions": "nohtml+advanced"
    }
```

支持的扩展描述如下：

| 扩展 | 描述 |
| --- | --- |
| `advanced` | 启用高级Markdown扩展 |
| `pipetables` | 添加管道表 |
| `gfm-pipetables` | 添加使用标题的管道表以获取列数 |
| `hardlinebreak` | 使用软换行符作为硬换行符 |
| `footnotes` | 允许脚注 |
| `footers` | 添加页脚块 |
| `citations` | 添加引用 |
| `attributes` | 允许附加HTML属性 |
| `gridtables` | 添加网格表 |
| `abbreviations` | 在文档级别存储缩写对象 |
| `emojis` | 支持表情符号和笑脸 |
| `definitionlists` | 添加定义列表 |
| `customcontainers` | 添加块自定义容器 |
| `figures` | 添加图形 |
| `mathematics` | 启用数学符号 |
| `bootstrap` | 启用引导程序类 |
| `medialinks` | 扩展图像Markdown链接，以防链接到视频或音频文件，并输出适当的链接 |
| `smartypants` | 使用SmartyPants |
| `autoidentifiers` | 使用自动标识符 |
| `tasklists` | 添加任务列表 |
| `diagrams` | 允许图表 |
| `nofollowlinks` | 将rel = nofollow添加到呈现为HTML的所有链接 |
| `noopenerlinks` |  |
| `noreferrerlinks` | 将rel = nofollow添加到呈现为HTML的所有链接 |
| `nohtml` | 禁用HTML支持 |
| `yaml` | 将YAML格式解析为MarkdownDocument |
| `nonascii-noescape` | 禁用非美国ASCII字符的URI转义 |
| `autolinks` | 启用文本`http：//`，`https：//`，`ftp：//`，`mailto：`，`www.xxx.yyy`的自动链接 |
| `globalization` | 通过添加适当的html属性添加对从右到左的内容的支持 |

## Markdown管道

在服务注册期间使用`IOptions<MarkdownPipelineOptions>`配置Markdown管道，使用配置扩展方法`ConfigureMarkdownPipeline`可配置管道。

默认情况下，管道启用一些Markdown高级功能，并通过将在Markdown内容中找到的任何HTML转换为转义的HTML实体来禁用HTML。

您可以在启动管道期间多次调用此扩展方法以更改配置。

要清除此配置：

```
services
    .AddOrchardCms()
    .ConfigureServices(tenantServices =>
        tenantServices.PostConfigure<MarkdownPipelineOptions>(o =>
            {
                o.Configure.Clear();
            }));
```

要包括其他`MarkdownPipelineOptions`，例如表情符号和笑脸，我们可以使用：

```
services
    .AddOrchardCms()
    .ConfigureServices(tenantServices =>
        tenantServices.ConfigureMarkdownPipeline((pipeline) => 
        { 
            pipeline.UseEmojiAndSmiley();
        }));
```