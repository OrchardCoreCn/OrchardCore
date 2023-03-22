# Liquid (`OrchardCore.Liquid`)

该模块提供了一种从管理站点安全地创建模板的方法。  
有关Liquid语法的更多信息，请参见此网站：<https://shopify.github.io/liquid/>。
Liquid语法由Fluid驱动。请查看<https://github.com/sebastienros/fluid>以获取额外的示例和自定义过滤器。

## 一般概念

### HTML转义

默认情况下，所有输出都会被编码为HTML。  
这意味着任何HTML保留字符都将转换为相应的HTML实体。  
如果您需要呈现一些原始HTML字符，可以使用“raw”过滤器。

## 内容项过滤器

所有在标准Liquid语法中可用的默认过滤器都可在OrchardCore中使用。  
此外，每个Orchard模块都可以为其自己的目的提供自定义过滤器。 
以下是适用于内容项的常见过滤器列表。

### `display_url`

返回内容项的URL。

输入

```liquid
{{ Model.ContentItem | display_url }}
```

输出

```text
/blog/my-blog-post
```

### `display_text`

返回内容项的标题。

输入

```liquid
{{ Model.ContentItem | display_text }}
```

输出

```text
My Blog Post
```

### `container`

返回另一个内容项的容器内容项。

输入

```liquid
{{ Model.ContentItem | container | display_text }}
```

在此示例中，我们假设`ContentItem`表示博客文章。

输出

```text
Blog
```
## 字符串过滤器

### `slugify`

将文本转换为可用于URL的字符串。

输入

```liquid
{{ "This is some text" | slugify }}
```

输出

```text
this-is-some-text
```

### `local`

将UTC日期和时间转换为基于站点设置的本地日期和时间。

输入

```liquid
{{ "now" | local | date: "%c" }}
```

或

```liquid
{{ Model.ContentItem.CreatedUtc | local | date: "%c" }}
```

输出

```text
2017年8月2日星期三 11:54:48
```

### `utc`

将本地日期和时间转换为基于站点设置的UTC日期和时间。

输入

```liquid
{{ "now" | utc | date: "%c" }}
```

输出

```text
2017年8月2日星期三 11:54:48
```

### `t`

使用当前区域设置本地化字符串。

输入

```liquid
{{ "Hello!" | t }}
```

输出

```text
Bonjour!
```

## Html过滤器

### `absolute_url`

为给定的相对虚拟路径创建完整的绝对URL。

输入

```liquid
{{ '~/some-page' | absolute_url }}
```

如果当前租户没有URL前缀，则输出：

```text
https://example.com/some-page
```

如果当前租户有URL前缀，则输出：

```text
https://example.com/url-prefix/some-page
```

如果输入URL以波浪线开头，则输出URL的基础始终是当前租户的根URL，包括如果已配置租户前缀，则包括租户前缀。例如，`~/`将始终指向租户的主页，而不考虑配置。

还会添加HTTP请求方案（例如“https”）和端口号。

### `href`

为相对虚拟路径创建内容URL。建议在每种情况下生成引用相对路径的URL。

输入

```liquid
{{ '~/some-page' | href }}
```

如果当前租户没有URL前缀，则输出：

```text
/some-page
```

如果当前租户有URL前缀，则输出：

```text
/url-prefix/some-page
```

如果输入URL以波浪线开头，则输出URL始终相对于当前租户的根URL，包括如果已配置租户前缀，则包括租户前缀。例如，`~/`将始终指向租户的主页，而不考虑配置。

### `html_class`

将字符串转换为友好的HTML类。

输入

```liquid
{{ "LandingPage" | html_class }}
```

输出

```text
landing-page
```

### `liquid`

呈现Liquid字符串模板。

输入

```liquid
{{ Model.ContentItem.Content.Paragraph.Content.Html | liquid }}
```

在此示例中，我们假设`Model.ContentItem.Content.Paragraph.Content`表示`HtmlField`，而`Html`是字段值。

输出

```html
<p> <img src="/blog/media/kitten.jpg" /> </p>
```

可以选择为模型绑定传递类。

### `markdownify`

将Markdown字符串转换为HTML。

输入

