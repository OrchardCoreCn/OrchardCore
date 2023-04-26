# HTML 清理器

作为Orchard Core基础设施的一部分，提供了一个HTML清理器。

清理器清理可能导致XSS攻击的用户输入。

默认情况下，它用于以下部分和字段：

- HTML正文部分
- HTML字段
- Markdown正文部分
- Markdown字段

!!! 注意
    要在这些字段上禁用清理，请在字段或部分设置中禁用“Sanitize Html”选项。

## Razor Helper

`@Orchard.SanitizeHtml((string)Model.ContentItem.HtmlBodyPart.Html);`


## 默认配置

默认情况下，清理的元素列在此页面上：https://github.com/mganss/HtmlSanitizer#tags-allowed-by-default

Orchard Core通过以下方式更改这些默认值：

- 允许属性`class`
- 删除标签`form`

## 配置清理器

可以使用`IOptions<HtmlSanitizerOptions>`在服务注册期间使用配置扩展方法`ConfigureHtmlSanitizer`对清理器进行配置。

您可以在启动管道期间多次调用此扩展方法以更改配置。

```csharp
services
    .AddOrchardCms()
    .ConfigureServices(tenantServices =>
        tenantServices.ConfigureHtmlSanitizer((sanitizer) =>
            {
                sanitizer.AllowedSchemes.Add("mailto");
            }));
```

有关选项，请参见https://github.com/mganss/HtmlSanitizer。
> 该文档由ChatGPT 4 翻译
