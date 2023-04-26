# 开始创建Orchard Core主题

在本文中，我们将通过将其添加到现有的Orchard Core CMS应用程序中[先前创建](README.md)，来创建一个Orchard Core主题。

## 创建Orchard Core主题

- 安装[Code Generation Templates](templates/README.md)
- 创建一个文件夹，并与之前创建的应用程序文件夹并列（*不要*在里面），文件夹名为主题名称（例如：`MyTheme.OrchardCore`）。最好在`OrchardCore.Themes`目录下创建一个新文件夹。打开它。我们将在这个文件夹中创建一个新项目。
- 执行命令`dotnet new octheme`
- 在Visual Studio中，将新创建的主题项目添加到解决方案中，然后从主要的Orchard Core CMS Web应用程序中添加对该项目的引用。
- 在Visual Studio Code或CLI中，执行命令`dotnet sln add MyTheme.OrchardCore.csproj`添加项目到解决方案中。然后，转到`OrchardCore.Cms.Web`文件夹并执行命令`dotnet add OrchardCore.Cms.Web.csproj reference ../OrchardCore.Themes/MyTheme.OrchardCore.csproj`
- 将主要的 Orchard Core CMS Web 应用程序设置为启动项目。

缩略图也可以通过在`wwwroot`文件夹中添加`Theme.png`来创建。

![image](assets/MyTheme.png)

主题属性可以在__Manifest.cs__文件中更改：

```csharp## 如何创建自己的 Orchard Core 主题？

要创建自己的 Orchard Core 主题，请按照以下步骤：

1. 在 Visual Studio 中创建一个新项目，并选择“ASP.NET Core Web 应用程序”。

2. 给项目命名，然后点击“创建”按钮。

3. 在“创建新 ASP.NET Core Web 应用程序”弹窗中选择“空”的 Web 应用程序，然后点击“创建”按钮。

4. 添加对“OrchardCore.DisplayManagement”NuGet 包的引用。

5. 添加下面的 “Theme.cs” 文件，其中包含有关主题名称、作者、网站、版本和描述等详细信息。

   ```
   using OrchardCore.DisplayManagement.Manifest;

   [assembly: Theme(
       Name = "MyTheme",
       Author = "My name",
       Website = "https://mywebsite.net",
       Version = "0.0.1",
       Description = "My Orchard Core Theme description."
   )]
   ```

   该主题应该会在“Active themes”管理页面中出现，并且可以设置为默认主题。

6. 如何在我的主题中启用 Razor 模板？

   我们在源代码中拥有的主题仅使用“Liquid”文件，因此它们的 .csproj 文件只引用：

   `<Project Sdk="Microsoft.NET.Sdk">`

   如果您想在自己的主题中使用 Razor 模板，您只需要将此 .csproj 文件的第一行更改为：`<Project Sdk="Microsoft.NET.Sdk.Razor">` 是一个用来定义 ASP.NET Core Razor Pages 项目的 XML 元素，指定了项目使用的SDK为Microsoft.NET.Sdk.Razor。Razor Pages是一种基于视图的Web框架，它允许使用Razor语法以简单和直观的方式创建Web页面。Microsoft.NET.Sdk.Razor是一个工具套件，可为Razor项目提供构建和部署基础架构。我明白，有任何需要翻译的 Markdown 文档请提供。好的，请输入需要翻译的英文Markdown文档。好的，明白。如果您需要帮助请随时告诉我。
$END_OF_FILE$