```liquid
{{ "### Services" | markdownify }}
```

输出

```html
<h3>Services</h3>
```

### `sanitize_html`

消毒一些HTML内容。

```liquid
{% capture output %}
  <span class="text-primary">{{ Content }}</span>
{% endcapture %}
{{ output | sanitize_html | raw }}
```

### `shortcode`

呈现短代码。应与“raw”过滤器结合使用。

```liquid
{{ Model.ContentItem.Content.RawHtml.Content.Html | shortcode | raw }}
```

## Json过滤器

### `json`

将输入值序列化为json字符串。要将json格式缩进，请将true参数传递给Liquid过滤器。

示例：

```liquid

{{ Model.ContentItem.Content | json }}

{{ Model.ContentItem.Content | json: true }}
```

### `jsonparse`

将json字符串转换为JObject。 
这可以用于构建集合并在Liquid中迭代值。

示例：

```liquid
{% capture someCollection %}
[
  {"key":"key1", "value":"value1"},
  {"key":"key2", "value":"value2"},
]
{% endcapture %}

{% assign jsonObject = someCollection | jsonparse %}

{% for k in jsonObject %}
  {{k["key"]}} {{k["value"]}}
{% endfor %}
```

## 属性

默认情况下，Liquid模板可以访问一组公共对象。

### `Model.Content`

如果可用，则为包含由内容的部分和字段生成的所有形状的区域形状。

### `Model.ContentItem`

如果可用，则表示正在呈现的当前内容项。

以下属性在`ContentItem`对象上可用。

| 属性 | 示例 | 描述 |
| --------- | ---- |------------ |
| `Id` | `12` | 数据库中文档的ID。 |
| `ContentItemId` | `4qs7mv9xc4ttg5ktm61qj9dy5d` | 所有版本的内容项的公共标识符。 |
| `ContentItemVersionId` | `4jp895achc3hj1qy7xq8f10nmv` | 内容项版本的唯一标识符。 |
| `DisplayText` | `Blog` | 内容项的标题。可以使用TitlePart手动编辑。 |
| `Number` | `6` | 版本号。 |
| `Owner` | `admin` | 创建此内容项的用户的用户名。 |
| `Author` | `admin` | 此版本的编辑器的用户名。 |
| `Published` | `true` | 此内容项版本是否已发布。 |
| `Latest` | `true` | 此内容项版本是否为内容项的最新版本。 |
| `ContentType` | `BlogPost` | 内容类型。 |
| `CreatedUtc` | `2017-05-25 00:27:22.647` | 创建或发布内容项的时间。 |
| `ModifiedUtc` | `2017-05-25 00:27:22.647` | 创建内容项版本的时间。 |
| `PublishedUtc` | `2017-05-25 00:27:22.647` | 上次发布内容项的时间。 |
| `Content` | `{ ... }` | 包含所有内容属性的文档。请参阅下面的用法。 |

#### 内容属性

内容项的`Content`属性公开了其所有部分和字段。可以直接评估`Content`以检查所有可用属性。它将呈现完整文档。

```liquid
<pre>{{ Model.ContentItem.Content }}</pre>
```

约定是，每个部分都按其名称作为第一级公开。  
如果内容项具有自定义字段，则它们将在名称匹配内容类型的部分下可用。

例如，假设类型`Product`具有名为`Size`的文本字段，请按如下方式访问此字段的值：

```liquid
{{ Model.ContentItem.Content.Product.Size.Text }}
```

同样，如果内容项具有`Title`部分，我们可以按如下方式访问它：

```liquid
{{ Model.ContentItem.Content.TitlePart.Title }}
```
**注意**：这不再是显示内容项标题的推荐方法。
请改用`Model.ContentItem.DisplayText`属性。


### 用户

表示当前请求的已验证用户。

以下属性在`User`对象上可用。

| 属性 | 示例 | 描述 |
| --------- | ---- |------------ |
| `Identity.Name` | `admin` | 已验证用户的名称。 |
| `Identity.Claims` |  | 已验证用户的声明。 |

##### user_email过滤器

返回用户的电子邮件。

```liquid
{{ User | user_email }}
```

##### user_id过滤器

返回用户的唯一标识符。

