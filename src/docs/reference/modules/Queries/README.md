# 查询 (`OrchardCore.Queries`)

查询模块提供了一个管理UI和API，用于查询数据。

## 创建自定义查询源

### 查询

创建一个继承自`Query`的类，该类将表示表示此新查询所必需的状态。

### 查询源

创建一个实现`IQuerySource`的类，以公开新类型的查询。  
可以像这样注册查询源：

```csharp
services.AddScoped<IQuerySource, LuceneQuerySource>();
```

### 编辑器

通过为类型`Query`提供自定义的`DisplayDriver`实现来编辑查询。

```csharp
public class LuceneQueryDisplayDriver : DisplayDriver<Query, LuceneQuery>
{
...
}
```

### 查询对话框

显示查询类型列表时，将使用形状`Query_Link__[QuerySource]`的模板。  
例如，如果源是`Lucene`，则将使用文件`Query-Lucene.Link.cshtml`。

## 配方步骤

可以在配方中使用`queries`步骤创建查询。  
以下是一个示例步骤：

```json
{
    "name": "queries",
    "Queries": [ {
        // Common properties
        "Name": "AwesomeQuery",
        "Source": "Lucene",
        // Properties of the concrete query
        ...
        }
    ]
}

```

## Web API

### `api/queries/{name}`

执行指定名称的查询。

动词：**POST**和**GET**

| 参数 | 示例 | 描述 |
| --------- | ---- |------------ |
| `name` | `myQuery` | 要执行的查询的名称。 |
| `parameters` | `{ size: 3}` | 表示查询参数的Json对象。 |

## GraphQL

通过GraphQL公开查询（Lucene或SQL）时，需要定义查询返回类型的模式。  
有两个选项：返回`ContentItem`或返回自定义对象。

如果要公开`ContentItems`（例如类型为`BlogPost`），则需要选中`Return Content Items`复选框，并像这样定义`Schema`：

```json
{
    "type": "ContentItem/BlogPost"
}

```

但是，如果要公开自定义对象（例如仅DisplayText），则需要取消选中`Return Content Items`复选框，并将`Schema`更改为如下所示：

```json
{
    "type": "object",
    "properties": {  
        "Content.ContentItem.DisplayText" : {
            "type" : "string",
            "description" : "This is BlogPost display text."
        }
    }
}
```

其中属性可以是`string`或`integer`类型。

对于具有自定义对象模式的Lucene查询，您仅限于存储在Lucene索引中的元素。

对于SQL查询，可以公开查询中的任何列，其中属性名称是查询中的列别名。

以下是手动添加到数据库中的表的自定义查询示例：

```sql
-- 在查询上不要选中“返回内容项”复选框
SELECT Name FROM Test
```

以下是如何为此查询定义模式以将其添加到GraphQL端点。

```json
{
    "type": "object",
    "properties": {  
        "Name" : {
            "type" : "string",
            "description" : "This is your custom table 'Name' column."
        }
    }
}
```

如果您的查询与内容类型名称相同，则可能会导致它们在GraphQL端点中发生冲突。
对于SQL和Lucene查询，您可以在其模式中定义自定义字段类型名称。

```json
{
    "type": "object",
    "fieldTypeName": "customGraphQLFieldTypeName",
    "properties": {  
        "Name" : {
            "type" : "string",
            "description" : "This is your custom table 'Name' column."
        }
    }
}
```

## SQL查询（`OrchardCore.Queries.Sql`）

此功能提供了一种针对SQL数据库的新类型查询。

### 查询配方步骤

以下是从查询配方步骤创建SQL查询的示例：

```json
{
    "Source": "Sql",
    "Name": "ContentItems",
    "Template": "select * from ContentItemIndex", // json encoded query template
    "ReturnDocuments": false
}
```

## Liquid模板

您可以通过使用`Queries`属性从Liquid视图和模板访问查询。  
查询按名称访问，例如`Queries.RecentBlogPosts`。

### 查询

`query`过滤器提供了一种执行查询的方法。

```liquid
{% assign recentBlogPosts = Queries.RecentBlogPosts | query %}
{% for item in recentBlogPosts %}
{{ item | display_text }}
{% endfor %}
```

上面的示例将遍历查询名称为`RecentBlogPosts`的所有结果，并显示表示内容项的文本。  
可以使用查询结果的任何可用属性。此示例假定结果将是内容项。

### 参数

