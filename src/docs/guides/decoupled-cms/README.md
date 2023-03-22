# 创建新的解耦 CMS 网站
## 介绍

本文将介绍创建一个功能齐全的分离 CMS 网站的过程，该网站允许您编辑博客文章并呈现它们。

__解耦__ 是一种开发模型，其中站点的前端和前端（管理）托管在同一个 Web 应用程序中，但只有前端由 CMS 驱动。然后，开发人员可以编写自己的 ASP.NET Razor 页面或控制器，以完全控制网站生成的内容，同时仍然利用 CMS（本例中为Orchard Core）来创作内容。

!!! 注意
    虽然本指南从新项目开始，并使用 Razor Pages，但您可以使用本指南中的许多内容将Orchard Core作为内容管理ASP.NET添加到任何现有的核心应用程序。

![Final Result](images/custom-preview.jpg)

## 先决条件

您应该：

- 能够在核心项目中创建新ASP.NET项目
- 熟悉 C# 和 HTML
- 安装 .NET SDK
- 安装Visual Studio .NET 或Visual Studio Code

## 设置项目

### 创建Orchard Core CMS Web 应用程序

#### 选项 1 - 使用Visual Studio .NET 

如果要使用 Visual Studio .NET，请按照此选项进行。

- 打开 Visual Studio .NET.
- 创建一个ASP.NET Core Web 应用程序项目。

![New project](images/new-project.jpg)

- 输入 __项目名称__ 并选择 __位置__. 们将使用"OrchardSite"作为名称。然后单击"创建"。.
- 选择 __Web 应用程序模板__ ，将其他所有内容保留为默认值，然后单击"创建"。

#### 选项 2 - 从命令行

从项目中的文件夹中

- 键入要创建的项目的名称"OrchardSite"。`dotnet new webapp -o OrchardSite`

这将使用Razor Pages创建 Web 应用程序。

### 测试网站

- 启动项目。

新创建的网站应该能够运行，并且看起来像这样：

![Setup](images/home.jpg)

### 将果Orchard Core CMS 添加到网站

- 双击或编辑 __.csproj__ 文件 
- 修改   `<PropertyGroup>` 就像这样:

```xml
<PropertyGroup>
  <TargetFramework>net6.0</TargetFramework>
</PropertyGroup>
```

这将允许重新加载 Razor 页面，而无需重新编译它们。

- 添加一个新的 `<ItemGroup>` 节点 :

```xml
<ItemGroup>
  <PackageReference Include="OrchardCore.Application.Cms.Core.Targets" Version="1.5.0" />
</ItemGroup>
```
这将添加来自Orchard Core CMS 的包

- Edit the `Program.cs` file to configure OrchardCore CMS services like this:

```csharp
builder.Services.AddOrchardCms();
```

!!! 注意 "Razor Pages"
    `AddRazorPages` 不需要直接调用，因为已在`services.AddOrchardCms()`内部调用它。 

- Edit the `Program.cs` file
- Remove everything after `app.UseStaticFiles();` and replace it by `app.UseOrchardCore();` like this:

```csharp
   ...
   
   app.UseHttpsRedirection();
   app.UseStaticFiles();
   
   app.UseOrchardCore();
}
```

启动应用程序时，将显示"设置"页面：


![Setup](images/setup.jpg)

### 设置新网站

"设置"页面需要一些信息，以便创建新数据库来存储内容和用户帐户。

- 输入网站的名称。在此示例中，我们将使用"My Website"。
- 在"配方"下拉列表中，选择可用于分离和无头模式的空白站点。
- 如果检测到的时区不正确，请选择时区。默认情况下，所有日期和时间都将相对地输入或呈现到此时区。
- 选择数据库服务器。最简单的方法是选择Sqlite，因为它不需要您执行任何其他步骤。
- 在"超级用户"部分中，输入一些帐户信息或您的选择。在此示例中，我们将用 admin 作用户名。
- 单击完成设置。

几秒钟后，应显示与原始模板相同的网站，并发送"欢迎"消息。

如果选择__Sqlite__，则应用程序的所有状态现在都存储在项目根文件夹中的`App_Data`文件夹中。