```liquid
{{ User | user_id }}
```

##### users_by_id过滤器

按ID（S）从数据库加载单个或多个用户对象。

生成的对象具有以下属性：

| 属性 | 示例 | 描述 |
| --------- | ---- |------------ |
| `UserId` | `42z3ps88pm8d40zn9cfwbee45c ` | 已验证用户的ID。 |
| `UserName` | `admin` | 已验证用户的名称。 |
| `NormalizedUserName` | `ADMIN` | 已验证用户的规范化名称。 |
| `Email` | `admin@gmail.com` | 已验证用户的电子邮件。 |
| `NormailizedEmail` | `ADMIN@GMAIL>COM` | 已验证用户的规范化电子邮件。 |
| `EmailConfirmed` | `true` | 如果用户已确认其电子邮件或不需要电子邮件确认，则为True |
| `IsEnabled` | `true` | 如果用户已启用，则为True |
| `RoleNames` | `[Editor,Contributor]`  | 分配给用户的角色名称数组 |
| `Properties` | `UserProfile.FirstName.Text` | 保存用户的自定义用户设置。 |

您可以使用此过滤器来加载当前已验证用户的用户信息，如下所示。

```liquid
{% assign user = User | user_id | users_by_id %}

{{ user.UserName }} - {{ user.Email }}

```

您可以将此过滤器与UserPicker字段一起使用，以加载选定用户的信息。

```liquid
{% assign users = Model.ContentItem.Content.SomeType.UserPicker.UserIds | users_by_id %}

{% for user in users %}
  {{ user.UserName }} - {{ user.Email }}
{% endfor %}

```
#### User has_permission filter

检查用户是否具有权限清除，可选地在资源上

```liquid
{{ User | has_permission:"EditContent",Model.ContentItem }}
```

#### User is_in_role filter

检查用户是否在角色中

```liquid
{{ User | is_in_role:"Administrator" }}
```

#### User has_claim filter

检查用户是否具有指定类型的声明

```liquid
{{ User | has_claim:"email_verified","true" }}
{{ User | has_claim:"Permission","ManageSettings" }}
```

提供对当前站点设置的访问，例如 `Site.SiteName`。

| 属性 | 示例 | 描述 |
| -------- | ------- |------------ |
| `BaseUrl` |  | 站点的基本URL。| 
| `Calendar` |  | 站点的日历。| 
| `MaxPagedCount` | `0` | 可分页的最大页面数。| 
| `MaxPageSize` | `100` | 用户可以设置的最大页面大小。| 
| `PageSize` | `10` | 列表的默认页面大小。| 
| `SiteName` | `My Site` | 站点的友好名称。| 
| `SuperUser` | `4kxfgfrxqmdpnt5n508cqvpvca` | 站点超级用户的用户ID。| 
| `TimeZoneId` | `America/Los_Angeles` | 站点的时区ID，根据tz数据库，参见https://en.wikipedia.org/wiki/List_of_tz_database_time_zones | 
| `UseCdn` | `false` | 启用/禁用CDN的使用。| 
| `ResourceDebugMode` | `Disabled` | 提供了是否使用src或debug-src来加载脚本和样式表的选项| 
| `CdnBaseUrl` | `https://localhost:44300` | 如果提供了CDN基本URL，则会将其前缀添加到本地脚本和样式表中| 
| `Meta` |  | 在当前主题的head部分呈现的元数据。| 
请求。

以下属性可在 `Request` 对象上使用。

