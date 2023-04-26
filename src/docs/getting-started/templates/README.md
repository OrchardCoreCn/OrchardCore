# 代码生成模板

Orchard Core Templates使用 `dotnet new` 模板配置从命令行创建新的网站、主题和模块。

有关 `dotnet new` 的更多信息，请访问 <https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new>

## 安装Orchard CMS模板

一旦安装了.NET Core SDK，请输入以下命令以安装创建Orchard Core Web应用程序的模板：

```CMD
dotnet new install OrchardCore.ProjectTemplates::1.5.0
```

这将使用Orchard Core的最稳定版本。为了使用Orchard Core最新的 `main` 分支，可以使用以下命令：

```CMD
dotnet new install OrchardCore.ProjectTemplates::1.5.0-* --nuget-source https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json  
```
## 创建一个新网站

### 从命令行 (自动化方式)

#### 生成 Orchard Cms Web 应用程序

!!! 警告
    由于当前发布版本中存在一个缺陷，以下 `dotnet new` 命令将需要额外的参数 `--orchard-version 1.5.0`。例如，使用 `dotnet new occms` 代替 `dotnet new occms --orchard-version 1.5.0`

```CMD
dotnet new occms
```

上面的命令将使用默认选项。

您可以传递以下 CLI 参数来设置选项：

```CMD
Orchard Core Cms Web App (C#)
```
作者:Orchard项目
选项:
  -lo|--logger          配置记录器组件。
                             nlog       - 将NLog配置为记录器组件。
                             serilog    - 将Serilog配置为记录器组件。
                             none       - 不使用记录器。
                         默认值:nlog

  -ov|--orchard-version 指定要使用的Orchard Core软件包的版本。
                         字符串-可选
                         默认值:1.5.0
```

使用以下命令可以忽略日志记录:

```CMD
dotnet new occms --logger none
```

#### 生成一个模块化ASP.NET MVC Core Web应用程序
```CMD
dotnet new ocmvc  
```

### 从 Visual Studio (新建项目对话框)

这些模板也可以从 Visual Studio 的新建项目对话框中使用。

### 从 Visual Studio (手动方式)

启动 Visual Studio，通过创建新的 ASP.NET Core Web 应用程序创建一个新的解决方案文件（`.sln`）：

![image](../assets/images/templates/orchard-screencast-1.gif)

既然我们已经创建了一个新的 Web 应用程序，我们需要添加正确的依赖项，让这个新的 Web 应用程序成为 Orchard Core 应用程序的目标。

!!! 注意
    如果您想要使用 `preview` 包，请[在包源中配置 OrchardCore Preview URL](../preview-package-source.md).
最后，我们需要在 `Program.cs` 文件中注册 Orchard CMS 服务，如下所示：

```csharp
using OrchardCore.Logging;

var builder = WebApplication.CreateBuilder(args);

builder.Host.UseNLogHost();

builder.Services
    .AddOrchardCms()
    .AddSetupFeatures("OrchardCore.AutoSetup");

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
```


# 使用异常处理中间件

```csharp
app.UseExceptionHandler("/Error");
```

使用静态文件中间件

```csharp
app.UseStaticFiles();
```

使用OrchardCore中间件

```csharp
app.UseOrchardCore();
```

启动应用程序

```csharp
app.Run();
```

## 创建一个新的CMS模块

### 从命令行Shell创建一个新模块（自动化方式）

#### 模块命令

```CMD
dotnet new ocmodulecms
```
上面的命令将使用默认选项。

您可以传递以下CLI参数来设置选项：

```CMD
Orchard Core模块（C#）
作者：Orchard Project
选项：
  -A|--AddPart           在Program.cs中为部分添加依赖项注入。如果未提供PartName，则将使用默认名称
                         bool - 可选
                         默认值：false / (*) true

  -P|--PartName          添加所需的所有部分文件
                         string - 可选
                         默认值：MyTest

  -ov|--orchard-version  指定要使用的Orchard Core软件包的版本。
                         string - 可选
                         默认值：1.5.0
```

```CMD
dotnet new ocmodulecms -n 模块名称.OrchardCore

