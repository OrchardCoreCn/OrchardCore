# 创建一个模块化的 ASP.NET Core 应用程序

## 你将要构建什么

您将创建一个模块化的 ASP.NET Core MVC 网络应用程序，类似于包含在 Orchard Core 中的 "Hello World" 应用程序示例。它包括一个网络应用程序和一个模块。网络应用程序提供了布局，而模块注册了路由并响应首页请求。您可以参考 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 中的以下项目以获取更多信息。

- src/OrchardCore.Mvc.Web
- src/OrchardCore.Modules/OrchardCore.Mvc.HelloWorld

## 所需材料

- 当前版本的 .NET SDK。您可以从此处下载: <https://dotnet.microsoft.com/download>.
- 一个文本编辑器和一个终端，您可以在其中运行 dotnet CLI 命令。

## 创建 Orchard Core 网站和模块

有不同的方法可以为 Orchard Core 创建网站和模块。您可以在 [此处](../../getting-started/templates/README.md) 了解更多信息。

在本指南中，我们将使用我们的[代码生成模板](../../getting-started/templates/)。您可以使用以下命令安装模板的最新稳定版本：
```dotnet new install OrchardCore.ProjectTemplates::1.5.0-*```

!!! note
    如果想使用模板的开发分支，请添加 `--nuget-source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

创建一个名为`OrchardCore.Mvc`的空文件夹，它将包含我们的项目。打开一个终端，进入该文件夹并运行以下命令来创建Web应用程序：

```dotnet new ocmvc -n OrchardCore.Mvc.Web```

接下来，创建“ Hello World”模块。

```dotnet new ocmodulemvc -n OrchardCore.Mvc.HelloWorld```
 
将Web应用程序中指向该模块的项目引用添加到其中。

```dotnet add OrchardCore.Mvc.Web reference OrchardCore.Mvc.HelloWorld```

可选地，您可以添加一个解决方案文件，该文件引用了Web应用程序和模块，以便在Visual Studio中打开解决方案。
```
dotnet new sln -n OrchardCore.Mvc
dotnet sln add OrchardCore.Mvc.Web\OrchardCore.Mvc.Web.csproj
dotnet sln add OrchardCore.Mvc.HelloWorld\OrchardCore.Mvc.HelloWorld.csproj
```

## 测试生成的应用程序

从包含两个项目的`OrchardCore.Mvc`根文件夹中运行以下命令启动Web应用程序：

`dotnet run --project .\OrchardCore.Mvc.Web\OrchardCore.Mvc.Web.csproj`

!!! 注意
    如果您正在使用模板的开发分支，请在运行应用程序之前运行`dotnet restore .\MySite\MySite.csproj source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

现在，您的应用程序应该在以下端口上运行和侦听：
```
现在侦听：https://localhost:5001
```
现在在http://localhost:5000上进行监听
应用程序已启动。按Ctrl+C关闭。

```

打开浏览器并导航至<https://localhost:5001/OrchardCore.Mvc.HelloWorld/Home/Index>。它应该会显示__Hello from OrchardCore.Mvc.HelloWorld__。

> 布局来自主Web应用程序项目，而控制器、操作和视图来自模块项目。

## 注册自定义路由

默认情况下，模块中的所有路由都遵循模式`{area}/{controller}/{action}`,其中 `{area}` 是模块名称。我们将更改模块中视图的路由以响应主页请求。

在`OrchardCore.Mvc.HelloWorld`的`Startup.cs`文件中，在`Configure()`方法中添加一个自定义路由。

```csharp
    routes.MapAreaControllerRoute（
        name：“Home”，
        areaName：“OrchardCore.Mvc.HelloWorld”，
        pattern：“”，
        defaults：new {controller =“Home”，action =“Index”}
