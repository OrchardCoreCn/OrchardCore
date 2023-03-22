# 标题 (`OrchardCore.Title`)

`Title` 模块提供了一个 **标题部分**，让您可以自定义内容项的 DisplayText 属性。DisplayText 属性在整个管理界面中用于帮助您识别您的内容项。

## TitlePart

将此部分附加到您的内容项上，以自定义 ContentItem 的 DisplayText 属性。

### TitlePart 设置

默认情况下，附加 TitlePart 将允许内容编辑器手动编辑 ContentItem 的 DisplayText（标题）。

您还可以通过使用 Liquid 表达式指定模式来生成标题。

Pattern 可以访问当前 ContentItem，并在 ContentItem 更新时执行。例如，可以使用字段来生成模式。以下示例使用名为 `Name` 的 __Text 字段__，在 `Product` 内容类型上。

```liquid
{{ ContentItem.Content.Product.Name.Text }}
```

## 主题

当 **Title Part** 附加到内容类型时，将呈现以下形状。

| 名称        | 显示类型 | 默认位置 | 模型类型           |
| ----------- | ------------ | ---------------- | -------------------- |
| `TitlePart` | `Detail`     | `Header:5`       | `TitlePartViewModel` |
| `TitlePart` | `Summary`    | `Header:10`      | `TitlePartViewModel` |

### 视图模型

`TitlePartViewModel` 类中提供了以下属性。

| 名称        | 类型        | 描述                     |
| ----------- | ----------- | ------------------------------- |
| `Title`     | `string`    | 部分的标题属性。 |
| `TitlePart` | `TitlePart` | `TitlePart` 实例。       |
> 该文档由ChatGPT 4 翻译
