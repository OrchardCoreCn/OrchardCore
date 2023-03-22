# 内容 (`OrchardCore.Contents`)

该模块提供内容管理服务。

## Liquid

您可以通过使用 `Content` 属性从Liquid视图和模板中访问内容项。  
默认情况下，您可以通过别名或内容项 ID 检索内容。  
其他模块（例如 `Alias` 和 `Autoroute`）允许您通过其他标识符检索内容。

### 从句柄加载

```liquid
{% assign my_content = Content["alias:main-menu"] %}
```

句柄可以采用各种形式，例如使用 Autoroute 时，带有 `slug` 前缀。

```liquid
{% assign my_content = Content["slug:my-blog/my-blog-post"] %}
```

> 通过实现 `IContentHandleProvider` 提供句柄。

### 加载内容项的最新版本

您可以使用 `Latest` 属性通过别名检索内容项的最新版本（无论是已发布版本还是最新草稿版本）：

```liquid
{% assign my_content = Content.Latest["alias:main-menu"] %}
```

### 从内容项 ID 加载

```liquid
{% assign my_content = Content.ContentItemId["417qsjrgv97e74wvp149h4da53"] %}
```

当可用内容项 ID 列表时，应优先使用 `content_item_id` 过滤器：

```liquid
{% assign posts = postIds | content_item_id %}
```

### 从内容项版本 ID 加载

```liquid
{% assign my_content = Content.ContentItemVersionId["49gq8g6zndfc736x0az3zsp4w3"] %}
```

### 从句柄呈现内容项

```liquid
{% contentitem handle:"alias:test", display_type="Summary" %}
```

当未指定时，默认显示类型为“Detail”。
可以指定可选的 `alternate` 参数。

### 记录到浏览器控制台

`console_log` Liquid过滤器可用于将数据从众所周知的属性或可序列化为 json 的对象转储到浏览器控制台。

```liquid
{{ Model.Content | console_log }}
```

```liquid
{{ Model.ContentItem | console_log }}
```

众所周知的属性包括
- 字符串
- JTokens
- 内容项（来自 `Model.ContentItem` 属性）
- 形状（来自 `Model.Content` 属性）
- 可序列化为 json 的对象。

!!! 注意
    要记录形状，请在调用 `{{ Model.Content | shape_render }}` 后调用 `{{ Model.Content | console_log }}`
    这将允许形状执行，并为任何子形状填充替代项。

## Razor Helper

以下方法可从 Razor 帮助程序中使用。

| 方法 | 参数 | 描述 |
| --------- | ---- |------------ |
| `GetContentItemIdByHandleAsync` | `string name` | 从其句柄返回内容项 ID。例如 `alias:carousel`，`slug:myblog/my-blog-post` |
| `GetContentItemByHandleAsync` | `string handle, bool latest = false` | 从其句柄加载内容项，寻找最新版本或不寻找。 |
| `GetContentItemByIdAsync` | `string contentItemId, bool latest = false` | 从其 ID 加载内容项，寻找最新版本或不寻找。 |
| `GetContentItemsByIdAsync` | `IEnumerable<string> contentItemIds, bool latest = false` | 按 ID 加载内容项列表，寻找最新版本或不寻找。 |
| `GetContentItemByVersionIdAsync` | `string contentItemVersionId` | 从其版本 ID 加载内容项。 |
| `ConsoleLog` | `object content` | 将内容记录到浏览器控制台 |

> 如果视图使用 Orchard Core 的 Razor 基类，则可以在 `Orchard` 属性上访问 Razor Helper，或者在所有其他情况下注入 `OrchardCore.IOrchardHelper`。

### Razor 控制台日志

`ConsoleLog` 扩展方法可用于将数据从众所周知的属性或可序列化为 json 的对象转储到浏览器控制台。

`@Orchard.ConsoleLog(Model.Content as object)` 注意我们将其转换为对象，因为扩展方法不支持动态分派。

`@Orchard.ConsoleLog(Model.ContentItem as object)` 注意我们将其转换为对象，因为扩展方法不支持动态分派。

众所周知的属性包括
- 字符串
- JTokens
- 内容项（来自 `Model.ContentItem` 属性）
- 形状（来自 `Model.Content` 属性）
- 可序列化为 json 的对象。

!!! 注意
    要记录形状，请在调用 `@await DisplayAsync(Model.Content)` 后调用 `@Orchard.ConsoleLog(Model.Content as object)`
    这将允许形状执行，并为任何子形状填充替代项。

## GraphQL

内容模块提供了一个功能，用于为内容项提供 GraphQL 查询。
有关如何发送 GraphQL 查询的更多信息，请参见[此部分](../Apis.GraphQL/README.md)。

### 内容类型查询

您可以使用内容查询来获取某个内容类型的单个内容项或内容项列表。

在此示例中，我们使用 `blogPost` 查询来获取 `BlogPost` 内容项的列表。 在响应中，我们仅包括每个 `BlogPost` 内容项的 `contentItemId` 和 `displayText`：

```graphql
query {
  blogPost {
    contentItemId
    displayText
  }
}
```

我们还可以使用 `blogPost` 查询查询特定的 `BlogPost` 内容项。 请注意，我们使用 `where` 参数来选择内容项：

