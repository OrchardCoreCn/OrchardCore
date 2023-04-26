
# 创建Orchard Core CMS网站

在本指南中，您将使用项目模板将Orchard Core设置为内容管理系统。

## 您需要什么

- .NET SDK的当前版本。您可以从以下网址下载它：<https://dotnet.microsoft.com/download>.
- 文本编辑器和终端，您可以在其中键入 dotnet 命令。

## 创建项目

有多种创建Orchard Core网站和模块的方法。您可以在[这里](../../getting-started/templates/README.md)了解更多信息。

在本指南中，我们将使用“代码生成模板”。您可以使用此命令安装最新稳定版本的模板：

```dotnet new install OrchardCore.ProjectTemplates::1.5.0-*```

!!! 注意

    要使用模板的开发分支，请添加 `--nuget-source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`
创建一个空文件夹来存放你的网站。打开终端，进入该文件夹并运行以下命令：

```dotnet new occms -n MySite```

这样就在一个名为 `MySite` 的文件夹中创建了一个新的Orchard Core CMS项目。

## 设置站点

应用程序已经由模板创建，但尚未设置。

通过执行以下命令启动应用程序：

`dotnet run --project .\MySite\MySite.csproj`

!!! note
    如果你正在使用模板的开发分支，请在运行应用程序之前运行`dotnet restore .\MySite\MySite.csproj --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`。


现在您的应用程序应该正在运行，并监听以下端口：
现在监听 on: https://localhost:5001 和 on: http://localhost:5000。应用已经启动，按 Ctrl+C 可以关闭应用。

在浏览器中打开 https://localhost:5001 可以显示设置屏幕。

为了演示的目的，我们将使用 Blog 配方创建网站。Blog 配方是 Orchard Core 的 [入门配方](../../getting-started/starter-recipes.md)之一，其中包含一系列功能和配置 Orchard Core 网站的步骤。

完成设置表单，选择 Blog 配方和 SQLite 数据库。

![image](assets/setup-screen.jpg)

提交表单后，几秒钟后您将可以看到一个博客站点。

![image](assets/blog-home-page.jpg)

为了配置并开始编写内容，您可以转到 https://localhost:5001/admin。
## 概要

您刚刚创建了一个由Orchard Core CMS驱动的博客引擎。

