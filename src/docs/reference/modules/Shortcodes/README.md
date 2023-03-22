# 短代码 (`OrchardCore.Shortcodes`)

添加短代码功能。

短代码是一小段代码，用方括号括起来，可以为内容编辑器添加一些行为，例如嵌入媒体文件。

可以通过启用短代码模板功能或通过代码实现短代码。

## 短代码模板

使用 [Liquid](../Liquid/) 创建带有短代码模板的短代码通过 _Design -> Shortcodes_ 菜单创建。

短代码模板旨在能够覆盖同名的基于代码的短代码。

| 参数 | 描述 |
| --------- | ----------- |
| `Name` | 您的短代码名称，不带方括号。 |
| `Hint` | 要显示的短代码提示。 |
| `Usage` | 用于描述短代码的用法和参数的 html 字符串。 |
| `Categories` | 您的短代码所属的类别。 |
| `Return Shortcode` | 选择短代码时从短代码选择器返回的短代码值。默认为名称。 |
| `Content` | 您的短代码的 Liquid 模板。 |

### 模板参数

| 参数 | 描述 |
| --------- | ----------- |
| `Args` | 如果有，则提供给用户的参数。 |
| `Content` | 如果有，则由用户提供的内部内容。
| `Context` | 调用者提供给短代码的上下文，例如 `HtmlBodyPart`。 |

### 示例短代码模板：

#### `[display_text]`

| 参数 | 值 |
| --------- | ----------- |
| `Name` | display_text |
| `Hint` | 返回内容项的显示文本。 |
| `Usage` | [display_text] |
| `Content` | `{{ Context.ContentItem.DisplayText }}`<br>`{{ More }}` |

!!! 注意
    当调用者（即 `HtmlBodyPart`）将 `ContentItem` 值传递给 `Context` 时，才能使用 `ContentItem` `Context`。

#### `[site_name]`

| 参数 | 值 |
| --------- | ----------- |
| `Name` | site_name |
| `Hint` | 返回站点名称。 |
| `Usage` | [site_name] |
| `Content` | `{{ Site.SiteName }}` |

#### `[primary]`

| 参数 | 值 |
| --------- | ----------- |
| `Name` | primary |
| `Hint` | 以主题的主要颜色格式化文本。 |
| `Usage` | [primary text]&lt;br&lt;[primary]text[/primary] |
| `Content` | `{% capture output %}`<br>`{% if Args.Text != nil %}`<br>`<span class="text-primary">{{Args.Text}}</span>`<br>`{% else %}`<br>`<span class="text-primary">{{Content}}</span>`<br>`{% endif %}`<br>`{% endcapture %}`<br>`{{ output | sanitize | raw }}` |

## 通过代码的短代码

### 短代码委托

可以使用 `AddShortcode` 扩展方法通过 `ShortcodeDelegate` 在代码中注册短代码。

