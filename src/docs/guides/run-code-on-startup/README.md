# 如何在模块启动时运行任务

`Startup` 类用于初始化服务和中间件，会在租户初始化时被调用。

接口 `OrchardCore.Modules.IModularTenantEvents` 提供了一种定义用户代码的方式，该代码在第一次访问租户时（租户激活）将被执行。

所有租户都是延迟加载的，这意味着当应用程序启动时，不会调用事件处理程序。相反，它们会在处理第一个请求时调用。

在下面的示例中，类 `MyStartupTaskService` 继承自 `ModularTenantEvents` 来实现 `IModularTenantEvents`。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;
using OrchardCore.Modules;


```
public class MyStartupTaskService : ModularTenantEvents
{
    private readonly ILogger<MyStartupTaskService> _logger;

    public MyStartupTaskService(ILogger<MyStartupTaskService> logger)
    {
        _logger = logger;
    }

    public override Task ActivatingAsync()
    {
        _logger.LogInformation("一个租户已被激活。");

        return Task.CompletedTask;
    }
}
```

然后在模块的__Startup.cs__文件的`ConfigureServices()`方法中注册该类。
```csharp
services.AddScoped<IModularTenantEvents, MyStartupTaskService>();
```

!!! note
    `ActivatingAsync`事件按照它们的注册顺序执行，该顺序来自模块的依赖项图。`ActivatedAsync`事件按相反顺序执行。

当从终端运行时，在处理第一个请求后，您应该看到以下输出：

```
info: MyStartupTaskService[0]
      租户已激活。
```


> 该文档由Chat-GPT 翻译
