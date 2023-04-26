# 自定义编码设置

默认情况下，ASP.NET MVC仅允许在不进行编码的情况下呈现ASCII字符，无论是使用`HtmlEncoder`，`UrlEncoder`还是`JavaScriptEncoder`。

不幸的是，大多数内部字符默认情况下将被编码，导致有效载荷更大，即使这在技术上是正确的。

## 禁用Unicode范围编码

ASP.NET MVC提供了一种配置这些设置的方法。Orchard Core使用DI中注册的编码器以遵守任何自定义选项。要禁用所有范围的Unicode字符编码，您可以在Web应用程序的`Startup`类的`Configure()`方法中使用此代码：

```csharp
services.Configure<WebEncoderOptions>(options => 
{
      options.TextEncoderSettings = new TextEncoderSettings(UnicodeRanges.All);
});
```

理想情况下，您应该只指定实际使用的Unicode范围。

## 在代码中使用编码器
建议使用 DI 容器中注册的编码器，而不是使用静态编码器。

不要调用 `HtmlEncode.Default` 或其他任何 `TextEncoder`，而是像这样在服务构造函数中注入一个编码器：

```csharp
public class MyService
{
    private readonly HtmlEncoder _htmlEncoder;

    public MyService(HtmlEncoder htmlEncoder)
    {
        _htmlEncoder = htmlEncoder;
    }
}
```

这将确保您为 Unicode 范围设置的设置也适用于您自己的代码。


> 该文档由Chat-GPT 翻译
