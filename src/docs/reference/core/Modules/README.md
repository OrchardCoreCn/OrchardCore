# 模块

Orchard Core模块库提供了一种自包含的模块化系统机制，您可以选择特定的应用程序框架，而不必受到应用程序设计的限制。

## 入门

在Visual Studio中，创建一个新的Web应用程序。

通过管理项目NuGet包将`OrchardCore.Application.Cms.Targets`安装到项目中。

接下来，在`Startup.cs`中，修改`ConfigureServices`方法，添加以下行：

```csharp
services.AddOrchardCms();
```

接下来，在`Configure`方法的末尾，将此块替换为：

```csharp
app.UseOrchardCore();
```

```note
注意：上面的代码将Hello World!替换为Orchard Core。
```

## 其他框架

您可以轻松地将您喜欢的应用程序框架添加到管道中。下面的实现是设计为并排工作的，因此如果您想在管道中使用Asp.Net Mvc和Nancy，只需添加两者即可。

下面的模块化框架包装器是设计为直接与模块化应用程序框架一起使用的，因此避免仅添加原始框架并期望它可以正常工作。

### Asp.Net Mvc

通过管理项目NuGet包将`OrchardCore.Application.Mvc.Targets`安装到项目中。

接下来，在`Startup.cs`中，修改`ConfigureServices`方法，使其如下所示：

```csharp
            // Add ASP.NET MVC and support for modules
            services
                .AddOrchardCore()
                .AddMvc()
                ;
```

!!! note
    注意：添加了`.AddMvc()`

Asp.Net Mvc现在是您管道的一部分。

您可以在此处找到一个示例应用程序：[`OrchardCore.Mvc.Web`](../../../../OrchardCore.Mvc.Web/Startup.cs)

## 配置

以下配置值默认用于模块嵌入式静态文件，并可进行自定义：

```json
    "StaticFileOptions": {
      // The CacheControl header sent with any static file served by modules
      "CacheControl": "public, max-age=2592000, s-maxage=31557600"
    }
```

> 该文档由ChatGPT 4 翻译
