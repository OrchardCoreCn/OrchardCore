# 创建模块化ASP.NET Core 应用程序

## 你将构建什么

You will build a modular ASP.NET Core MVC web application similar to the sample "Hello World" application included with Orchard Core. It includes a web application and a module. The web application provides the layout while the module registers a route and responds to homepage requests. You can refer to the following projects in [Orchard Core](https://github.com/OrchardCMS/OrchardCore) for more information.

- src/OrchardCore.Mvc.Web
- src/OrchardCore.Modules/OrchardCore.Mvc.HelloWorld

## 你需要准备什么

- The current version of the .NET SDK. You can download it from here <https://dotnet.microsoft.com/download>.
- A text editor and a terminal where you can run dotnet CLI commands.

## Creating an Orchard Core site and module

There are different ways to create sites and modules for Orchard Core. You can learn more about them [here](../../getting-started/templates/README.md).

In this guide we will use our [Code Generation Templates](../../getting-started/templates/). You can install the latest stable release of the templates using this command:

```dotnet new install OrchardCore.ProjectTemplates::1.5.0-*```

!!! 注意
    使用模板的开发版分支需要添加 `--nuget-source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

Create an empty folder, called `OrchardCore.Mvc`, that will contain our projects. Open a terminal, navigate to that folder and run the following command to create the web application:

```dotnet new ocmvc -n OrchardCore.Mvc.Web```

Next, create the "Hello World" module.

```dotnet new ocmodulemvc -n OrchardCore.Mvc.HelloWorld```
 
Add a project reference to the web application that points to the module.

```dotnet add OrchardCore.Mvc.Web reference OrchardCore.Mvc.HelloWorld```

Optionally, you can add a solution file that references both the web application and module in case you want to open a solution in Visual Studio.

```
dotnet new sln -n OrchardCore.Mvc
dotnet sln add OrchardCore.Mvc.Web\OrchardCore.Mvc.Web.csproj
dotnet sln add OrchardCore.Mvc.HelloWorld\OrchardCore.Mvc.HelloWorld.csproj
```

## 测试生成的应用程序

From the `OrchardCore.Mvc` root folder containing both projects, run the following command to start the web application:

`dotnet run --project .\OrchardCore.Mvc.Web\OrchardCore.Mvc.Web.csproj`

!!! 注意
    如果您使用的是模板的开发分支, 运行程序前，先执行：
    
     `dotnet restore .\MySite\MySite.csproj --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json` 

Your application should now be running and listening on the following ports:

```
Now listening on: https://localhost:5001
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Open a browser and navigate to <https://localhost:5001/OrchardCore.Mvc.HelloWorld/Home/Index>. It should display __Hello from OrchardCore.Mvc.HelloWorld__.

> The Layout is from the main web application project, while the controller, action and view are from the module project.

## 注册自定义路由

By default, all routes in modules follow the pattern `{area}/{controller}/{action}`, where `{area}` is the name of the module. We will change the route of the view in the module to respond to homepage requests.

In the `Startup.cs` file of `OrchardCore.Mvc.HelloWorld`, add a custom route in the `Configure()` method.

```csharp
    routes.MapAreaControllerRoute(
        name: "Home",
        areaName: "OrchardCore.Mvc.HelloWorld",
        pattern: "",
        defaults: new { controller = "Home", action = "Index" }
    );
```

You can also change the `Index.cshtml` file in the module's `Views` -> `Home` folder so that it displays __Hello World__ similar to the project in Orchard Core.

```html
<h1>Hello World</h1>
```

Restart the application and navigate to the homepage at <https://localhost:5001> to display the __Hello World__ message.

## 摘要

You just created a modular ASP.NET Core MVC web application using Orchard Core. It includes a web application that supplies the layout and a custom module that responds to homepage requests.

## 视频讲解

<https://www.youtube.com/watch?v=LoPlECp31Oo>
