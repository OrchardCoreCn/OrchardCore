# 创建一个新的解耦CMS网站

## 介绍

本文介绍了创建一个完全功能的解耦CMS网站的过程，可以让您编辑博客文章并呈现它们。

__解耦__ 是一种开发模型，其中站点的前端和后端(管理)托管在同一个Web应用程序中，但只有后端由CMS驱动。开发人员可以编写自己的ASP.NET Razor页面或控制器，完全控制网站生成的内容，同时利用CMS(在这种情况下是Orchard Core)来编写内容。

!!! 注意
     虽然本指南从一个新项目开始，并使用Razor页面，但您可以在任何现有的ASP.NET Core应用程序中使用本指南的大部分内容将Orchard Core作为内容管理后端添加到中。

![最终结果](images/custom-preview.jpg)

## 先决条件

您应该：

- 能够创建一个新的ASP.NET Core项目
- 熟悉C＃和HTML
- 已安装.NET SDK
- 拥有Visual Studio .NET或Visual Studio Code

## 项目设置

### 创建Orchard Core CMS Web应用程序

#### 选项1——从Visual Studio .NET开始

如果您想使用Visual Studio .NET，请按照以下步骤操作。

- 打开Visual Studio .NET。
- 创建一个新的ASP.NET Core Web应用程序项目。

![New project](images/new-project.jpg)

- 输入一个“项目名称”和“位置”。在本教程中，我们将使用“OrchardSite”作为名称。然后单击“创建”。
- 选择“Web应用程序”模板，将所有其他选项保持默认值，然后单击“创建”。

#### 选项2——从命令行开始


从工程目录开始

- 输入 `dotnet new webapp -o OrchardSite`，其中“OrchardSite”是要创建的项目名称。

这将使用 Razor Pages 创建一个 Web 应用程序。

### 测试网站

- 启动项目。

新创建的网站应该可以运行，并且看起来像这样:

![Setup](images/home.jpg)

### 将Orchard Core CMS 添加到网站

- 双击或编辑 __.csproj__ 文件
- 修改 `<PropertyGroup>` 部分如下:
这将允许重新加载Razor页面，无需重新编译它们。

- 添加一个新的`<ItemGroup>`，如下所示：

```xml
<ItemGroup>
  <PackageReference Include="OrchardCore.Application.Cms.Core.Targets" Version="1.5.0" />
</ItemGroup>
```
这将添加来自Orchard Core CMS的包

- 编辑`Program.cs`文件以配置OrchardCore CMS服务，如下所示：
```csharp
builder.Services.AddOrchardCms();
```

!!! 注意 "Razor Pages"
    不要直接调用 `AddRazorPages`，因为 `services.AddOrchardCms()` 已在内部调用它。

- 编辑 `Program.cs` 文件
- 删除 `app.UseStaticFiles()` 后面的所有内容，并将其替换为 `app.UseOrchardCore()`，如下所示：

```csharp
   ...
   
   app.UseHttpsRedirection();
   app.UseStaticFiles();
   
   app.UseOrchardCore();
}
```
启动应用程序，设置屏幕将显示：

! [Setup]（images / setup.jpg）

### 设置新站点


设置屏幕需要一些信息，以创建新数据库来存储内容和用户帐户。

-为您的网站输入一个名称。在这个例子中，我们将使用“My Site”。
-在__Recipe__下拉列表中，选择可用于解耦和无头模式的__空白站点__。
-如果检测到的时区不正确，请选择时区。默认情况下，所有日期和时间都将相对于此时区输入或呈现。
-选择数据库服务器。开始的最简单方法是选择__Sqlite__，因为不需要您采取任何其他步骤。
-在__Super User__部分中，输入您选择的某些帐户信息。在这个例子中，我们将使用`admin`作为用户名。
-单击__完成设置__。

几秒钟后，应该显示与原始模板相同的站点，并显示“欢迎”信息。

如果您选择了__Sqlite__，则应用程序的所有状态现在都存储在名为`App_Data`的文件夹中，该文件夹位于您的项目根文件夹中。
如果出了问题，请尝试删除`App_Data`文件夹（如果存在），然后重新按照本节中的步骤操作。

## 创建博客文章

本部分涵盖 Orchard Core CMS 的基本内容管理概念，例如《内容类型》和《内容项》。

### 内容建模

在 Orchard Core CMS 中，大部分被管理的内容称为 __内容项__。内容项是类似于页面、文章、博客文章、新闻项目或任何需要编辑的版本化文档。每个文档都基于一个定义了它由哪些属性构成的 __内容类型__。例如，任何一篇文章都会有标题和一些文本。博客文章可能还有标签。Orchard Core CMS 允许您根据自己的需求对内容类型进行建模，这就是所谓的 _内容建模_。

!!! 提示 "对于开发人员"
    内容类型类似于一个类，其中内容项可以看作是内容类型的实例。

### 创建博客文章内容类型

Orchard 预先配置了一组称为 __内容部分__ 的可组合数据管理元素，可以像LEGO一样用于创建自定义类型。例如，__标题部件__ 将提供一个漂亮的编辑器来输入内容项的标题，并将其设置为屏幕上默认显示的文本。另一个重要的内容部件是 __Markdown 正文部件__，它提供了一种存储和呈现Markdown作为内容项主要文本的方法。这对于博客文章也非常有用。

