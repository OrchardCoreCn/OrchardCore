# 列表 (`OrchardCore.Lists`)

ListPart 允许您将内容项与父容器关联（例如，博客包含博客文章列表）。

## 主题

### Shapes

当 `ListPart` 附加到内容项时，可以为其设置以下形状。

| 名称 | 显示类型 | 默认位置 | 模型类型 |
| ------| ------------ |----------------- | ---------- |
| `ListPart` | `Detail`, `DetailAdmin` | `Content:10` | `ListPartViewModel` |

### `ListPartViewModel`

`ListPartViewModel` 类上可用以下属性。

| 属性 | 类型 | 描述 |
| --------- | ---- |------------ |
| `ListPart` | `ListPart` | `ListPart` 实例。 |
| `ContentItems` | `IEnumerable<ContentItem>` | 部分由其组成的内容项。 |
| `ContainedContentTypeDefinitions` | `IEnumerable<ContentTypeDefinition>` | 列表接受的内容类型。 |
| `Context` | `BuildPartDisplayContext` | 当前显示上下文。 |
| `Pager` | `dynamic` | 列表的分页器。 |

### `ListPart`

`ListPart` 类上可用以下属性。

| 名称 | 类型 | 描述 |
| -----| ---- |------------ |
| `Content` | 部分的原始内容。 |
| `ContentItem` | 包含此部分的内容项。 |

### `ListPartSettings`

`ListPartSettings` 类上可用以下属性。

| 名称 | 类型 | 描述 |
| -----| ---- |------------ |
| `PageSize` | 每页返回的内容项数。 |
| `EnableOrdering` | 启用拖放排序内容项的标志。 |
| `ContainedContentTypes` | 可包含此部分的内容类型。 |

### 模板

以下示例用于呈现 `ListPart` 的项目并自定义分页器。
例如，它可以在名为 `Blog-ListPart.liquid` 的文件中设置，以仅覆盖 `Blog` 内容类型。

```liquid
{% for item in Model.ContentItems %}
    {{ item | shape_build_display: "Summary" | shape_render }}
{% endfor %}

{% assign previousText = "Newer Posts" | t %}
{% assign nextText = "Older Posts" | t %}
{% assign previousClass = "previous" | t %}
{% assign nextClass = "next" | t %}

{% shape_pager Model.Pager previous_text: previousText, next_text: nextText,
    previous_class: previousClass, next_class: nextClass %}

{{ Model.Pager | shape_render }}
```

## Orchard Helpers

### QueryListItemsCountAsync

返回满足给定谓词的 `IEnumerable<ContentItem>` 的计数。

### QueryListItemsAsync

返回满足给定谓词的 `IEnumerable<ContentItem>`。

## Liquid 标签

### list_count

`list_count` 过滤器计算给定 `ContentItem` 对象或以字符串形式给定的显式 `ContentItem` id 的列表中发布的内容项的数量。

### list_items

`list_items` 过滤器为给定的 `ContentItem` 对象或以字符串形式给定的显式 `ContentItem` id 的列表加载发布的内容项。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/a3yyR27vdQQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Lucene 索引

**重大变更**：包含在列表中的每个内容项都有一个关联的 `ContainedPart`。
Orchard Core 1.1 之前 Lucene 中的索引数据为：

`"Content.ContentItem.ListContentItemId"`

在 1.1 之后，它已更改为包括显示顺序，因此现在是：

`"Content.ContentItem.ContainedPart.ListContentItemId"`   
和  
`"Content.ContentItem.ContainedPart.Order"`
> 该文档由ChatGPT 4 翻译
