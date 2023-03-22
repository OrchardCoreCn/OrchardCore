# 从模块向管理导航添加菜单项

“INavigationProvider”接口是与处理管理导航菜单项相关的每个任务的入口点。 
为了从模块中添加菜单项，只需创建一个实现该接口的类。

## 你要建造什么

您将构建一个模块，该模块将在根级别添加一个菜单项和两个子菜单项。
每个菜单项将指向自己的视图。

## 你需要什么

- The current version of the .NET SDK. You can download it from here <https://dotnet.microsoft.com/download>.
- A text editor and a terminal where you can type dotnet commands.

## 创建一个Orchard CoreCMS网站和模块

有不同的方法来创建网站和模块的Orchard Core。 你可以在[这里](../../getting-started/templates/README.md)了解更多. 在本指南中，我们将使用我们的“代码生成模板”。

可以使用以下命令安装最新发布的模板：

```dotnet new install OrchardCore.ProjectTemplates::1.5.0-*```

!!! note
    要使用模板的开发分支请添加 `--nuget-source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

创建一个包含站点的空文件夹。打开终端，导航到该文件夹并运行以下命令：

```dotnet new occms -n MySite```

这将在 `MySite` 文件夹下创建一个新的Orchard Core CMS 站点.
现在可以使用以下命令创建新模块：

```dotnet new ocmodulecms -n MyModule```

该模块在“MyModule”文件夹中创建。
下一步添加模块引用：

```dotnet add MySite reference MyModule```

我们还需要参考`OrchardCore.Admin`包以便能够实现所需的接口：

```dotnet add .\MyModule\MyModule.csproj package OrchardCore.Admin --version 1.5.0-*```

!!! note
    To use the development branch of the template add ` --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json --version 1.5.0-*`

## 添加控制器和视图

### 添加控制器

创建`DemoNavController.cs`文件保存到“.\MyModule\Controllers”文件夹，其中包含以下内容：

#### DemoNavController.cs

```csharp
using Microsoft.AspNetCore.Mvc;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using OrchardCore.Admin;

namespace MyModule.Controllers
{
    [Admin]
    public class DemoNavController : Controller
    {
        public ActionResult ChildOne()
        {
            return View();
        }

        public ActionResult ChildTwo()
        {
            return View();
        }
    }
}
```

!!! tip
   “[Admin]”属性确保控制器正在使用管理主题，并且用户有权访问它。
   另一种方法是将这个类命名为“AdminController”。

### 添加视图

创建文件夹“.\MyModule\Views\DemoNav”，并将以下两个文件添加到其中：

#### ChildOne.cshtml

```html
<p>View One</p>
```

#### ChildTwo.cshtml

```html
<p>View Two</p>
```

## 添加菜单项

现在只需添加一个实现“INavigationProvider”接口的类。
按照惯例，我们称这些类为`AdminMenu.cs`把它放在我们模块文件夹的根目录下。

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
        private readonly IStringLocalizer S;

        public AdminMenu(IStringLocalizer<AdminMenu> localizer)
        {
            S = localizer;
        }

        public Task BuildNavigationAsync(string name, NavigationBuilder builder)
        {
            // We want to add our menus to the "admin" menu only.
            if (!String.Equals(name, "admin", StringComparison.OrdinalIgnoreCase))
            {
                return Task.CompletedTask;
            }

            // Adding our menu items to the builder.
            // The builder represents the full admin menu tree.
            builder
                .Add(S["My Root View"], S["My Root View"].PrefixPosition(),  rootView => rootView               
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
    如果要在将字符串翻译为其他语言时保持字母排序，建议对第二个参数（`position`）使用“PrefixPosition”扩展方法。

然后您必须在`Startup.cs`模块的文件。

在顶部`Startup.cs`文件中，添加以下“using”语句：

```csharp
using OrchardCore.Navigation;
```

Add this line to the `ConfigureServices()` method:

```csharp
services.AddScoped<INavigationProvider, AdminMenu>();
```

## Testing the resulting application

From the root of the folder containing both projects, run this command:

`dotnet run --project .\MySite\MySite.csproj`

!!! note
    如果您使用的是模板的开发分支，请运行 `dotnet restore .\MySite\MySite.csproj --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json` before running the application

您的应用程序现在应该正在运行在下列端口：

```
Now listening on: https://localhost:5001
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

打开浏览器 <https://localhost:5001>

如果尚未设置站点，请选择“空白站点（Blank Site）”作为配方，并使用“SQLite”作为数据库。

一旦你的网站准备好了，你应该会看到一条“找不到页面”的消息，这是一条“空白网站配方”的正常消息。

Enter the Admin section by opening <https://localhost:5001/admin> and logging in.
打开进入管理后台<https://localhost:5001/admin>并登录。

使用左侧菜单转到“设置>功能”，搜索您的模块“MyModule”，然后启用它。

现在您的模块已启用，您应该会看到一个关于管理的新条目。
单击新菜单项以渲染我们先前创建的视图。

## 摘要

您刚刚学习了如何向管理导航添加菜单项。
