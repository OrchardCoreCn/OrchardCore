# 通过NuGet包入门Orchard Core

在这篇文章中，我们将看到使用Orchard Core提供的NuGet包创建CMS Web应用程序有多么简单。

## 创建Orchard Core CMS应用程序

在Visual Studio中，创建一个新的空白的.NET Core Web应用程序。例如：'Cms.Web'。不要勾选“将解决方案和项目放在同一个目录中”，因为后来当你创建模块和主题时，你会想让它们与解决方案中的Web应用程序一起生存。

!!! 注意
    如果你想使用`preview`包，[请在Package sources中配置OrchardCore Preview url](preview-package-source.md)。

要添加对包的引用，请右键单击项目并单击“管理NuGet包...”，如果需要，请勾选“包括预发布版本”。如果你添加了上面的预览源，请从右上方的“Package Source”选择此源。在“Browse”选项卡中，搜索“OrchardCore.Application.Cms.Targets”并“安装”该包。

### 仅使用.NET 6框架和`Program.cs`入门？
!!!提示
    当使用`.NET 6`框架启动新项目时，你会注意到创建的项目没有像以前的.NET框架版本一样拥有`Startup`类。

打开`Program.cs`文件，并删除如下代码：

```csharp
builder.UseOrchardCore(); 
```

此外，删除以下代码

```csharp
app.UseHttpsRedirection();
app.UseRouting();
app.UseAuthorization();
app.MapRazorPages();
```

最后，在请求管道中添加以下代码

```csharp
builder.UseOrchardCore(); 
```
在完成后，`Program.cs` 文件会像这样

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddOrchardCms();

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}

app.UseStaticFiles();
app.UseOrchardCore();
```
## 使用 `Program.cs` 入门？

打开 `Program.cs` 文件，然后添加 OrchardCore CMS 服务，可以通过加入这一行代码来实现：

```csharp
builder.Services.AddOrchardCms();
```

构建 `WebApplication` 后，将这一行代码:

```csharp
app.MapGet("/", () => "Hello World!");
```

替换为以下行代码： 

```csharp
app.UseOrchardCore().Run();
```

最后，删除默认的 `Pages` 和/或 `Views` 文件夹，允许OrchardCore从当前主题渲染视图。

## 设置你的应用程序

启动你的应用程序 (Ctrl+F5)。设置页面将显示。

输入有关该网站的必要信息：

- 网站名称。例如： `Orchard Core`。
- 用于的主题配方。例如： `Agency`。
- 网站时区。例如： `(+01:00) Europe/Paris`。
- 要使用的 Sql 提供程序。例如： `SqLite`。
- 管理员用户名。例如： `admin`。
- 管理员的电子邮件。例如： `foo@bar.com`。
- 密码和密码确认。提交表单后，几秒钟后您的网站将生成。

然后，您可以通过`/admin` URL访问管理员页面。享受吧。
$END_OF_FILE$