dotnet new ocmodulecms -n 模块名称.OrchardCore --AddPart true

dotnet new ocmodulecms -n 模块名称.OrchardCore --AddPart true --PartName 测试
```

!!! note
    `Part`会自动添加到提供的 `PartName`的末尾。

### Visual Studio 新建模块 (手动方式)

打开Visual Studio，打开Orchard Core解决方案文件（`.sln`），选择 `OrchardCore.Modules` 文件夹，右击选择 “add --> new project（添加 --> 新建项目）”，创建一个新的 .NET Standard 类库：

![image](../assets/images/templates/38450533-6c0fbc98-39ed-11e8-91a5-d26a1105b91a.png)

为了将这个新的类库标记为Orchard模块，我们现在需要引用 `OrchardCore.Module.Targets` NuGet软件包。
!!! 注意
    如果您想使用 `preview` 的包，[请在包源中配置OrchardCorePreview的URL](../preview-package-source.md)

这些 `*.Targets` NuGet 包用于将类库标记为特定的Orchard Core功能。  
现在，我们对 `OrchardCore.Module.Targets` 情有独钟。  
我们将向新的类库添加 `OrchardCore.Module.Targets` 作为依赖项来标记它为模块。  
为此，您需要右键单击 `MyModule.OrchardCore` 项目，然后选择 "Manage NuGet Packages" 选项。  
在 Nuget 包管理器中查找包，您需要勾选 "include prerelease" 然后确保您之前添加的 Orchard Core feed 被选择。  
找到它后，在版本: 最新预发布 x.x.x.x 右侧的面板中单击“安装”按钮。

![image](../assets/images/templates/38450558-f4b83098-39ed-11e8-93c7-0fd9e5112dff.png)

安装完成后，您的新模块将如下所示：

![image](../assets/images/templates/38450628-31c8e2b0-39ef-11e8-9de7-c15f0c6544c5.png)

为了让Orchard Core识别这个模块，现在需要一个 `Manifest.cs` 文件。这是该文件的示例： 

```csharp
使用OrchardCore.Module.Manifest创建新模块

在你的Orchard Core项目中，创建一个新的文件夹来存储你的自定义模块。在此文件夹中，添加一个名为`Manifest.cs`的新文件，并添加下面的代码：

```
using OrchardCore.Modules.Manifest;

[assembly: Module(
    Name = "TemplateModule.OrchardCore",
    Author = "The Orchard Team",
    Website = "http://orchardproject.net",
    Version = "0.0.1",
    Description = "Template module."
)]
```

为了让这个模块开始工作，我们现在需要在我们的新模块中添加一个`Startup.cs`文件。可以参考这个文件: [`OrchardCore.Templates.Cms.Module/Startup.cs`](https://github.com/OrchardCMS/OrchardCore/tree/dev/src/Templates/OrchardCore.ProjectTemplates/content/OrchardCore.Templates.Cms.Module/Startup.cs)

最后一步是将我们的新模块添加到`OrchardCore.Cms.Web`项目中作为引用，以便将其包含为网站模块。之后，你就可以开始构建自定义模块了。你可以参考我们的 [模板模块](https://github.com/OrchardCMS/OrchardCore/tree/dev/src/Templates/OrchardCore.ProjectTemplates/content/OrchardCore.Templates.Cms.Module/) 来了解通常所需的基本内容。

创建一个新的主题

### 通过命令行 Shell 创建新主题（自动化方式）


#### 主题命令

```CMD
dotnet new octheme -n "ThemeName.OrchardCore"
```

### 从 Visual Studio 新建主题（手动方式）

与模块的操作应该是一样的，但我们需要引用 `OrchardCore.Theme.Targets` ，且 `Manifest.cs` 文件略有不同：

```csharp
using OrchardCore.DisplayManagement.Manifest;

[assembly: Theme(
    Name = "TemplateTheme.OrchardCore",
    Author = "The Orchard Team",
    Website = "https://orchardproject.net",
    Version = "0.0.1",
    Description = "The TemplateTheme."
这是一个不完整的代码块，缺少开头的反引号（`）。请提供完整的代码块以获取帮助。


> 该文档由Chat-GPT 翻译