```
# 在Orchard Core中创建模块应用程序

在Orchard Core中，一个模块是指封装在自己的项目中的功能区块。这些模块可以通过应用程序进行加载和卸载，可以根据需要启用或禁用。在本教程中，您将学习如何创建一个用于 Orchard Core 的 ASP.NET Core MVC 模块应用程序。

## 先决条件

* .NET Core SDK 3.1或更高版本。
* Visual Studio 2019或更高版本。

## 步骤

### 1. 创建一个新的网站

* 打开 Visual Studio 并选择 **创建新项目**。
* 在左侧选择 **ASP.NET Core Web 应用程序**。
* 在右侧选择 **Web 应用程序**。
* 输入项目名称和位置，然后单击 **创建**。

### 2. 安装 Orchard Core

* 打开命令提示符。
* 执行以下命令：

```
dotnet new -i OrchardCore.ProjectTemplates::1.0.0-*
```

* 创建一个名为 **OrchardCore.Web** 的新文件夹，并在其中运行以下命令：

```
dotnet new ocmvc -n YourModuleName
```

* 完成后，切换到新的文件夹，然后运行以下命令启动应用程序：

```
dotnet run
```

* 导航到 <https://localhost:5001> 来查看网站。

### 3. 创建一个模块

* 在新文件夹的根目录中，创建一个名为 **Modules** 的文件夹。
* 在 **Modules** 文件夹中创建一个名为 **YourModuleName** 的新文件夹。
* 在 **YourModuleName** 文件夹中，创建一个新的 **.NET Core 类库** 项目。
* 选择 **netstandard2.0** 作为目标框架。
* 将生成的默认文件删除。
* 打开 **YourModuleName.csproj** 文件并添加以下 Orchard Core引用：

```xml
<ItemGroup>
  <PackageReference Include="OrchardCore.Module.Targets" Version="1.0.0-*" PrivateAssets="All" />
  <PackageReference Include="OrchardCore.Mvc.Targets" Version="1.0.0-*" PrivateAssets="All" />
</ItemGroup>
```

*右键单击项目并选择 **添加** -> **新项目文件夹**。该目标文件夹应该是你的 **Views** 因此输入 **Views** 作为目录名称。
*添加 **Home** 目录和 **Index.cshtml** 视图文件。
*打开 **Startup.cs** 文件并将以下代码添加到 **ConfigureServices** 方法的末尾：

```csharp
services.AddMvc().AddOrchardCoreMvc();
```

这将向依赖注入容器注册OrchardCore MVC的必要服务。
*现在，要将新的模块添加到 Orchard Core应用程序中，请打开 **OrchardCore.Web.csproj** 文件并添加以下行：

```xml
<ItemGroup>
  <ProjectReference Include="..\Modules\YourModuleName\YourModuleName.csproj" />
</ItemGroup>
```

* 重启应用程序，并导航到 <https://localhost:5001> 将显示 Orchard Core的默认欢迎页面。

### 4. 更改默认欢迎页面

* 打开 **YourModuleName** 项目的 **Controllers** 文件夹。
* 创建一个名为 **HomeController.cs** 的新文件并输入以下内容：

```csharp
using Microsoft.AspNetCore.Mvc;

namespace YourModuleName.Controllers
{
  public class HomeController : Controller
  {
    public ActionResult Index()
    {
      return View();
    }
  }
}
```

这将创建一个名为 **Index** 的动作，该动作在呈现视图时显示。
* 将以下代码复制到您的视图文件（**Views/Home/Index.cshtml**）中以显示 __Hello World__：

```html
<h1>Hello World</h1>
```

* 重启应用程序并导航到 <https://localhost:5001> 将显示 __Hello World__ 消息。

## 总结

您刚刚创建了一个使用Orchard Core的模块化ASP.NET Core MVC web应用程序。 它包括一个提供布局的Web应用程序和一个响应主页请求的自定义模块。

## 教程

<https://www.youtube.com/watch?v=LoPlECp31Oo>


> 该文档由Chat-GPT 翻译