| 属性 | 示例 | 描述 |
| --------- | ---- |------------ |
| `QueryString` | `?sort=name&page=1` | 带有前导'?'字符的转义查询字符串。|
| `UriQueryString` | `?sort=name&page=1` | 以正确的方式转义的查询字符串，以便合并到URI表示中。|
| `ContentType` | `application/x-www-form-urlencoded; charset=UTF-8` | `Content-Type` 标头。|
| `ContentLength` | `600` | `Content-Length` 标头。|
| `Cookies` | 用法：`Request.Cookies.orchauth_Default` | 此请求的 cookie 集合。|
| `Headers` | 用法：`Request.Headers.accept` | 请求标头。每个属性值都是值数组。|
| `Query` | 用法：`Request.Query.sort` | 从 `QueryString` 解析的查询值集合。每个属性值都是值数组。|
| `Form` | 用法：`Request.Form.value` | 表单值的集合。|
| `Protocol` | `https` | 此请求的协议。|
| `Path` | `/OrchardCore.ContentPreview/Preview/Render` | 请求的未转义路径。|
| `UriPath` | `/OrchardCore.ContentPreview/Preview/Render` | 以正确的方式转义的路径，以便合并到URI表示中。|
| `PathBase` | `/mytenant` | 此请求的未转义基本路径。|
| `UriPathBase` | `/mytenant` | 以正确的方式转义的基本路径，以便合并到URI表示中。|
| `Host` | `localhost:44300` | 未转义的 `Host` 标头。可能包含端口。|
| `UriHost` | `localhost:44300` | 适用于在HTTP标头中使用的URI格式化和编码的 `Host` 标头。|
| `IsHttps` | `true` | 如果请求的方案为 `https`，则为 True。|
| `Scheme` | `https` | 请求的方案。|
| `Method` | `GET` | HTTP 方法。|
| `Route` | 用法：`Request.Route.controller` | 此请求的路由值。|
文化。

以下属性可在 `Culture` 对象上使用。

| 属性 | 示例 | 描述 |
| --------- | ---- |------------ |
| `Name` | `en-US` | 请求的区域设置作为 ISO 语言代码。|
| `Dir` | `rtl` | 文本书写方向。|


### HttpContext

表示当前请求的 HttpContext。

以下属性可在 `HttpContext` 对象上使用。

| 属性 | 示例 | 描述 |
| --------- | ---- |------------ |
| `Items` | `HttpContext.Items["Item1"]` | 返回具有键 Item1 的项。|


#### httpcontext_add_items
将键/值添加到 HttpContext.Items 集合中

`{% httpcontext_add_items Item1:"value 1", Item2:"Value2"  %}`

#### httpcontext_remove_items
从 HttpContext.Items 集合中删除键

`{% httpcontext_remove_items "Item1" %}`


## Shape Filters

这些过滤器允许您创建和过滤形状。

### `shape_new`

返回具有指定名称的形状作为输入。

输入

```liquid
{% assign date_time = "DateTime" | shape_new %}
```

创建形状时，还可以传递属性。  
属性名称会转换为 PascalCase。例如：`prop_name1` 可以通过形状模板中的 `Model.PropName1` 访问。

```liquid
{{ Model.Content | shape_new: prop_value1: "some value", prop_value2: 5 }}
```

### `shape_render`

呈现形状。

```liquid
{{ Model.Content | shape_render }}
```

### `shape_stringify`

将形状转换为其字符串表示形式。与 `shape_render` 不同，此过滤器的结果将被编码，如果在输出中呈现，则会被编码。

输入

```liquid
{{ "DateTime" | shape_new | shape_stringify }}

```

输出

```text
Monday, September 11, 2017 3:29:26 PM
```

### `shape_properties`

返回具有添加的属性的形状。
属性名称会转换为 PascalCase。例如：`prop_name1` 可以通过形状模板中的 `Model.PropName1` 访问。

输入

```liquid
{% assign my_shape = "MyCustomShape" | shape_new | shape_properties: prop_value1: "some value", prop_value2: 5 %}
```

## Layout Tags

### `layout`

设置视图的布局。

输入

```liquid
{% layout "CustomLayout" %}
```

在当前主题的 `Layout` 形状中内部添加了一个替代项。

### `render_body`

在布局中，呈现当前视图的正文。

输入

```liquid
{% render_body %}
```

### `render_section`

在布局中，呈现具有指定名称的部分。

输入

```liquid
{% render_section "Header", required: false %}
```

### `page_title`

更改并呈现当前页面的标题。

输入

```liquid
{% page_title Site.SiteName, position: "before", separator: " - " %}
```

默认参数是附加到标题当前值的文本。  
`position` 是将值附加的位置，在此示例中在开头。  
`separator` 是用于分隔标题所有片段的字符串。

## Shape Tags

### `shape_clear_alternates`

从形状中删除任何替代项。

输入

```liquid
{% shape_clear_alternates my_shape %}
```