!!! 提示 "对于开发人员"
    内容部件类似于部分类，然后将每个内容部件聚合起来以定义内容类型。内容字段类似于添加到内容类型中的自定义属性。
让我们创建一个名为 `Blog Post` 的新内容类型，并向其添加一些必要的内容部分：

- 从运行的网站中，打开 url `/admin`。
- 在登录界面中，输入设置期间使用的用户凭据。
- 您会看到网站的管理界面。
- 在左菜单中，选择__Content Definition__，然后选择 __Content Types__。
- 在右上角点击__Create new type__
- 在 __Display Name__ 中输入 `Blog Post`。__Technical Name__ 将自动生成值为`BlogPost`，如下所示：

![New Content Type](images/new-content-type.jpg)

- 点击 __Create__
- 展示了一个__Content Parts__列表。 选择 __Title__ 和 __Markdown Body__，然后点击 __Save__

![Add Content Parts](images/add-content-parts.jpg)

- 在下一页中，将__Parts__按以下方式重新排序：

![Edit Content Type](images/edit-content-type.jpg)
- 然后点击 __保存__

你可以发现每个内容部分前都有一个 __编辑__ 按钮。这使我们可以为每个部分定义一些可能仅适用于此类型的设置。

- 在 `MarkdownBody` 部分，点击 __编辑__。
- 选择 __`所见即所得编辑器`__ 作为要使用的编辑器类型，然后点击 __保存__：

![编辑MarkdownBody类型](images/edit-markdownbody.jpg)

__Blog Post__ 内容类型已经可以使用了。

### 创建博客文章

- 在左侧菜单中，选择 __新建__，然后点击 __Blog Post__，以显示新创建的 `BlogPost` 内容类型的编辑器。

![编辑Blog Post](images/edit-blogpost.jpg)

- 使用一些内容填写 __标题__ 和 __MarkdownBody__ 表单元素，然后点击 __发布__。为了举例，我们将使用 `This is a new day` 和一些 Lorem Ipsum 文本。
- 在菜单中，点击 __内容 > 内容项__，以显示所有可用的内容项。
![内容项](images/content-items-1.jpg)

这显示我们现在有一个名为`This is a new day`的新博客文章内容项。随着我们创建更多的内容项，它们将出现在此页面上。

## 在网站上呈现内容

下一步是创建一个自定义 Razor 页面，以显示具有自定义 URL 的任何博客文章。

### 创建自定义 Razor 页面

- 在编辑器中，点击`Pages`文件夹，在其中创建一个名为`BlogPost.cshtml`的新文件，具有以下内容：

```html
@page "/blogpost/{id}"

<h1>This is the blog post: @Id</h1>

@functions
{
```
### 从路由值访问

路由中，命名为`{id}`的url片段将自动赋值给使用`@Id`语法呈现的`Id`属性。

要显示上一页，打开`/blogpost/1`这个url。

### 通过它的标识符加载博客文章

每个内容项在Orchard Core中都有一个唯一且不可变的Content Item Identifier， 我们可以在Razor页面中使用它来加载博客文章。

- 编辑`BlogPost.cshtml` Razor页面：

```html hl_lines="2 10"
@page "/blogpost/{id}"
@inject OrchardCore.IOrchardHelper Orchard
```
@{
    var blogPost = await Orchard.GetContentItemByIdAsync(Id);
}

<h1>这是博客文章: @blogPost.DisplayText</h1>

@functions
{
    [FromRoute]
    public string Id { get; set; }
}
```

- 在内容项页面中，单击我们在前面部分创建的博客文章。
- 找到 `/ContentItems/` 后面的 url 部分，在下面的截图中为 `4tavbc16br9mx2htvyggzvzmd3`：

![Content Item id](images/content-item-id.jpg)

- 通过将 `[YOUR_ID]` 部分替换为你自己博客文章的值，打开 url `/blogpost/[YOUR_ID]`。
- 页面应显示博客文章的实际标题。

![按ID显示博客文章](images/blogpost-id.jpg)

### 访问内容项的其他属性

在前面一节中，`DisplayText` 属性用于呈现博客文章的标题，该属性通常适用于每个内容项，例如 `ContentItemId` 或 `Author`。 然而，每个内容类型定义了一组独特的动态属性，例如我们在 **内容建模** 部分添加的  __Markdown 部分__。

内容项的动态属性以 Json 文档的方式在 `Content` 属性中提供。

- 在标题后添加以下行以编辑 Razor 页面：

```html hl_lines="4"
...
<h1>This is the blog post: @blogPost.DisplayText</h1>

@Orchard.ConsoleLog(blogPost)
...
```
- 重新打开具有内容项ID的博客文章页面，然后按下__F12__以可视化浏览器的调试工具，然后打开__控制台__。内容项的状态应该显示如下：

![Console Log](images/console-log.jpg)

显示当前内容项的所有属性，包括包含我们为博客文章内容类型配置的所有动态部分的“Content”属性。

展开“MarkdownBodyPart”节点可查看博客文章内容的“Markdown”字段。

- 编辑Razor页面以注入此代码：

```html hl_lines="4"
...
<h1>@blogPost.DisplayText</h1>

