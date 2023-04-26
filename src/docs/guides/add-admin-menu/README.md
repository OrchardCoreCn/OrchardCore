# 如何从模块向管理导航中添加菜单项

`INavigationProvider` 接口是处理管理导航菜单项所有任务的入口点。为了从你的模块中添加菜单项，你只需要创建一个实现该接口的类。

## 你需要构建什么

你将构建一个模块，该模块将在根目录下添加一个菜单项和两个子菜单项。每个菜单项都将指向其自身的视图。

## 你需要什么

- .NET SDK 的当前版本。你可以从 <https://dotnet.microsoft.com/download> 下载它。
- 一个文本编辑器和一个可以输入 dotnet 命令的终端。


## 创建 Orchard Core CMS 站点和模块

有不同的方法可以为 Orchard Core 创建站点和模块。你可以在[这里](../../getting-started/templates/README.md)了解更多。在本指南中，我们将使用我们的“代码生成模板”。

使用以下命令安装最新版本的模板：
```dotnet new install OrchardCore.ProjectTemplates::1.5.0-*```

!!! 注意
    要使用模板的开发分支，请添加`--nuget-source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

创建一个空文件夹，用于保存您的站点。打开一个终端，导航到该文件夹并运行以下命令：

```dotnet new occms -n MySite```

这将在名为`MySite`的新文件夹中创建一个新的Orchard Core CMS站点。
现在我们可以使用以下命令创建一个新模块：

```dotnet new ocmodulecms -n MyModule```

该模块会被创建在'MyModule'文件夹中。
下一步是通过添加项目引用，将该模块从应用程序中引用：

```dotnet add MySite reference MyModule```
为了能够实现所需的接口，我们还需要引用 `OrchardCore.Admin` 包：
 

```dotnet add .\MyModule\MyModule.csproj package OrchardCore.Admin --version 1.5.0-*```

!!! note
    要使用模板的开发分支，请添加 ` --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json --version 1.5.0-*`

## 添加控制器和视图

### 添加控制器

在 `.\MyModule\Controllers` 文件夹下创建一个名为 `DemoNavController.cs` 的文件，并添加以下内容：


#### DemoNavController.cs

```csharp
using Microsoft.AspNetCore.Mvc;
using System;
using System.Collections.Generic;
```
使用 System.Linq、System.Text 和 System.Threading.Tasks 命名空间。
使用 OrchardCore.Admin 命名空间。
定义了一个名为 DemoNavController 的控制器类，派生自 Controller 类。
通过 [Admin] 特性将该控制器标记为 Orchard 后台管理页面中的一部分。
该控制器有两个公共方法 ChildOne 和 ChildTwo，均返回 ActionResult 类型并返回一个视图。
## 添加控制器和视图

### 添加控制器

在模块项目上右键点击，选择`Add->New Scaffolded Item...`。

在左侧面板中选择 MVC->Controller->MVC Controller with views, using Entity Framework。

在`Add Controller`界面中，给控制器类命名为`DemoNavController`，用于控制演示导航的子页面视图。

![](https://docs.microsoft.com/zh-cn/dotnet/architecture/modern-web-apps-azure/media/image6-9.png)

接下来，选择`MyModule.Data.Models.DemoNavViewModel (MyModule.Data)`作为Model class，并将`Data context class`设置为`MyModuleDbContext (MyModule.Data)`。

将`Controller name`设置为`DemoNavController`，选择`Views using Razor (there will be a view folder created with this option)`作为`Template`。

> 注意：本示例使用了`MyModuleDbContext`进行数据操作。如果你使用的是不同的上下文类，请替换为自己的。

单击 `Add` 以添加控制器。

您的`DemoNavController.cs`应该是这样的：

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;

namespace MyModule.Controllers
{
    [Authorize(Policy = "IsAdmin")]
    public class DemoNavController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }
    }
}
```

!!! tip
   属性 `[Authorize]` 确保控制器使用了管理主题，且用户有权限访问它。
   另一种实现此行为的方法是将类名命名为 `AdminController`。

### 添加视图

创建一个名为`.\MyModule\Views\DemoNav`文件夹，在其中添加以下两个文件：

#### Index.cshtml

```html
@model IEnumerable<MyModule.Data.Models.DemoNavViewModel>

<h2>Demo Nav</h2>
<ul>
    @foreach (var item in Model)
    {
        <li><a asp-controller="DemoNav" asp-action="Index" asp-route-nodeid="@item.NodeId">@item.DisplayText</a></li>
    }
</ul>
```