```graphql
query {
  blosPost(where: {
    contentItemId: "417qsjrgv97e74wvp149h4da53"
  }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

### 可用字段

这些字段在内容项级别可用：

| 属性 |
| -------- |
| `contentItemId` |
| `contentItemVersionId` |
| `contentType` |
| `displayText` |
| `published` |
| `latest` |
| `modifiedUtc` |
| `publishedUtc` |
| `createdUtc` |
| `owner` |
| `author` |

此外，还可以像这样检索所有内容部分：

```graphql
{
  blogPost {
    displayText
    autoroutePart {
      path
    }
  }
}
```

### 查询参数

可以组合不同类型的查询参数来过滤结果：

- 排序：使用 `orderBy` 按任何字段值对内容项进行排序
- 过滤：使用 `where` 选择标量或关系过滤器中的内容项
- 分页：使用 `first` 和 `skip` 切片查询中的内容项

#### 排序

当查询特定内容类型的所有内容项时，可以为每个标量字段的类型提供 `orderBy` 参数：`orderBy: { <field>: ASC }` 或 `orderBy: { <field>: DESC }`。

按 `displayText` 升序排序所有 `BlogPost` 内容项：

```graphql
query {
  blogPost(orderBy: { displayText: ASC }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

按 `publishedUtc` 降序排序所有 `BlogPost` 内容项，然后按 `displayText` 升序排序：

```graphql
query {
  blogPost(orderBy: { publishedUtc: DESC, displayText: ASC  }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

您正在排序的字段不必在实际查询中选择。

目前还无法按其部分或自定义字段对响应进行排序。

#### 过滤

当查询特定内容类型的所有内容项时，可以向 where 参数提供不同的参数以根据您的要求约束响应中的数据。 
可用选项取决于所讨论的类型上定义的标量和部分字段。

##### 单个过滤器

如果向 `where` 参数提供了一个参数，则查询响应将仅包含符合此约束的内容项。  
可以使用 `AND` 和/或 `OR` 组合多个过滤器，请参见下文的[任意组合过滤器与 AND 和 OR](#arbitrary-combination-of-filters-with-and-and-or)以获取更多详细信息。

##### 按发布状态过滤

默认情况下，仅返回已发布的内容项。 您可以选择 `DRAFT`、`LATEST` 或 `ALL` 版本的内容项。

```graphql
query {
  blogPost(status: DRAFT) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

##### 按具体值过滤

过滤查询响应的最简单方法是为某个字段提供具体值以进行过滤。

查询具有特定显示文本的所有 `BlogPost` 内容项：

```graphql
query {
  blogPost(where: {
    displayText: "About"
  }) {
    contentItemId
  }
}
```

##### 高级过滤条件

根据您要过滤的字段类型，您可以访问不同的高级条件来过滤查询响应。

查询所有 `BlogPost` 内容项，其中 `displayText` 在给定字符串列表中：

```graphql
query {
  blogPost(where: {
    displayText_in: ["My biggest Adventure", "My latest Hobbies"]
  }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

查询所有 `BlogPost` 内容项，其中创建日期小于特定日期：

```graphql
query {
  blogPost(where: {
    creationgUtc_lt: "2011-11-13T07:45:00"
  }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

##### 内容部分过滤器

对于内容部分，您可以通过在 `where` 中嵌套相应参数来定义部分上的条件。

查询所有 `BlogPost` 内容项，其中 `autoroutePart` 具有其 `path` 中的特定值：

```graphql
query {
  blogPost(where: {
    autoroutePart {
      path_contains: "/about"
    }
  }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

##### 组合多个过滤器

您可以使用过滤器组合器 `OR`、`AND` 和 `NOT` 来创建任意逻辑组合的过滤条件：

对于 `AND` 过滤器，所有嵌套条件都必须为 `true` 才能评估。
对于 `OR` 过滤器，至少有一个嵌套条件必须为 `true` 才能评估。
对于 `NOT` 过滤器，所有嵌套条件都必须为 `false` 才能评估。
使用 `OR`、`AND` 和 `NOT`

让我们从一个简单的例子开始：

查询所有在 `2018` 中创建的 `BlogPost` 内容项，其中 `displayText` 在给定字符串列表中：

```graphql
query {
  blogPost(where: {
    AND: {
      displayText_in: ["My biggest Adventure", "My latest Hobbies"]
    }, {
      publishedUtc_gt: "2018"
    }
  }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

##### 任意组合过滤器与 AND 和 OR

您可以组合甚至嵌套过滤器组合器 `AND`、`OR` 和 `NOT`，以创建任意逻辑组合的过滤条件。

查询所有在 `2018` 中创建的 `BlogPost` 内容项，其中 `displayText` 在给定字符串列表中，或者具有我们提供的特定 `contentItemId`：

```graphql
query {
  blogPost(where: {
    OR: {
      AND: {
        displayText_in: ["My biggest Adventure", "My latest Hobbies"],
        publishedUtc_gt: "2018"
      },

      contentItemId: "417qsjrgv97e74wvp149h4da53"
    }
  }) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

请注意，我们将 `AND` 组合器嵌套在 `OR` 组合器内。

#### 分页

当查询特定内容类型的所有内容项时，可以提供参数以允许您对查询响应进行分页。

##### 限制结果数量

要限制结果数量，请使用 `first`。

##### 跳过 skip 的元素

要跳过一些结果，请使用 `skip`。

##### 示例

查询前 3 个内容项：

```graphql
query {
  posts(first: 3) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

查询从位置 6 到位置 10 的内容项：

```graphql
query {
  posts(
    first: 5
    skip: 5
  ) {
    contentItemId
    displayText
    publishedUtc
  }
}
```

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/j6xuupq9FYY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/wbTEUl_N0Lk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