<p>@blogPost.Content.MarkdownBodyPart.Markdown</p>

@Orchard.ConsoleLog(blogPost)
...
```
- 刷新博客文章页面以显示Markdown文本。
- 最后，我们可以使用以下代码处理Markdown内容并将其转换为HTML：

```html
<p>@await Orchard.MarkdownToHtmlAsync((string) blogPost.Content.MarkdownBodyPart.Markdown)</p>
```

### 从自定义slug中加载博客文章

尽管我们可以通过Content Item Id加载博客文章，但这对用户来说并不友好，良好的SEO优化是在URL中重用标题。

在Orchard Core CMS中，别名部分允许提供自定义用户友好的文本来标识内容项。

- 在网站的管理员部分，打开 __内容定义__ > __内容类型__ > __博客文章__
- 在页面底部，选择 __添加部分__
- 选择 __别名__ 然后点击 __保存__
- 将 __别名__ 移动到 __标题__ 下并保存
- 编辑博客文章，现在将显示 __别名__ 文本框，您可以在其中输入一些文本。在本示例中，我们将使用 `new-day`
现在我们可以更新 Razor 页面，使用别名替代内容项 ID，无论是在 URL 中还是在加载内容项的方式中。

- 用以下代码更改 Razor 页面：

```html
@page "/blogpost/{slug}"
@inject OrchardCore.IOrchardHelper Orchard

@{
    var blogPost = await Orchard.GetContentItemByHandleAsync($"alias:{Slug}");
}

...

@functions
{
    [FromRoute]
```
## 使用自定义模式生成标识符

通过自定义设置，__别名部分__可以自动生成内容。在我们的例子中，我们希望它能够从__标题__自动生成。为了提供这样的模式，CMS 使用了一个名为__Liquid__的模板语言，以及一些自定义函数来操作内容项的属性。Orchard提供了一个通常适用的默认模式。

- 编辑博客文章的内容定义，并为__别名部分__点击__编辑__。
- 在__模式__文本框中，注意预先填充的模式:

![Edit Alias Pattern](images/alias-pattern.jpg)

这将动态提取内容项的`DisplayText`属性，对于我们的例子而言是__标题__，并在这些值上调用`slugify`过滤器，它将把标题转换成一个可用于slug的值。
- 编辑博客文章内容项。
- 清除 __Alias__ 文本框中的内容。这样系统就可以使用我们定义的自定义模式生成它。
- 点击 __发布（并继续）__。

别名现在是 `this-is-a-new-day`：

![Generated Alias](images/generated-alias.jpg)

- 打开网址 `/blogpost/this-is-a-new-day`，确认该路由器仍能用自动生成的别名正常工作。

![This Is A New Day](images/this-is-a-new-day.jpg)

!!! 注意 "任务"
    创建一个新的博客文章，并验证别名是否自动生成，并且可以使用自己的定制url显示。

## 为博客文章配置预览功能

一个对于需要编辑内容的用户来说非常有用的功能叫做 __预览__。如果您尝试编辑一篇博客文章并点击 __预览__ 按钮，一个新的窗口将打开，显示当前编辑的值的实时预览。
- 在编辑已有的博客文章时，点击 __预览__，并将新窗口侧边栏打开。
- 在可见的预览窗口中编辑 __标题__，并注意结果是如何自动更新的。

![预览编辑器](images/preview-editor.jpg)

CMS不知道渲染内容项时要使用哪个Razor页面，因此会使用通用页面。但是，与为生成别名提供模式的方式相同，我们可以提供一种模式，以调用特定页面以预览内容项。

- 编辑博客文章的内容定义，点击 __添加部件__，然后选择 __预览__。点击 __保存__。
- 在部件列表中，对于 __预览__，点击 __编辑__，以更改此内容类型的设置。
- 在 __Pattern__ 文本框中，输入 `/blogpost/{{ ContentItem.Content.AliasPart.Alias }}`，这是生成与在Razor页面中配置的路由相同URL的方式。

![编辑预览模式](images/preview-pattern.jpg)

- 点击 __保存__ 并打开编辑博客文章的预览。

![自定义预览](images/custom-preview.jpg)

正如您所看到的，预览现在使用我们为显示博客文章设置的特定路由，编辑者在编辑内容时可以获得完整的体验。
!!! hint “建议”
    也可以使用专门的模板进行预览，该模板可以为编辑器提供提示，或检测错误，并在预览窗口中呈现它们。用户还可以更改窗口大小，以测试在不同客户端上的呈现效果。

## 概要

在本教程中，我们学习了如何

- 开始使用 Orchard Core CMS 项目
- 创建自定义内容类型
- 编辑内容项
- 创建具有自定义路由的 Razor 页面以呈现内容
- 使用不同的标识符加载内容项
- 在编辑内容时渲染所见即所得的预览屏幕

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/yWpz8p-oaKg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


> 该文档由Chat-GPT 翻译
