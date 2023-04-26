# GraphQL (OrchardCore.GraphQL)

## GraphQL

GraphQL模块允许客户端应用程序查询Orchard网站处理的内容。它使GraphiQL Explorer视图能够测试GraphQL查询，并提供HTTP端点以发送客户端查询。

## HTTP方法，标头和正文

### GET请求

在接收到HTTP GET请求时，GraphQL查询应在“query”查询字符串中指定。例如，如果我们想执行以下GraphQL查询：

```graphql
{
  me {
    name
  }
}
```

可以通过HTTP GET发送此请求：

`http://myapi/graphql?query={me{name}}`

查询变量可以作为JSON编码的字符串发送到名为variables的附加查询参数中。如果查询包含多个命名操作，则可以使用operationName查询参数来控制应执行哪个操作。

### POST请求

#### application/json内容类型

标准的GraphQL POST请求应使用`application/json`内容类型标头，并包括以下形式的JSON编码正文：

```graphql
{
  "query": "...",
  "operationName": "...",
  "variables": { "myVariable": "someValue", ... }
}
```

`operationName`和`variables`是可选字段。仅在查询中存在多个操作时才需要`operationName`。

#### application/graphql内容类型

另一个选项是使用`application/graphql`内容类型标头，HTTP POST正文内容将被视为GraphQL查询字符串。

#### 查询字符串

除上述内容外，如果存在“query”查询字符串参数（如上面的GET示例中），则将解析并以与HTTP GET相同的方式处理。

### 响应

无论通过哪种方式发送查询和变量，响应都以JSON格式在请求正文中返回。查询可能会导致一些数据和一些错误，并以以下形式的JSON对象返回：

```json
{
  "data": { ... },
  "errors": [ ... ]
}
```

如果没有返回错误，则响应中不存在“errors”字段。如果未返回数据，则仅在执行期间发生错误时才包括“data”字段。

## 身份验证

执行GraphQL查询需要发行者具有`ExecuteGraphQL`权限。与Orchard Core中的任何其他API一样，GraphQL API支持cookie和OAuth 2.0身份验证。这意味着它与OpenId模块兼容，并支持JSON Web Token（JWT）。

默认情况下，匿名用户无法执行GraphQL查询。

## 配置

可以通过标准的shell配置来配置公共选项，如暴露异常和最大深度、最大复杂度和字段影响。

配置如下。

```json
{
  "OrchardCore": {
    "OrchardCore_Apis_GraphQL": {
      "ExposeExceptions": true,
      "MaxDepth": 50, 
      "MaxComplexity": 100, 
      "FieldImpact": 2.0,
      "DefaultNumberOfResults": 100,
      "MaxNumberOfResults": 1000,
      "MaxNumberOfResultsValidationMode": "Default"
    }
  }
}
```

*ExposeExceptions（bool，默认值：生产为false，开发为true）*

如果设置为true，则将堆栈跟踪暴露给graphql客户端

*DefaultNumberOfResults（int，默认值：100）*
所有分页字段/类型返回的默认结果数。

*MaxNumberOfResults（int，默认值：1000）*
所有分页字段/类型返回的最大结果数。

*MaxNumberOfResultsValidationMode（枚举，值：Default | Enabled | Disabled，默认值：Default）()*
如果在分页器参数中超过了最大结果数，则指定验证行为

* 默认-在生产中将记录信息并仅返回最大结果数。在开发中，将引发graphql验证错误。
* Enabled-将引发graphql验证错误
* Disabled-将记录信息并仅返回最大结果数

*MaxDepth（int？，默认值：20）*

强制执行请求中所有查询的总最大嵌套。

*MaxComplexity（int？，默认值：null）*

*FieldImpact（double？，默认值：null）*

有关MaxDepth、MaxComplexity、FieldImpact和保护免受恶意查询的更多信息，请参见graphql-dot-net文档：<https://graphql-dotnet.github.io/docs/getting-started/malicious-queries/>
> 该文档由ChatGPT 4 翻译