| 参数 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Name` | `string` | 短代码的名称。 |
| `Shortcode` | `ShortcodeDelegate` | 短代码委托。 |
| `Describe` | `Action<ShortcodeOption>` | 可选的短代码描述。 |

在此示例中，我们注册了一个 `[bold]` 短代码委托并描述了短代码。

``` csharp
services.AddShortcode("bold", (args, content, ctx) => {
    var text = args.NamedOrDefault("text");
    if (!String.IsNullOrEmpty(text))
    {
        content = text;
    }

    return new ValueTask<string>($"<b>{content}</b>");
}, describe => {
    describe.DefaultValue = "[bold text-here]";
    describe.Hint = "Add bold formatting with a shortcode.";
    describe.Usage = "[bold 'your bold content here']";
    describe.Categories = new string[] { "HTML Content" };
    };
});
```

### `IShortcodeProvider`

还可以通过实现 `IShortcodeProvider` 接口并使用 `AddShortcode` 扩展方法注册短代码。

在此示例中，我们将 `ImageShortcodeProvider` 注册为 `[image]` 并描述短代码。

``` csharp
services.AddShortcode<ImageShortcodeProvider>("image", describe => {
    describe.DefaultValue = "[image] [/image]";
    describe.Hint = "Add a image from the media library.";
    describe.Usage = 
@"[image]foo.jpg[/image]<br>
<table>
  <tr>
    <td>Args:</td>
    <td>width, height, mode</td>
  </tr>
  <tr>
    <td></td>
    <td>class, alt</td>
  </tr>
</table>"; 
    describe.Categories = new string[] { "HTML Content", "Media" };
    };
});
```

!!! 注意
    从版本 `1.0.0-rc2-13450` 升级时，您可能需要重新启用短代码功能，通过 _Configuration -> Features_

    短代码模板功能仅在 [Preview Feed](../../../getting-started/preview-package-source) 中可用

## 可用的短代码

### `[image]`

[image] 短代码从站点的媒体库中呈现图像。

示例
```
[image alt="My lovely image"]my-image.jpg[/image]
```
这将为站点媒体文件夹中的文件 `my-image.jpg` 渲染图像标记。

可以使用以下参数：

- **alt：** 为无法看到图像的读者添加替代文本，并且对于 SEO 也很好。
- **class：** 为图像标记添加 html class 属性以进行样式设置。
- **format：** 更改文件格式以从原始文件开始。可以是 jpeg、png、gif 或 bmp。
- **quality：** 设置用于 jpeg 图像的编码质量。质量越高，文件大小越大。该值可以从 0 到 100，并默认为 75。
- **width、height：** 可以设置宽度和高度以调整图像大小。可能的值受到限制，以防止恶意客户端创建太多相同图像的变体。值可以是 16、32、50、100、160、240、480、600、1024、2048。
- **mode：** 调整模式控制如何调整图像。  
   选项为：
  - **pad：** 将调整大小的图像填充到其容器的边界以适合。如果只传递一个维度，则将保持原始纵横比。
  - **boxpad：** 将图像填充到容器的边界，而不调整原始源。当缩小时，执行与 pad 相同的功能。
  - **max**（默认）：将调整大小的图像限制为适合其容器的边界，保持原始纵横比。
  - **min：** 调整图像大小，直到最短边达到给定尺寸。在此模式下禁用放大，并且如果尝试，则将返回原始图像。
  - **stretch：** 将调整大小的图像拉伸到适合其容器的边界。
  - **crop：** 使用与 max 相同的功能调整图像大小，然后删除任何落在其容器边界之外的图像区域。

### `[asset_url]`

[asset_url] 短代码从站点的媒体库返回相对 URL。

示例
```
[asset_url]my-image.jpg[/asset_url]
```
这将返回文件 `my-image.jpg` 在站点媒体文件夹中的相对 URL `/my-tenant/media/my-image.jpg`。

可以使用以下参数：

- **format：** 更改文件格式以从原始文件开始。可以是 jpeg、png、gif 或 bmp。
- **quality：** 设置用于 jpeg 图像的编码质量。质量越高，文件大小越大。该值可以从 0 到 100，并默认为 75。
- **width、height：** 可以设置宽度和高度以调整图像大小。可能的值受到限制，以防止恶意客户端创建太多相同图像的变体。值可以是 16、32、50、100、160、240、480、600、1024、2048。
- **mode：** 调整模式控制如何调整图像。  
   选项为：
  - **pad：** 将调整大小的图像填充到其容器的边界以适合。如果只传递一个维度，则将保持原始纵横比。
  - **boxpad：** 将图像填充到容器的边界，而不调整原始源。当缩小时，执行与 pad 相同的功能。
  - **max**（默认）：将调整大小的图像限制为适合其容器的边界，保持原始纵横比。
  - **min：** 调整图像大小，直到最短边达到给定尺寸。在此模式下禁用放大，并且如果尝试，则将返回原始图像。
  - **stretch：** 将调整大小的图像拉伸到适合其容器的边界。
  - **crop：** 使用与 max 相同的功能调整图像大小，然后删除任何落在其容器边界之外的图像区域。

### `[locale]`

`locale` 短代码在指定的语言中有条件地呈现内容。输出基于当前线程文化。

仅当启用了 `OrchardCore.Localization` 模块时，才可用此短代码。

示例
```
[locale en]English Text[/locale][locale fr]French Text[/locale]
```

默认情况下，如果当前区域设置是指定语言的父级，则短代码将呈现内容。 
例如，如果当前区域设置为 `en-CA`，并且您指定了此短代码：`[locale en]English Text[/locale]` 输出将为 `English Text`。
您可以通过将短代码的第二个参数传递为 `false` 来禁用此行为。 
`[locale en false]English Text[/locale]` 如果当前文化不是 `en`，则不会呈现任何内容。

## 渲染短代码

当使用支持短代码的显示驱动程序生成的 `Shape` 时，会自动呈现短代码。

- `HtmlBodyPart`
- `HtmlField`
- `MarkdownBodyPart`
- `MarkdownField`

=== "Liquid"

    ``` liquid
    {{ Model.Content.HtmlBodyPart | shape_render }}
    ```

=== "Razor"

    ``` html
    @await DisplayAsync(Model.Content.HtmlBodyPart)
    ```

短代码也可以通过Liquid过滤器或 html 帮助程序呈现

=== "Liquid"

    ``` liquid
    {{ Model.ContentItem.Content.RawHtml.Content.Html | shortcode | raw }}
    ```

=== "Razor"

    ``` html
    @Html.Raw(@await Orchard.ShortcodesToHtmlAsync((string)Model.ContentItem.Content.RawHtml.Content.Html))
    ```

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/hsTJSIxUmZo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/ofPKGsW5Ftg" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
