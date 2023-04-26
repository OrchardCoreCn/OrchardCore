# 创建查询已分配分类法的内容所需的组件

## 你将会创建的内容

在这个例子中，你将会创建一个搜索工具，根据已分配的分类法来筛选博客文章。当然，这并不需要限制只在博客中使用。

## 你需要的内容

你将会从使用博客配方的新 Orchard Core 网站开始。

你将会使用 Liquid 模板使 Lucene 查询变成动态的。

你将会使用 Razor 文件来调用这个查询。

## 设置环境

为了让这个示例更加有趣，我们将会编辑 BlogPost 的内容类型以允许多个类别的分配。

前往 Content > Content Definition > Content Types 并点击编辑 Blog Post。然后点击在 "Category" 编辑器旁边的 "Edit" 按钮。取消选择 "唯一" 并点击 "保存"。同时，由于我们想要让 2 个分类法字段（Category 和 Tags）可以被搜索，因此可以在这里进行设置。点击编辑 Category，然后勾选 "将该元素包含在索引中" 的复选框。
现在在博客文章内容类型的“Tags”字段上执行相同的操作。

确保通过导航到“搜索>索引>Lucene索引”，然后单击“重建”，重新构建索引。

然后导航到内容>内容类型>分类法，单击以编辑分类。 然后单击“添加分类”添加“Politics”。 然后发布分类法。

您可能想创建更多的博客文章，并分配各种组合的标签和类别，以便您可以尝试筛选器。

首先添加一个新的Lucene查询。将其命名为“GetBlogsByFilter”。您可以将架构留空，将索引设置为默认值（“搜索”），并选中“返回内容项”复选框。

查询本身将使用Liquid进行以下操作：

- 检查是否有每个筛选器的值
- 根据筛选器数据构建正确的块

### 快速回顾

博客配方为我们创建了2个“分类法”内容类型，“分类”和“标签”。 我们要构建的内容将允许我们传递0个或多个分类和0个或多个标签，并获取与这些筛选器相关联的BlogPosts集合。
### 一个筛选对象

在JSON中，模拟这个筛选器的一个好方法是这样的:

```
{
  categories: [...],
  tags: [...],
}
```

### 一个起点查询

```
{
	"size": 10,
	"query": {
 	  "term": { "Content.ContentItem.ContentType.keyword" : "BlogPost" }
	}
```
### 更加智能的查询

为了创建那个逻辑，我们可以这样做：

```json
{
	"size": 10,
	"query": {
		"bool": {
			"must": [
				{
					"match": {
						"published": true
					}
				},
				{
					"bool": {
						"should": [
							{
								"match_phrase": {
									"title": "search term"
								}
							},
							{
								"match_phrase": {
									"body": "search term"
								}
							}
						]
					}
				}
			],
			"filter": {
				"bool": {
					"should": [
						{
							"terms": {
								"category": ["category1", "category2", "category3"]
							}
						},
						{
							"terms": {
								"tag": ["tag1", "tag2", "tag3"]
							}
						}
					]
				}
			}
		}
	}
}
```

上面的查询会从 10 个 BlogPosts 中获取数据，而无需使用筛选器。如果我们收到的筛选器如下所示，则将使用此查询：

```
{
  categories: ["category1", "category2", "category3"],
  tags: ["tag1", "tag2", "tag3"],
}
```

这个查询比第一个查询更加智能。它还包括“published”字段的匹配。此外，通过使用“should”语句，它为标题和正文同时提供了一个搜索术语的匹配。最后，在筛选器中，我们使用“terms”条款来匹配所需的分类和标签。
### 一个简单的查询

如果您想获取Elasticsearch中所有的`BlogPost`，可以使用以下查询:

```json
{
  "query": {
    "term": { "Content.ContentItem.ContentType.keyword" : "BlogPost" }
  }
}
```

注意，查询是在 `Content.ContentItem.ContentType` 字段中使用 `term` 的方式。这将返回所有类型为 `BlogPost` 的内容项。

### 使用过滤器查询

让我们使用过滤器来取出`BlogPost`，其中属于指定类别和标签之一。首先我们可以使用 `bool` 过滤器,让我们添加一个 `must` 条件和两个 `should` 条件。在一个过滤器中，`must` 子句是必须匹配的，而`should`子句则可以至少匹配一个。

