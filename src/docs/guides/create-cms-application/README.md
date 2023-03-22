# 创建一个 Orchard Core CMS 网站

在本指南中，您将从项目模板将Orchard Core设置为内容管理系统。

## 您需要什么

- The current version of the .NET SDK. You can download it from here <https://dotnet.microsoft.com/download>.
- A text editor and a terminal where you can type dotnet commands.

## 创建项目

There are different ways to create sites and modules for Orchard Core. You can learn more about them [here](../../getting-started/templates/README.md).  

In this guide we will use our "Code Generation Templates". You can install the latest stable release of the templates using this command:

```dotnet new install OrchardCore.ProjectTemplates::1.5.0-*```

!!! 注意
    要使用开发分支请添加 `--nuget-source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

Create an empty folder that will contain your site. Open a terminal, navigate to that folder and run the following command:

```dotnet new occms -n MySite```

这将在名为 的文件夹中创建一个新的 Orchard Core CMS 项目。 `MySite`.

## 设置网站

应用程序已由模板创建，但尚未设置。

通过执行此命令运行应用程序：

`dotnet run --project .\MySite\MySite.csproj`

!!! note
    如果使用模板的开发分支，请先执行 `dotnet restore .\MySite\MySite.csproj --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json` before running the application

Your application should now be running and listening on the following ports:

```
Now listening on: https://localhost:5001
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Open a browser and navigate to <https://localhost:5001> to display the setup screen.

For demonstration purposes, we will create the website using the __Blog__ recipe. The __Blog__ recipe is a [starter recipe](../../getting-started/starter-recipes.md) included with Orchard Core that contains a list of features and steps to configure an Orchard Core website.

Complete the setup form and select the __Blog__ recipe and __SQLite__ for the database.

![image](assets/setup-screen.jpg)

提交表单。几秒钟后，你应该看一个博客网站。

![image](assets/blog-home-page.jpg)

为了配置它并开始编写内容，您可以转到 <https://localhost:5001/admin>.

## 总结

您刚刚创建了一个Orchard Core CMS 驱动的博客引擎。
