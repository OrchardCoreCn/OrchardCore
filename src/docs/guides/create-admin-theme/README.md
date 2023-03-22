# 创建自定义管理员主题

可以为 Orchard Core 创建自定义管理员主题。

Orchard Core 的默认管理员主题称为 `TheAdmin`。


## 你将要创建什么

你将创建一个使用 `TheAdmin` 作为基础主题的自定义主题。


## 你需要什么

- 已经设置好的现有 Orchard Core 网站。


## 创建 Orchard Core 主题

请按照 [创建主题](../../getting-started/theme.md) 指南创建一个 Orchard Core 主题。


## 编辑Manifest.cs

在您的主题的根目录中，将会有一个名为`Manifest.cs`的文件。

编辑这个文件：

```csharp
using OrchardCore.DisplayManagement.Manifest;

[assembly: Theme(
    Name = "MyAdminTheme",
    Author = "My name",
    Website = "https://mywebsite.net",
    Version = "0.0.1",
    Description = "My Orchard Core 管理主题。",
    Tags = new [] { "admin" },
    BaseTheme = "TheAdmin"
)]
```


添加属性 `Tags = new [] { "admin" }` 和属性 `BaseTheme = "TheAdmin"`

该标签允许选择该主题作为管理主题。

`BaseTheme` 属性的意思是当自定义管理主题处于活动状态时，Orchard Core Display Management会在显示管理内容时同时查找 `TheAdmin`主题和`MyAdminTheme`的模板备选项。

`MyAdminTheme`中的视图将覆盖`TheAdmin`中的视图。

## 启用自定义管理主题

从包含两个项目的根目录中运行以下命令：

`dotnet run --project .\MySite\MySite.csproj`

!!! note
    如果您正在使用模板的开发分支，请在运行应用程序之前运行 `dotnet restore .\MySite\MySite.csproj --source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json`

您的应用程序现在应该正在运行并包含开放端口：
```
现在正在监听: https://localhost:5001
现在正在监听: http://localhost:5000
应用程序已启动。按Ctrl + C以关闭。
```

在浏览器中打开< https://localhost:5001>

通过打开< https://localhost:5001/admin>并登录进入管理部分。

使用左侧菜单转到“设计->主题”，搜索您的主题“ MyAdminTheme”，然后选择“使当前”。

现在您的管理主题已启用。

从这里，您可以创建模板或使用“placement.json”来更改在管理中呈现的形状。

## 摘要

您刚学会了如何创建自定义管理员主题。


> 该文档由Chat-GPT 翻译
