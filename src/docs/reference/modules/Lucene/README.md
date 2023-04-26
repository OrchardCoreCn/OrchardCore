# Lucene (`OrchardCore.Search.Lucene`)

Lucene模块允许您管理Lucene索引。

## 配方步骤

可以使用`lucene-index`步骤在配方执行期间创建Lucene索引。以下是一个示例步骤：

```json
{
  "steps":[
    {
      "name":"lucene-index",
      "Indices":[
        {
          "Search":{
            "AnalyzerName":"standardanalyzer",
            "IndexLatest":false,
            "IndexedContentTypes":[
              "Article",
              "BlogPost"
            ]
          }
        }
      ]
    }
  ]
}
```

### 查询配方步骤

以下是从查询配方步骤创建Lucene查询的示例：

```json
{
  "steps": [
    {
      "Source": "Lucene",
      "Name": "RecentBlogPosts",
      "Index": "Search",
      "Template":"...", // JSON encoded query template.
      "ReturnContentItems": true
    }
  ]
}
```

## Web API

### `api/lucene/content`

执行具有指定名称的查询并返回相应的内容项。

动词：`POST`和`GET`

| 参数 | 示例 | 描述 |
| --------- | ---- |------------ |
| `indexName` | `search` | 要查询的索引的名称。 |
| `query` | `{ "query": { "match_all": {} } }` | 表示查询的JSON对象。 |
| `parameters` | `{ size: 3}` | 表示查询参数的JSON对象。 |

### `api/lucene/documents`

执行具有指定名称的查询并返回相应的Lucene文档。仅返回存储的字段。

动词：`POST`和`GET`

| 参数 | 示例 | 描述 |
| --------- | ---- |------------ |
| `indexName` | `search` | 要查询的索引的名称。 |
| `query` | `{ "query": { "match_all": {} } }` | 表示查询的JSON对象。 |
| `parameters` | `{ size: 3}` | 表示查询参数的JSON对象。 |

## Lucene Worker (`OrchardCore.Search.Lucene.Worker`)

此功能创建一个后台任务，该任务将使本地文件系统索引与可能具有自己的本地索引的其他实例同步。  
建议仅在多个实例（农场）上运行相同租户并使用Lucene文件系统索引时使用它。

如果您正在运行Azure App Services或使用Elasticsearch，则不需要此功能。

## Lucene查询

Lucene模块提供了用于使用ElasticSearch查询查询Lucene数据的管理UI和API。
请参见：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html

### 查询过滤器

查询过滤器用于从Lucene中检索记录，而不必关心它们的提升值。因此，它正在检索记录，就像SQL数据库一样。

以下是过滤查询的示例：

```json
{
  "query":{
    "bool":{
      "filter":[
        {
          "term":{
            "Content.ContentItem.Published":"true"
          }
        },
        {
          "wildcard":{
            "Content.ContentItem.DisplayText":"Main*"
          }
        }
      ]
    }
  }
}
```

在bool查询中使用must查询。"查找特定内容类型"

```json
{
  "query":{
    "bool":{
      "must":{
        "term":{
          "Content.ContentItem.ContentType.keyword":"Menu"
        }
      },
      "filter":[
        {
          "term":{
            "Content.ContentItem.Published":"true"
          }
        },
        {
          "wildcard":{
            "Content.ContentItem.DisplayText":"Main*"
          }
        }
      ]
    }
  }
}
```

使用[`query_string` Lucene查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)和[查询解析器语法](https://lucene.apache.org/core/2_9_4/queryparsersyntax.html)（使用语法如“exact match”和“should AND contain”）：

```json
{
    "query": {
        "query_string": {
            "query": "Content.ContentItem.FullText:\"exploration\""
        }
    }
}
```

或者以一种不必在查询中选择字段的方式（以允许用户进行更简单的搜索）：

```json
{
  "query":{
    "query_string":{
      "query":"\"exploration\"",
      "default_field":"Content.ContentItem.FullText"
    }
  }
}
```

与以前的一个替代方案[`simple_query_string`](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-simple-query-string-query.html)：

```json
{
  "query":{
    "simple_query_string":{
      "query":"\"exploration\"",
      "fields":[
        "Content.ContentItem.FullText"
      ]
    }
  }
}
```

正如您所看到的，它允许在多个查询类型上进行过滤。 Lucene中可用的所有查询类型也都是过滤器。

因此，您可以使用：

- `bool`
- `geo_distance`
- `geo_bounding_box`
- `fuzzy`
- `match`
- `match_all`
- `match_phrase`
- `prefix`
- `query_string`
- `range`
- `regexp`
- `simple_query_string`
- `term`
- `terms`
- `wildcard`

有关更多详细信息，请参见ElasticSearch文档：
https://www.elastic.co/guide/en/elasticsearch/reference/current/query-filter-context.html

## 自动映射

从OC版本1.5开始，Lucene模块将自动将文本字段映射为索引中的`.keyword`后缀作为`stored`值，除非文档已经明确设置为`stored`。它将忽略长度大于256个字符的任何值。这样，任何TextField都可以用作技术值，并使用术语查询进行搜索。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/9EgZ_J1npw4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
