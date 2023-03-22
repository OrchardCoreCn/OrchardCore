# 动态缓存 (`OrchardCore.DynamicCache`)

## 目的

动态缓存允许您缓存标记的部分。  
每个缓存的标记部分可以包含其他（子）缓存的标记部分。

缓存的部分可以都有自己的缓存策略，这允许比页面级缓存更细的配置选项。

使用 `IDynamicCache` 服务存储缓存值。  
其默认实现基于 `IDistributedCache`，后者本身基于 `IMemoryCache`。

### 示例

布局（未缓存）

- 部分 A
  - 部分 A1（根据角色变化）
  - 部分 A2
- 部分 B
  - 部分 B1（根据查询字符串变化）
  - 部分 B2

## 渲染缓存的部分

当第一次渲染此页面时，将评估所有形状。已标识为可缓存的标记块将存储在 `IDynamicCache` 服务中。

在后续请求中，如果已经缓存了可缓存的部分（并且缓存条目仍然有效），则不会处理它（`ShapeMetadata` 中的 `Processing` 事件）。标记将从缓存中检索并作为响应的一部分返回。

## 使缓存的部分无效
如果缓存的部分无效，则该部分的标记将在下一次请求中重新生成，然后放回缓存以供后续请求使用。

- 如果其子级仍然被缓存，则将使用其缓存值。
- 使块无效也将使所有父块无效。

例如，如果使 `Section B2` 无效，则 `Section B` 也将无效。当渲染布局时，将再次运行 `Section B` 代码，以及 `Section B2`，但将重用 `Section B1` 的缓存内容。

缓存的部分可以定义依赖项，这允许缓存知道何时应使缓存值无效。

例如，如果缓存部分包括内容项的正文，则可能希望在该内容项更改时自动使此缓存部分无效。
您可以通过将 `contentitemid：{ContentItemId}` 添加到缓存部分的依赖项来实现此目的。

缓存的部分还可以配置滑动过期窗口、绝对过期窗口或两者兼备。  
如果未提供过期窗口，则将使用默认的一分钟滑动窗口。  
如果提供了两种类型的过期窗口，则将使用滑动策略，直到绝对过期窗口允许的最大时间为止。

### 知名缓存依赖项

以下是可用于使缓存条目无效的常见缓存依赖项值列表。

| 依赖项 | 描述 |
| --------- | ----------- |
| `contentitemid：{ContentItemId}` | 当描述其唯一 ID（`{ContentItemId}`）的内容项已发布、未发布或已删除时无效。 |
| `alias：{Alias}` | 当具有特定别名（`{Alias}`）的内容项已发布、未发布或已删除时无效。 |

您可以通过在响应事件中调用 `ITagCache` 上的 `RemoveTagAsync()` 来创建自己的依赖项。

## 变化的缓存部分（上下文）

您可能有一个需要根据请求的上下文而变化的缓存部分。  
例如，这将是在站点上的每个页面上都包含的标题部分，但对于每个用户包含不同的标记部分（例如，登录表单或当前登录用户的用户名等）。

您可以通过将“vary by”值（称为上下文）添加到缓存部分的缓存策略中来实现此目的。

在上面给出的标题示例中添加“user”上下文将为登录到您的站点的每个用户创建唯一的缓存项。

上下文是分层的。例如，如果形状根据“user”和“user.roles”上下文变化，则仅使用“user”值，因为它比“user.roles”更专业化。

上下文可以参数化，例如 `query:age` 将选择查询字符串的 `age` 值。

### 可用上下文

| 上下文 | 描述 |
| --------- | ----------- |
| `features` | 启用的功能列表。 |
| `features：{featureName}` | 指定的功能名称。 |
| `query` | 查询字符串值列表。 |
| `query：{queryName}` | 指定的查询名称值。 |
| `user` | 当前用户。 |
| `user.roles` | 当前用户的角色。 |
| `route` | 当前请求路径。 |

您可以通过实现 `ICacheContextProvider` 来创建自己的上下文。

### 回退上下文

有时您可能希望根据不可用上下文变化的已知值进行变化。

例如：您可能希望缓存所有博客文章，以便可以在整个站点上快速显示您的帖子列表。如果缓存块的缓存 ID 为 `blog-post`，则可以使用已知值作为上下文，以使每个博客文章的缓存项有所不同。在这种情况下，您可以使用内容项 ID 作为上下文：

```liquid
{% cache "blog-post-summary"，vary_by: Model.ContentItem.ContentItemId %}
    ...
{% endcache %}
```

## 用法

可以将缓存部分配置为包含形状，也可以使用 `cache` Liquid块或 `cache` Razor标记助手将其明确添加到标记中：

### 缓存形状

`ShapeMetadata.Cache(string cacheId)`

在形状实例上调用时，将标记形状为已缓存。返回 `CacheContext` 对象。

示例：`myShape.Cache("myshape")`

#### CacheContext 成员

| 方法 | 描述 |
| --------- | ----------- |
| `WithDuration(Timespan)` | 缓存指定时间量的形状。 |
| `WithSlidingExpiration(Timespan)` | 使用滑动窗口缓存指定时间量的形状。 |
| `AddContext(params string[])` | 在指定的上下文值上变化缓存的内容。 |
| `RemoveContext(string)` | 删除指定的上下文。 |
| `AddDependency(params string[])` | 定义将使缓存条目无效的上下文值。 |
| `RemoveDependency(string)` | 删除指定的依赖项。 |
| `AddTag(string)` | 将标记添加到缓存条目中，以便可以通过此标记值使其无效。 |
| `RemoveTag(string)` | 删除指定的标记。 |

