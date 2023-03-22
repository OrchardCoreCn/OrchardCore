# Orchard Core的NuGet包使用 入门篇

In this article, we are going to see how easy it is to create a CMS Web application using the NuGet packages provided by Orchard Core.

## Create an Orchard Core CMS application

使用Visual Studio，创建一个名为Cms.Web空的.NET Core网站应用。在配置新项目界面，不要勾选`将解决方案和项目放在同一目录中`选项，因为稍后创建模块和主题时，你可能希望它们在解决方案的同级目录中。

!!! 备注
    如果你想使用“预发行”包，[在“程序包源”中配置OrchardCore预览地址](preview-package-source.md)

    为项目添加包引用,在项目里的 `依赖项` 上右击然后选择 `管理NuGet程序包` , 勾选 `包括发行版` (如果需要的话) 。 如果你配置了上面的预览地址，点击右上角的 `程序包源` 并选择刚才配置的预览地址。在 `浏览` 选项卡中，搜索 `OrchardCore.Application.Cms.Targets` ，然后选中并 `安装` 这个包。

### Getting Started with `Program.cs` Only Using .NET 6 Framework?
!!! tip
    When starting a new project using `.NET 6` framework, you'll notice that the created project does not have a `Startup` class as it did in previous versions of the .NET framework.

Open `Program.cs` file. Remove the following line "if exists"

```csharp
builder.Services.AddRazorPages();
```

Add the following line 

```csharp
builder.Services.AddOrchardCms()
```

Additionally, remove the following lines

```csharp
app.UseHttpsRedirection();
app.UseRouting();
app.UseAuthorization();
app.MapRazorPages();
```
Lastly, add the following line to the request pipeline

```csharp
app.UseOrchardCore();
```

When you are done, the `Program.cs` file will something like this

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

app.Run();
```

### Getting Started Using `Program.cs` file?

Open `Program.cs` file, then add the OrchardCore CMS services by adding this line:

```csharp
builder.Services.AddOrchardCms();
```

After building the `WebApplication`, replace this line:

```csharp
app.MapGet("/", () => "Hello World!");
```

然后使用以下代码替换:

```csharp
app.UseOrchardCore();
```

Finally, remove the default `Pages` and/or `Views` folder to allow OrchardCore to render the views from the active theme.

## Setup your application

运行项目 (Ctrl+F5)。浏览器显示了 安装界面。

在安装界面输入需要的信息：

- 站点名字。 比如： `Orchard Core`.
- 配方。 比如： `Agency`.
- 默认时区。 比如： `(+01:00) Europe/Paris`.
- 数据库类型。 比如： `SqLite`.
- 超级用户名。 比如： `admin`.
- 超级用户的电子邮箱。 比如： `foo@bar.com`
- 超级用户的密码以及确认密码。

提交表单，你的网站在几秒后将会生成。

然后，你就可以使用 `/admin` 地址访问管理界面了。开始享受成果吧。