> 如果出现问题，请尝试删除`App_Data`文件夹（如果该文件夹存在）并重新设置网站。

## 创建博客文章

T本部分介绍Orchard CoreCMS的基本内容管理概念，如内 __Content Types__ 和 __Content Items__.

### 内容建模

在Orchard Core CMS 中，管理的内容大部分称为内容项（Content Item）。内容项是版本文档，如页面、文章、博客文章、新闻项目或任何需要编辑的内容。这些文档都基于定义由哪些属性创建的内容类型（Content Type）。例如，任何文章都将有一个标题和一些文本。博客文章可能也有标记。Orchard Core CMS 允许您以您想要的方式对内容类型进行建模，这称为内容建模。

!!! 开发人员提示
    内容类型类似于类，其中内容项可视为内容类型的实例。

### 创建博客文章内容类型

Orchard comes pre-configured with a set of composable elements of data management called __Content Parts__ that can be used to create custom types like a LEGO. A __Title Part__ for instance will provide a nice editor to enter the title of a content item, and also set it to the text to display by default in the screens. Another important content part is the __Markdown Body Part__ which provides a way to store and render Markdown as the main text of a content item. This is also useful for a Blog Post.

Orchard 预配置了一组可组合的数据管理元素，称为内容部件（Content Parts），可用于创建自定义类型（就像乐高）。例如，标题部件将提供一个不错的编辑器来输入内容项的标题，并将它设置为默认在屏幕中显示的文本。另一个重要内容部分是 __Markdown Body Part__ ，它提供了一种将标记下存储和呈现为内容项主文本的方法。这对于博客文章也很有用。


!!! 开发人员提示
    内容部件类似于部分类，然后聚合每个内容部件以定义内容类型。内容字段类似于添加到内容类型的自定义属性。


让我们创建一个名为 `Blog Post` 的新内容类型，并将其添加一些必要的内容部分： 

- 打开网址 `/admin`.
- 在登录屏页面，输入设置时使用的用户凭据。
- 你将看到管理后台.
- 在左侧菜单中，选择内容 > 内容定义，然后选择内容类型。
- 单击右上角的"创建新类型"
- 在"显示名称"中输入 `Blog Post` 。技术名称将自动生成`BlogPost`：

![New Content Type](images/new-content-type.jpg)

- 单击"创建"
- 将显示内容部件的列表。选择 __Title__ 和 __Markdown Body__ 正文，然后单击"保存"

![Add Content Parts](images/add-content-parts.jpg)

- In the following screen, scroll to the bottom of the page and re-order the Parts like this:
- 在下面的屏幕中，滚动到页面底部，然后重新选择内容部件：

![Edit Content Type](images/edit-content-type.jpg)

- 然后单击"保存"

您可以注意到每个内容部件前面有一个"编辑"按钮。这允许我们定义一些可能可用于每个设置的设置，仅适用于此类型。

- 在 `MarkdownBody` 部件上, 点击编辑.
- 选择 __`所见即所得编辑器（Wysiwyg editor）`__ 作为要使用的编辑器类型，然后单击"保存"：

![Edit Markdown Body Type](images/edit-markdownbody.jpg)

__Blog Post__ 内容类型已准备就绪。

### 创建博客文章

- 在左侧菜单中，选择"新建"，然后单击博客文章以显示新创建的`BlogPost`内容类型的编辑器。

![Edit Blog Post](images/edit-blogpost.jpg)

- 填写标题和 __MarkdownBody__ 包含一些内容，然后单击"发布"。这个示例中，我们将使用一些文本`This is a new day`。
- 在菜单中，单击 内容 > 内容项 以显示所有可用内容项。

![Content Items](images/content-items-1.jpg)

可以看到，我们现在有一个新的博客文章内容项名为  `This is a new day` 。当我们创建更多内容项时，这些项将显示在此页面上


## 在网站上呈现内容

下一步是创建自定义 Razor 页面，该页面将显示任何包含自定义 URL 的博客文章。

### 创建自定义Razor 页面