`query`过滤器允许您向参数化查询传递参数。例如，名为`ContentItems`的查询具有两个参数（`contentType`和`limit`），可以像这样调用它：

```liquid
{% assign fiveBlogPosts = Queries.ContentItems | query: contentType: "BlogPost", limit: 5 %}
```

## Razor Helpers

`QueryAsync`和`ContentQueryAsync`Orchard Helper扩展方法（分别在`OrchardCore.Queries`和`OrchardCore.ContentManagement`命名空间中）允许您直接从razor页面运行查询。

您可以使用`ContentQueryAsync`返回的内容项显示内容项的`DisplayAsync`扩展方法（也在`OrchardCore.ContentManagement`中）。

例如，要运行名为`RecentBlogPosts`的查询并显示结果：

```liquid
@foreach (var contentItem in await Orchard.ContentQueryAsync("RecentBlogPosts"))
{
    @await Orchard.DisplayAsync(contentItem)
}
```

> 如果视图使用Orchard Core的Razor基类，则可以在`Orchard`属性上访问Razor Helper，否则可以在所有其他情况下注入`OrchardCore.IOrchardHelper`。

## 执行SQL查询

### RDBMS支持

因为RDBMS供应商支持不同的SQL语言，所以此模块将分析您定义的查询并根据使用的RDBMS呈现特定的查询。  
这也允许在不同的RDBMS上运行的网站实例之间导出和共享查询。

### 示例

以下是返回所有已发布的博客文章的查询示例：

```sql
    select DocumentId
    from ContentItemIndex 
    where Published = true and ContentType = 'BlogPost'
```

通过选择“返回文档”选项，将加载与生成的`DocumentId`值相关联的内容项。

以下示例返回一组自定义值而不是内容项：

```sql
select
    month(CreatedUtc) as [Month],
    year(CreatedUtc) as [Year],
    day(CreatedUtc) as [Day],
    count(*) as [Count]
from ContentItemIndex 
where Published = true and ContentType = 'BlogPost' and PublishedUtc > now()
group by day(CreatedUtc), month(CreatedUtc), year(CreatedUtc)
```

## SQL参数

可以在运行查询时提供参数。  
参数是安全的，因为它们将始终在包含在查询中之前进行解析。  
参数的语法是`@name:default_value`，
其中`name`是参数的名称，`default_value`是表达式（通常是文字），用于在未定义参数时使用。

以下示例加载参数化内容类型的文档ID：

```sql
select DocumentId
from ContentItemIndex 
where Published = true and ContentType = @contenttype:'BlogPost'
```

如果在调用查询时未传递`contenttype`参数，则使用默认值。

参数名称区分大小写。

## 模板

SQL查询实际上是Liquid模板。这允许您的查询根据其获得的参数进行形状。  
在注入用户提供的值时，请确保对其进行编码，以使其无法被利用。  
建议使用参数将值注入查询，并仅使用Liquid模板更改查询的形状。

此示例检查是否提供了`limit`参数，如果是，则使用它：

```liquid
{% if limit > 0 %}
    select ... limit @limit
{% else %}
    select ...
{% endif %}
```

## 分页

使用`LIMIT [number]`和`OFFSET [number]`定义分页结果。

这些语句将根据使用的RDBMS自动转换。

## 助手函数

SQL解析器还能够将某些特定函数转换为预期的方言。

| 名称 | 描述 |
| ---------------- |----------------------------------- |
| `second(_date_)` | 返回日期的秒部分。 |
| `minute(_date_)` | 返回日期的分钟部分。 |
| `hour(_date_)`   | 返回日期的小时部分。   |
| `day(_date_)`    | 返回日期的天数部分。    |
| `month(_date_)`  | 返回日期的月份部分。  |
| `year(_date_)`   | 返回日期的年份部分。   |
| `now()`          | 返回当前日期时间（utc）。    |

## 脚本

此模块提供了以下JavaScript函数。

| 函数 | 描述 | 签名 |
| -------- | ----------- | --------- |
| `executeQuery` | 返回查询的结果。 | `executeQuery(name: String, parameters: Dictionary<string,object>): IEnumerable<object>` |

## 教程

<https://www.youtube.com/watch?v=6ZaqWmq8Pog&t=2891s>

## 破坏性更改

1.0 -> 1.1

查询API现在返回一个IQueryResults，它现在还将包含Lucene结果的计数。JSON数据结构已更改为：

```json
{
  items:[...],
  count:1231
}
```