#### ChildOne.cshtml

```html
<p>视图1</p>
```

#### ChildTwo.cshtml

```html
<p>视图2</p>
``` 

如何修改为自己管理主题所需的样式和 HTML 取决于您的具体需求。
</p>
<p>视图二</p>
```

## 添加菜单项


现在你只需要添加一个实现`INavigationProvider`接口的类。  
按照惯例，我们将这些类命名为`AdminMenu.cs`并将其放在模块文件夹的根目录中。

### AdminMenu.cs

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Localization;
using OrchardCore.Navigation;

namespace MyModule
{
    public class AdminMenu : INavigationProvider
    {
私有只读字段 IStringLocalizer S;

public AdminMenu(IStringLocalizer<AdminMenu> localizer)
{
    S = localizer;
}

public Task BuildNavigationAsync(string name, NavigationBuilder builder)
{
    // 我们只希望将菜单添加到“管理”菜单中。
    if (!String.Equals(name, "admin", StringComparison.OrdinalIgnoreCase))
    {
        return Task.CompletedTask;
    }

    // 将菜单项添加到构建器中。
    // 构建器表示完整的管理菜单树。
    builder 
        .Add(S["My Root View"], S["My Root View"].PrefixPosition(), rootView => rootView 
            .Add(S["Child One"], S["Child One"].PrefixPosition(), childOne => childOne
首先，您需要创建一个继承自 `INavigationProvider` 的类。这个类将被用来定义菜单项。假设我们正在为一个名为 `MyModule` 的模块创建菜单项。下面是示例代码：

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Localization;
using OrchardCore.Navigation;

namespace MyModule
{
    public class AdminMenu : INavigationProvider
    {
        private readonly IStringLocalizer S;

        public AdminMenu(IStringLocalizer<AdminMenu> stringLocalizer)
        {
            S = stringLocalizer;
        }

        public Task BuildNavigationAsync(string name, NavigationBuilder builder)
        {
            if (!string.Equals(name, "admin", StringComparison.OrdinalIgnoreCase))
            {
                return Task.CompletedTask;
            }

            builder
                .Add(S["MyModule"], S["MyModule"].PrefixPosition(), module => module
                    .Add(S["Child One"], S["Child One"].PrefixPosition(), childOne => childOne
                        .Action("ChildOne", "DemoNav", new { area = "MyModule"}))
                    .Add(S["Child Two"], S["Child Two"].PrefixPosition(), childTwo => childTwo
                        .Action("ChildTwo", "DemoNav", new { area = "MyModule"})));

            return Task.CompletedTask;
        }
    }
}
```

!!! note
    我们建议在第二个参数（`position`）中使用 `PrefixPosition` 扩展方法，以便在翻译为其他语言时保持字母顺序。

然后，您需要在模块的 `Startup.cs` 文件中注册此服务。

在 `Startup.cs` 文件的顶部添加此 `using` 语句：

```csharp
using OrchardCore.Navigation;
```
在`ConfigureServices()`方法中添加以下代码行：

```csharp
services.AddScoped<INavigationProvider, AdminMenu>();
```

## 测试生成的应用程序

从包含两个项目的根目录运行以下命令:

`dotnet run --project .\MySite\MySite.csproj`

!!! note
    如果您正在使用模板的开发分支，请在运行应用程序之前运行`dotnet restore .\MySite\MySite.csproj --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

您的应用程序现在应该在以下端口上运行：

```
Now listening on: https://localhost:5001
```
现在正在监听：http://localhost:5000
应用已启动。按Ctrl+C关闭。

在浏览器中打开<https://localhost:5001>

如果您尚未设置站点，请选择“空白站点”作为配方，并将数据库使用“SQLite”。

一旦您的站点准备就绪，您应该会看到一个“找不到页面”的消息，这在“空白站点”配方中是可以预期的。

通过打开<https://localhost:5001/admin>并登录，进入管理部分。

使用左侧菜单转到“配置：功能”，搜索您的模块“MyModule”，并启用它。

现在，您的模块已启用，并且您应该在管理中看到一个新条目。单击新菜单项以呈现我们之前创建的视图。

## 概要

您刚学会了如何在管理导航中添加菜单项。