- 在编辑器中，在`Pages`文件夹中，创建一个包含以下内容的新文件： `BlogPost.cshtml`  

```html
@page "/blogpost/{id}"

<h1>This is the blog post: @Id</h1>

@functions
{
    [FromRoute]
    public string Id { get; set; }
}
```

- 浏览器中打开  `/blogpost/1` 

!!! 访问路由值
    在路由中，命名 URL  `{id}`段将自动分配给使用语法呈现的`Id` 属性，使用`@Id`语法呈现。 
使用'@Id'语法呈现。

### 从标识符加载博客文章

Orchard Core 中的每个内容项都有一个唯一且不可变的内容项标识符。我们可以在Razor页面中使用它来加载博客文章

-编辑 `BlogPost.cshtml`:

```html hl_lines="2 10"
@page "/blogpost/{id}"
@inject OrchardCore.IOrchardHelper Orchard

@{
    var blogPost = await Orchard.GetContentItemByIdAsync(Id);
}

<h1>This is the blog post: @blogPost.DisplayText</h1>

@functions
{
    [FromRoute]
    public string Id { get; set; }
}
```

- 在"内容项"页中，单击我们在上一节中创建的博客文章。
- 在 以下屏幕截图中查找 url 的`/ContentItems/`后面部分：`/ContentItems/4tavbc16br9mx2htvyggzvzmd3`

![Content Item id](images/content-item-id.jpg)

- 打开 URL`/blogpost/[YOUR_ID]`，将[YOUR_ID]部分替换为您自己的博客文章的值。
- 该页应显示博客文章的实际标题。

![Blog Post by Id](images/blogpost-id.jpg)

### 访问内容项的其他属性

In the previous section the `DisplayText` property is used to render the title of the blog post. This property is common to every content items, so is the `ContentItemId` or `Author` for instance. However each Content Type defines a unique set of dynamic properties, like the __Markdown Part__ that we added in the __Content Modeling__ section.
在上一节中， `DisplayText`属性 用于呈现博客文章的标题。类似还有`ContentItemId` 或`Author` 这些属性对于每个内容项都是通用的， 。但是，每个内容类型定义一组唯一的动态属性，如我们在"内容建模"部分中添加的 __Markdown Part__  部分。


内容项的动态属性以Json的形式在“content”属性中可用。


- 通过在标题后添加以下行来编辑Razor页面：

```html hl_lines="4"
<h1>This is the blog post: @blogPost.DisplayText</h1>

@Orchard.ConsoleLog(blogPost)
```


- 使用内容项 ID 重新打开博客文章页面，然后按F12从浏览器中可视化调试工具，然后打开控制台。内容项的状态应显示为：

![Console Log](images/console-log.jpg)

将显示当前内容项的所有属性，包括“content”属性，该属性包含我们为Blog Post内容类型配置的所有动态部分。

展开“MarkdownBodyPart”节点将显示包含博客文章内容的“Markdown”字段。

- 编辑Razor页面以插入以下代码：

```html hl_lines="4"

<h1>@blogPost.DisplayText</h1>

<p>@blogPost.Content.MarkdownBodyPart.Markdown</p>

@Orchard.ConsoleLog(blogPost)

```

- 刷新博客文章页面以显示Markdown文本。
- 最后，我们可以处理 Markdown 内容，并将其转换为 HTML，并具有以下代码：

```html
<p>@await Orchard.MarkdownToHtmlAsync((string) blogPost.Content.MarkdownBodyPart.Markdown)</p>
```

### 从自定义地址（slug）加载博客文章

即使我们可以从他们的内容项Id加载博客文章，这不是用户友好的，一个好的SEO优化是重用URL中的标题。

在Orchard Core CMS中，别名部分允许提供自定义的用户友好文本来标识内容项。

- 在站点的管理部分，打开 __内容定义__ > __内容类型__ > __Blog Post__
- 在页面底部，选择 __添加部件__
- 选择 __Alias__ 然后点击 __保存__
- 移动 __Alias__ 到 __Title__ 下方，然后保存
- 编辑博客帖子,  __Alias__ 文本框已经显示出来了, 您可以在其中输入一些文本。在本例中，我们将使用 `new-day`

