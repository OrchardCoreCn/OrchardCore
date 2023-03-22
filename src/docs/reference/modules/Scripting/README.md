# 脚本 (`OrchardCore.Scripting`)

## 目的

脚本模块提供了一个API，允许您在不同的语言中评估自定义脚本。

## 用法

### 执行一些脚本

主要接口是 [IScriptingManager](https://github.com/OrchardCMS/OrchardCore/blob/main/src/OrchardCore/OrchardCore.Infrastructure.Abstractions/Scripting/IScriptingManager.cs)

要使用脚本引擎评估表达式，必须知道系统中可用的引擎。  
例如，默认情况下提供了JavaScript引擎，其前缀为`js`。  
要将当前日期和时间作为对象返回，我们可以这样做：

```csharp
var scriptingManager = serviceProvider.GetService<IScriptingManager>();

// 通过前缀查找javascript引擎
var engine = scriptingManager.GetScriptingEngine("js");

// 查找系统中的所有全局方法。在此，您可以根据需要向范围添加更多方法
var globalMethods = _scriptingManager.GlobalMethodProviders.SelectMany(x => x.GetMethods());

// 为引擎创建范围
var scope = engine.CreateScope(globalMethods, serviceProvider, null, null);

// 评估给定的脚本
var date = engine.Evaluate("js: new Date().toISOString()");
```

`js:`前缀用于描述代码所编写的语言。任何模块都可以提供
通过实现`IScriptingEngine`接口来提供新的脚本引擎。

### 自定义脚本环境

任何模块都可以独立于所选语言提供自定义脚本方法。  
例如，`Contents`模块提供了一个`uuid()`辅助方法，用于计算唯一的内容项标识符。

要创建全局方法，请实现`IGlobalMethodProvider`。然后，通过在模块的`Startup`中将其注册为单例，将其添加到当前的`IScriptingManager`实例中

```csharp
 services.AddSingleton<IGlobalMethodProvider, MyGlobalMethodProvider>();
```

## 文件

文件脚本引擎提供了读取文件内容的方法。

| 名称 | 示例 | 描述 |
| ---- | ---- | -------- |
| `text` | `file:text('../wwwroot/template.html')` | 返回文本文件的内容。 |
| `base64` | `file:base64('../wwwroot/image.jpg')` | 返回文件的Base64编码内容。 |


## JavaScript `OrchardCore.Scripting.JavaScript`

JavaScript脚本模块实现了一个`IScriptingEngine`，它使用[Esprima.NET](https://github.com/sebastienros/esprima-dotnet)来评估脚本。

### 方法

以下是Orchard模块提供的JavaScript方法列表。

#### 通用函数

| 函数 | 描述 
| -------- | ----------- |
| `log(level: String, text: String, param: Object): void` | 格式化并写入指定日志级别的日志消息。 |
| `uuid(): String` | 为内容项生成唯一标识符。 |
| `base64(String): String` | 解码指定的Base64编码字符串。使用https://www.base64-image.de/将文件转换为base64。 |
| `html(String): String` | 解码指定的HTML编码字符串。 |
| `gzip(String): String` | 从gzip / base64编码中解码指定的字符串。使用http://www.txtwizard.net/compression对字符串进行gzip。 |

#### 内容 (`OrchardCore.Contents`)

| 函数 | 描述 
| -------- | ----------- |
| `newContentItem(contentTypeName: String): IContent`| 创建ContentType的新实例（不持久化）|
| `createContentItem(contentTypeName: String, publish: Boolean, properties: Object): IContent`| 创建并持久化新的ContentItem。有条件地发布它。 |
| `updateContentItem(contentItem: IContent, properties: Object)`| 使用属性更新现有内容项 |
| `deleteContentItem(contentItem: IContent)`| 删除现有内容项 |
| `getUrlPrefix(path: String): String `| 使用租户前缀（如果指定）为路径添加前缀 |

#### 层 (`OrchardCore.Layers`)

| 函数 | 描述 
| -------- | ----------- |
| `isHomepage(): Boolean` | 如果当前请求URL是当前主页，则返回true |
| `isAnonymous(): Boolean` | 如果当前请求中没有经过身份验证的用户，则返回true |
| `isAuthenticated(): Boolean` | 如果当前请求中有经过身份验证的用户，则返回true |
| `url(url: String): Boolean` | 如果当前URL与提供的URL匹配，则返回true。在url参数末尾添加`*`以匹配以任何url开头的任何url |
| `culture(name: String): Boolean` | 如果当前文化名称或当前文化的父名称与`name`参数匹配，则返回true |

#### 查询 (`OrchardCore.Queries`)

| 函数 | 描述 
| -------- | ----------- |
| `executeQuery(name: String, parameters: Dictionary<string,object>): IEnumerable<object>` | 返回查询的结果。 |


#### HTTP (`OrchardCore.Workflows.Http`)

| 函数 | 描述 
| -------- | ----------- |
| `httpContext(): HttpContext` | 返回封装有关单个HTTP请求的所有特定于HTTP的信息的`HttpContext`。 |
| `queryString(name: String): String | Array` | 在没有参数的情况下调用时返回整个查询字符串（包括前导`?`），或者返回传递的参数名称的值。 |
| `responseWrite(text: String): void` | 将参数字符串直接写入HTTP响应流。 |
| `absoluteUrl(relativePath: String): String` | 返回相对路径参数的绝对URL。  |
| `readBody(): String` | 返回原始HTTP请求正文。  |
| `requestForm(name: String): String | Array` | 返回传递的表单字段名称的值。 |
| `deserializeRequestData(): Dictionary<string, object>` | 将请求数据反序列化为Dictionary<string, object>，用于发送JSON或表单数据的请求。替换了queryStringAsJson和requestFormAsJson方法 |

#### 配方 (`OrchardCore.Recipes`)

| 函数 | 描述 
| -------- | ----------- |
| `variables()` | 在配方的根部声明变量。例如：`"variables": { "blogContentItemId": "[js:uuid()]" }` 通过这样的方式检索变量值：`"ContentItemId": "[js: variables('blogContentItemId')]"` |
| `parameters()` | 检索设置期间指定的参数。例如：`"Owner": "[js: parameters('AdminUserId')]"` 参见可用的[设置配方参数](../Setup/#recipe-parameters) |
| `configuration(key: String, defaultValue: String)` | 按其键检索指定的配置设置，可选择提供默认值。例如：`[js: configuration('OrchardCore_Admin:AdminUrlPrefix', 'Admin')]` 参见[IShellConfiguration](../../core/Configuration/README.md) |

#### 工作流 (`OrchardCore.Workflows.Http`)

任何支持脚本表达式的工作流活动默认提供以下JavaScript函数：

| 函数 | 描述 |
| -------- | ----------- |
| `workflow(): WorkflowExecutionContext` | 返回提供有关当前工作流执行上下文的所有信息的`WorkflowExecutionContext`。 |
| `workflowId(): String` | 返回唯一的工作流ID。 |
| `input(name: String): Any` | 返回具有指定名称的输入参数。在工作流由工作流管理器执行时，提供工作流的输入。 |
| `output(name: String, value: Any): void` | 使用指定的名称设置输出参数。工作流输出可以由工作流的调用者收集。 |
| `property(name: String): Any` | 返回具有指定名称的属性值。属性是工作流活动可以从中读取和写入信息的字典。 |
| `lastResult(): Any` | 返回前一个活动提供的值（如果有）。 |
| `correlationId(): String` | 返回工作流实例的相关值。 |
| `signalUrl(signal: String): String` | 返回带有受保护的SAS令牌的工作流触发器URL，其中编码了指定的信号名称。如果当前工作流被配置为使用相同的信号名称的Signal活动阻塞，则使用此功能生成可以与受信任的方共享的URL以触发当前工作流。 |
| `setOutcome(outcome: String): void` | 将提供的结果添加到当前活动的结果列表中 |
| `createWorkflowToken(workflowTypeId: String, activityId: String, expiresInDays: Integer): String` | 为指定的workflowTypeid、activityId生成工作流SAS令牌。您还可以设置过期日期（以天为单位）。 |