```json
{
  "query": {
    "bool": {
      "must": [
        { "term": { "Content.ContentItem.ContentType.keyword" : "BlogPost" } }
      ],
      "should": [
        { "term": { "BlogPost.Category": "C#" } },
        { "term": { "BlogPost.Tag": "Elasticsearch" } }
      ]
    }
  }
}
```

上面的查询将返回属于`C#`类别或`Elasticsearch`标签的`BlogPost`。 

如果我们添加了类别或标签，则查询将仅限于这些项目。

```json
{
  "query": {
    "bool": {
      "must": [
        { "term": { "Content.ContentItem.ContentType.keyword" : "BlogPost" } }
      ],
      "should": [
        { "term": { "BlogPost.Category": "{{ category }}" } },
        { "term": { "BlogPost.Tag": "{{ tag }}" } }
      ]
    }
  }
}
```

如果查询未指定类别或标签，则它将返回10篇 `BlogPost`。我们可以添加一个条件来解决这个问题。

```json
{
  "query": {
    "bool": {
      "must": [
        { "term": { "Content.ContentItem.ContentType.keyword" : "BlogPost" } }
      ],
      "should": [
        { "term": { "BlogPost.Category": "{{ category }}" } },
        { "term": { "BlogPost.Tag": "{{ tag }}" } }
      ]
    }
  }
}
```

请注意，`if`条件未被满足，因此`else`块运行，并返回10个 `BlogPost`。

### 一个可以正常工作的查询

要执行实际过滤工作，我们将使用 `bool`，`must` 和 `match`。`Match`将处理将多个术语通过`OR`结合在一起的操作。`Must`将处理需要用`AND`连接的两个不同的分类法。

考虑下面的代码块：

```json
"match": {
  "BlogPost.Category": {
```


这将返回类别设置为“4..a”或“4..b”的 BlogPost 内容项。

要在分类字段上创建 `AND` 条件，我们将在 `must` 块中包装 `match` 块。我们将使用 Liquid 仅在需要时包含 `match` 块。我们还将使用 Liquid 循环遍历传递到数组中的值。
最后，将所有内容组合起来生成可用的查询：

```
该部分是一个用于Elasticsearch搜索的请求JSON对象。它包含了一个大小为10的字段和一个查询字段。如果有类别或标签，查询应用了一个布尔型条件，必须满足条件。如果有类别，使用“match”查询类别，查询语句为“BlogPost.Category”，操作符为“或”，并且匹配搜索中的类别关键字。如果有标签，同样使用“match”查询标签，查询语句为“BlogPost.Tags”，操作符为“或”，并且匹配搜索中的标签关键字。
## 在代码中使用查询

这篇文章并不旨在提供完整的实现。然而，作为简单的示例，您可以在视图中执行以下操作：

```python
from elasticsearch import Elasticsearch
from django.conf import settings

def search(request):
    client = Elasticsearch(settings.ES_CONNECTION_CLASS(
        hosts=settings.ES_HOSTS
    ))
    
    query = {
        # 之前的查询语句
        "query": {
            "bool": {
                "must": [
                    {
                        "match": {
                            "Content.ContentItem.ContentType.keyword": "BlogPost"
                        }
                    },
                    
                    # 如果标签存在，则添加标签查询
                    {% if tags %}
                        {
                            "match": {
                                "Content.TagNames": {
                                    "query":"{% for tag in tags %}{{tag}} {% endfor %}",
                                    "operator": "or"
                                }
                            }
                        },
                    {% endif %}
                ],
            }
        }
    }
    
    results = client.search(index=settings.ES_INDEX, body=query)
    
    # 处理搜索结果
    # ...
    
    return render(request,'search_results.html',{
        # 上面的搜索结果处理
        }
    })
```

在这个例子中，我们假设您已经在settings.py文件中定义了Elasticsearch的主机和连接设置。通过创建一个新的Elasticsearch客户端，我们可以使用做好的查询来搜索。最后，我们将搜索结果传递给模板以呈现给用户。
## 概述

您刚刚创建了用于搜索博客的组件，以找到仅带有特定标签的博客。


> 该文档由Chat-GPT 翻译