![Edit Alias](images/edit-alias.jpg)

我们现在可以更新Razor页面，在URL和加载内容项的方式中使用别名而不是内容项id。

- 使用以下代码更改Razor页面：

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
    public string Slug { get; set; }
}
```

这些更改包括在路由和本地属性中使用“slug”名称，还使用新方法加载具有别名的内容项。

- 打开 `/blogpost/new-day` 页面，它应该显示完全相同的结果，但是使用一个更符合SEO且用户友好的url。

### 使用自定义模式生成段塞

别名部分提供一些自定义设置，以便自动生成。在我们的例子中，我们希望它自动地从 __Title__ 生成。为了提供这样的模式，CMS使用了一种名为 __Liquid__ 的模板语言，以及一些自定义函数来操作内容项的属性。Orchard提供了一个普遍适用的默认模式。


- 编辑博客文章的内容定义，定位到“Alias”，单击“编辑”。
- 在 __模式(Pattern)__ 文本框注意预先填充的代码：

![Edit Alias Pattern](images/alias-pattern.jpg)

这将动态提取内容项的“DisplayText”属性，在我们的示例中是 `Title`，并对这些值调用“slugify”筛选器，这将把标题转换为可以在slug中使用的值。

- 编辑博客文章内容项。
- 清除“别名”文本框。这将允许系统使用我们定义的自定义模式生成它。
- 单击“发布（并继续）”。

现在的别名为： `this-is-a-new-day`:

![Generated Alias](images/generated-alias.jpg)

- 打开URL`/blogpost/this-is-a-new-day`以确认路由仍然使用这个自动生成的别名。

![This Is A New Day](images/this-is-a-new-day.jpg)

!!! note "分配"
    创建一个新的博客文章，并验证别名是自动生成的，并且可以使用它自己的自定义url来显示。

## 配置博客文章的预览功能

对于需要编辑内容的用户来说，一个非常有用的功能叫做“预览”。如果您尝试编辑博客文章并单击“预览”按钮，将打开一个新窗口，其中会显示当前编辑的值的实时预览。

- 在编辑现有的博客文章时，单击“预览”，并在旁边打开新窗口。
- 在预览窗口可见时编辑“标题”，并注意结果是如何自动更新的。

![Preview Editor](images/preview-editor.jpg)

The CMS doesn't know what Razor Page to use when rendering a content item, and will use a generic one instead. However, the same way we provided a pattern for generating an alias, we can provide a pattern to invoke a specific page for previewing a content item.
CMS不知道在呈现内容项时使用什么Razor页面，而是使用通用页面。然而，与提供生成别名的模式相同，我们也可以提供一个模式来调用特定页面以预览内容项。

- 编辑博客文章的内容定义，单击“添加零件”，然后选择“预览”。单击“保存”。
- 在零件列表中，选择 __Preview__ ，单击“编辑”以更改此内容类型的设置。
- In the __Pattern__ textbox, enter `/blogpost/{{ ContentItem.Content.AliasPart.Alias }}` which is the way to generate the same URL as the route which is configured in the Razor page.
- 在“模式”文本框中，输入`/blogpost/{{ ContentItem.Content.AliasPart.Alias }}` 这是生成与Razor页面中配置的路由相同的URL的方法。

![Edit Preview Pattern](images/preview-pattern.jpg)

- 单击“保存”并在编辑博客文章时打开预览。

![Custom Preview](images/custom-preview.jpg)

正如您所看到的，预览现在使用的是我们为显示博客文章而设置的特定路径，编辑在编辑内容时拥有完全逼真的体验。

!!! hint "建议"
    编辑器也可以在预览窗口中为它们提供提示，或者为预览提供提示。用户还可以更改窗口的大小，以便在不同的客户端上测试渲染。

## 摘要

In this tutorial we have learned how to

- Start a new Orchard Core CMS project
- Create custom content types
- Edit content items
- Create Razor Pages with custom routes to render then content
- Load content items with different identifiers
- Render wysiwyg preview screens while editing the content

## Video

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/yWpz8p-oaKg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