!!! note
    `AddDependency` 与 `AddContext` 不同之处在于它不会为每个上下文存储多个值，
    但在上下文的值变化时会使缓存的形状内容无效。
    在内部，它们共享相同的实现，因为物理缓存键将包含依赖项上下文值。

#### 形状标记助手属性

在使用形状标记助手时，可以使用以下属性：

| Razor属性 | Liquid属性 | 描述 | 必需 |
| --------- | ----------- | ----------- | ----------- |
| `cache-id` | `cache_id` | 缓存形状的标识符。 | 是 |
| `cache-context` | `cache_context` | 一组空格/逗号分隔的上下文值。 | 否 |
| `cache-dependency` | `cache_dependency` | 一组空格/逗号分隔的依赖项值。 | 否 |
| `cache-tag` | `cache_tag` | 一组空格/逗号分隔的标记值。 | 否 |
| `cache-fixed-duration` | `cache_fixed_duration` | 条目的缓存持续时间，例如“00:05:00”表示 5 分钟。 | 否 |
| `cache-sliding-duration` | `cache_sliding_duration` | 条目的滑动缓存持续时间，例如“00:05:00”表示 5 分钟。 | 否 |

例如，在Liquid模板中缓存菜单形状，您将使用此标记：

`{% shape "menu", alias: "alias:main-menu", cache_id: "main-menu", cache_fixed_duration: "00:05:00", cache_tag: "alias:main-menu" %}`

在Liquid模板中缓存内容项形状，您可以使用此标记：

`{% contentitem alias: "alias:main-menu", cache_id: "main-menu", cache_fixed_duration: "00:05:00", cache_tag: "alias:main-menu" %}`

### Liquid缓存块

Liquid `cache` 块可用于缓存标记的部分。`cache` 块可以嵌套。

#### 参数

| Liquid属性 | 描述 | 必需 |
| --------- | ----------- | ----------- |
| `id` | 缓存形状的标识符。 | 是（这是默认的第一个参数---不需要显式指定此参数的名称。） |
| `contexts` | 一组空格/逗号分隔的上下文值。 | 否 |
| `dependencies` | 一组空格/逗号分隔的依赖项值。 | 否 |
| `expires_after` | 条目的缓存持续时间，例如“00:05:00”表示 5 分钟。 | 否 |
| `expires_sliding` | 条目的滑动缓存持续时间，例如“00:05:00”表示 5 分钟。 | 否 |

#### 示例

简单块：

```liquid
{% cache "my-cache-block" %}
    ...
{% endcache %}
```

嵌套块：

```liquid
{% cache "a" %}
    A {{ "now" | date: "%T" }} (No Duration) <br />
    {% cache "a1", dependencies: "a1", vary_by: "user", expires_after: "0:5:0" %}
        A1 {{ "now" | date: "%T" }} (5 Minutes) <br />
    {% endcache %}
    {% cache "a2", dependencies: "a2", expires_after: "0:0:1" %}
        A2 {{ "now" | date: "%T" }} (1 Second) <br />
        {% cache "a2a", dependencies: "a2a", contexts: "route", expires_sliding: "0:0:5" %}
            A2A {{ "now" | date: "%T" }} (5 Seconds) <br />
        {% endcache %}
    {% endcache %}
{% endcache %}
```

### 更改缓存范围

当您进入缓存块时，您可能尚未知道所有子依赖项，甚至不知道缓存块应缓存多长时间。  
例如，可能是围绕查询的内容项列表的缓存块---因为您不知道查询将显示哪些内容项，因此无法在进入缓存块时定义正确的依赖项。

有四个标记允许您更改当前缓存范围。即使您不确定自己是否在缓存块内，也可以使用这些标记：

| Liquid标记 | 描述 | 示例 |
| --------- | ----------- | ----------- |
| `cache_dependency` | 将依赖项添加到当前缓存范围。 | `{% cache_dependency "alias:{Alias}" %}` |
| `cache_expires_on` | 设置缓存项将过期的固定日期和时间。在单个块中定义多个过期策略时，最严格的缓存策略（即生命周期最短的策略）将获胜。 | `{% cache_expires_on {A DateTime or DateTimeOffset instance %}`（例如来自内容项上的日期/时间字段） |
| `cache_expires_after` | 设置相对于缓存项缓存时的时间跨度，缓存项将在此时间跨度后过期。在单个块中定义多个过期策略时，最严格的缓存策略（即生命周期最短的策略）将获胜。 | `{% cache_expires_after "01:00:00" %}`（一小时） |
| `cache_expires_sliding` | 为缓存项的过期设置滑动窗口。在单个块中定义多个过期策略时，最严格的缓存策略（即生命周期最短的策略）将获胜。 | `{% cache_expires_sliding "00:01:00" %}`（一分钟） |

#### 示例：

显示查询的内容项：

```liquid
{% cache "recent-blog-posts" %}
    {% assign recentBlogPosts = Queries.RecentBlogPosts | query %}
    {% for item in recentBlogPosts %}
        {{ item | display_text }}

        {% assign cacheDependency = "contentitemid:" | append: Model.ContentItem.ContentItemId %}
        {% cache_dependency cacheDependency %}
    {% endfor %}
{% endcache %}
```

由查询显示的每个项目现在都会将其自己的缓存依赖项添加到 `recent-blog-posts` 缓存块中。

### Razor缓存标记助手

!!! note
    这已从 `<cache>` 重命名为 `<dynamic-cache>`，以避免与 ASP.NET Core 缓存标记助手发生冲突。

> 该文档由ChatGPT 4 翻译