### `shape_add_alternates`

向形状添加替代项。

输入

```liquid
{% shape_add_alternates my_shape "alternate1 alternate2" %}

{% assign my_alternates = "alternate1,alternate2" | split: "," %}
{% shape_add_alternates my_shape my_alternates %}
```

### `shape_clear_wrappers`

从形状中删除任何包装器。

输入

```liquid
{% shape_clear_wrappers my_shape %}

### `shape_add_wrappers`

向形状添加包装器。

输入

```liquid
{% shape_add_wrappers my_shape "wrapper1 wrapper2" %}

{% assign my_wrappers = "wrapper1,wrapper2" | split: "," %}
{% shape_add_wrappers my_shape my_wrappers %}
```

### `shape_clear_classes`

从形状中删除任何类。

输入

```liquid
{% shape_clear_classes my_shape %}
```

### `shape_add_classes`

向形状添加类。

输入

```liquid
{% shape_add_classes my_shape "class1 class2" %}

{% assign my_classes "class1,class2" | split: "," %}
{% shape_add_classes my_shape my_classes %}
```

### `shape_clear_attributes`

从形状中删除任何属性。

输入

```liquid
{% shape_clear_attributes my_shape %}
```

### `shape_add_attributes`

向形状添加属性。

输入

```liquid
{% shape_add_attributes my_shape attr_name1: "value1", attr_name2: "value2" ... %}
```

### `shape_add_properties`

向形状添加属性。这对于从父形状传递值很有用。
属性名称转换为PascalCase。
例如：`prop_name1`可以通过`Model.Properties["PropName1"]`或`Model.Properties.PropName1`在形状模板中访问。

输入

```liquid
{% shape_add_properties my_shape prop_name1: "value1", prop_name2: 2  %}
```

### `shape_remove_property`

按名称从形状中删除属性。

输入

```liquid
{% shape_remove_property my_shape "prop_name1" %}
```

### `shape_type`

设置形状的类型。

输入

```liquid
{% shape_type my_shape "MyType" %}
```

每当更改类型时，建议先使用`shape_clear_alternates`标记清除形状备用项。

### `shape_display_type`

设置形状的显示类型。

输入

```liquid
{% shape_display_type my_shape "Summary" %}
```

每当更改显示类型时，建议先清除形状备用项。

### `shape_position`

设置形状的位置。

输入

```liquid
{% shape_position my_shape "Content:before" %}
```

### `shape_tab`

设置形状的选项卡。

输入

```liquid
{% shape_tab my_shape "properties" %}
```

### `shape_remove_item`

按名称从区域中删除形状。

输入

```liquid
{% shape_remove_item Model.Content "HtmlBodyPart" %}
{{ Model.Content | shape_render }}
```

在此示例中，`Model.Content`属性计算为区域形状，通常来自内容项形状模板，其中包含为Body Part元素呈现的`HtmlBodyPart`形状。
此调用将删除名为`HtmlBodyPart`的特定形状。

### `shape_pager`

替换Pager形状的属性。

输入

```liquid
{% shape_pager Model.Pager next_class: 'next', next_text: '>>' %}
```

### `shape_build_display`

创建内容项的显示形状。它可以与`shape_render`结合使用
呈现内容项。

输入

```liquid
{{ mycontentitem | shape_build_display: "Detail" | shape_render }}


### `shape`

创建并呈现一个新的形状，带有可选的缓存参数。

输入

```liquid
{% shape "menu", alias: "alias:main-menu", cache_id: "main-menu", cache_expires_after: "00:05:00", cache_tag: "alias:main-menu" %}
```

使用形状标记时，可以指定特定的包装器和/或替代品。

```liquid
{% shape "menu", alias: "alias:main-menu", alternate: "Menu_Footer" %}
```

### `shape_cache`

设置形状的缓存参数。

输入

```liquid
{% shape_cache my_shape cache_id: "my-shape", cache_expires_after: "00:05:00" %}
```

有关可用缓存参数的更多信息，请参见[此部分](../DynamicCache/README.md#shape-tag-helper-attributes)

### `zone`

在指定的区域中呈现一些HTML内容。

输入

```liquid
{% zone "Header" %}
    <!-- some content goes here -->
{% endzone %}
```

然后可以在布局中使用`{% render_section "Header" %}`代码重用此块的内容。

## 标签助手标记

ASP.NET Core MVC提供了一组标记助手来呈现预定义的HTML输出。 Liquid模块提供了一种使用自定义Liquid标记调用这些标记助手的方法。


### `form`

调用ASP.NET Core的`form`标记助手。

```liquid
{% form action:"Create", controller: "Todo", method: "post" %}
    ... ... ...
{% endform %}
```

### `input`

使用`helper`调用ASP.NET Core的`input`标记助手，并绑定模型的`Text`

```liquid
{% helper "input", for: "Text", class: "form-control" %}
```

### `label`

使用`helper`调用ASP.NET Core的`label`标记助手，并绑定模型的`Text`

```liquid
{% helper "label", for: "Text" %}
```

### `validation_summary`

使用`helper`调用带有`div`的ASP.NET Core的`validation_summary`标记助手

```liquid
{% helper "div", validation_summary: "All" %}
```

### `validation_for`

使用`helper`调用带有`span`的ASP.NET Core的`validation_for`标记助手，并绑定模型的`Text`

```liquid
{% helper "span", validation_for: "Text" %}
```

### `link`

从`Orchard.ResourceManagement`包调用`link`标记助手。[请参见此部分。](../Resources/README.md#link-tag)

### `meta`

从`Orchard.ResourceManagement`包调用`meta`标记助手。[请参见此部分。](../Resources/README.md#meta-tags)

### `resources`

从`Orchard.ResourceManagement`包调用`resources`标记助手。[请参见此部分。](../Resources/README.md#rendering)

### `script`

从`Orchard.ResourceManagement`包调用`script`标记助手。[请参见此部分。](../Resources/README.md#inline-definition)

### `style`

从`Orchard.ResourceManagement`包调用`style`标记助手。[请参见此部分。](../Resources/README.md#inline-definition)

### `a`

从`OrchardCore.Contents`包调用`a`内容链接标记助手。

### `route_*`
可以使用`route_*`将路由数据添加到支持使用`asp-route-*`属性的ASP.NET Core标记助手中。

在以下示例中，`route_returnUrl`将`returnUrl`添加到表单操作中。

```liquid
{% form action: "Update", method: "post",  route_returnUrl: Request.Query["returnurl"] %}
    ... ... ...
{% endform %}
```

在以下示例中，`route_todoid`将`Model.TodoId`添加到超链接中。

```liquid
{% a action: "Delete" , controller: "Todo", class: "btn btn-danger", route_todoid: Model.TodoId %}
    Delete
{% enda %}
```


### `antiforgerytoken`

呈现一个`<hidden>`元素（防伪标记），当包含的`<form>`提交时将进行验证。

例子

```liquid
{% antiforgerytoken %}
```

### `helper`和`block`

允许从Liquid中调用自定义Razor [TagHelpers]（https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-3.0）。

对于属性（HtmlAttributeName），请使用驼峰式命名法，并将所有`-`替换为`_`。

Helper标记示例

```liquid
{% helper "mycustomtag", customAttribute: "foo" %}
```

块标记示例

```liquid
{% block "mycustomtag", customAttribute: "foo" %}
{% endblock %}
```

## Razor帮手

### `LiquidToHtmlAsync`

要在Razor中将Liquid字符串模板呈现为`IHtmlContent`，请使用视图的基本`Orchard`属性上的`LiquidToHtmlAsync`帮助程序扩展方法，例如：

输入

```csharp
@await Orchard.LiquidToHtmlAsync((string)Model.ContentItem.Content.Paragraph.Content.Html)
```

在此示例中，我们假设`Model.ContentItem.Content.Paragraph.Content`表示`HtmlField`，`Html`是字段值，并将其转换为字符串，因为扩展方法不支持动态分派。

输出

```html
<p> <img src="/media/kitten.jpg" /> </p>
```

可选地，您可以传递一个类进行模型绑定。

## CREDITS

### Fluid

<https://github.com/sebastienros/fluid>  
版权所有（c）2017 Sebastien Ros  
MIT许可证




















