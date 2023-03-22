# Orchard Core 主题解释 (`OrchardCore.Themes`)

本文将解释如何呈现内容项，以及可以自定义呈现的 HTML 的许多方式。  
它还解释了基本的主题概念，即 __Shapes__、__Alternates__、__Templates__、__Differentiators__、__Content Zones__ 和 __Display Types__。

## 目标

假设我们想要向我们的博客添加一个作品集部分，我们可以在其中列出我们正在进行的所有项目，并能够单独管理这些项目，而不是拥有一个静态页面，我们必须将每个项目的 HTML 复制粘贴到其中。

作品集应该有自己的 URL，如 `/portfolio`，并应按预定义顺序显示项目。

最终结果如下所示：

![Portfolio](./assets/portfolio-page.png)

## 建模内容

唯一明显需要的是创建一个新的 `Project` 内容类型。

但是，有许多不同的建模内容来表示作品集：

- 创建一个 `Portfolio` 内容类型，其中包含一个 `List` 内容部分，该部分限制为 `Project` 内容项。在这种情况下，项目独立于列表，并且可以在站点的其他位置重复使用（它们由作品集引用）。
- 创建一个 `Portfolio` 内容类型，其中包含一个 `BagPart` 内容部分，该部分限制为 `Project` 内容项。在这种情况下，项目内容项位于作品集内容项内（它们由作品集包含）。
- 创建一个 Liquid 页面内容项来查询和呈现所有类型为 `Project` 的内容项。
- 创建一个 Razor 页面，该页面将使用查询来加载所有项目内容项。

本文将解释如何使用 `BagPart` 进行操作，因为它将通过发出单个数据库查询提供最佳性能，同时允许从管理 UI 中执行所有操作。

## 创建内容类型

`Project` 是一个内容类型，将由以下内容组成：

- `Title` 部分，用于设置自定义名称；
- `Text` 字段，用于存储项目的链接；
- `Markdown` 部分，以 markdown 格式提供描述。

在管理 UI 中，选择 __Content Definition__，然后选择 __Content Types__，并创建一个名为 `Project` 的新类型。单击 **Create**。

选择 __Title__ 和 __Markdown__ 作为我们可以添加的部分，然后单击 **Save**。

单击 __Markdown__ 部分的 **Edit**。选择 __Wysiwyg editor__，然后单击 **Save**。这将提供更丰富的 markdown 编辑体验。

单击 **Add Field**，将其命名为 `Url`，并选择 __Text Field__。单击 **Save**，然后再次单击 **Save**。

花点时间将 __Title__ 部分拖放到列表的顶部，以便它将首先出现在编辑器中。然后单击 **Save**。

![New Content Type](./assets/new-content-type.png)

此时，您已经可以非常轻松地创建所有所需的 Project 内容项。但是，我们需要创建一个 `Portfolio` 类型来包含和组织它们。

在管理 UI 中，选择 __Content Definition__，然后选择 __Content Types__，并创建一个名为 `Portfolio` 的新类型。单击 **Create**。

选择 __Title__、__Autoroute__ 和 __Bag__，然后单击 **Save**。

现在，我们可以配置 `Portfolio` 内容类型，以仅接受其包中的 `Project` 内容项。

单击 __Bag__ 部分的 **Edit**。检查 __Project__，然后单击 **Save**。

单击 __Autoroute__ 部分的 **Edit**。输入 `{{ ContentItem | display_text | slugify }}`，然后检查 __Allow custom path__。单击 **Save**。这将生成可自定义的 URL 或使用用户定义的 URL。

花点时间将 __Title__ 部分拖放到列表的顶部，以便它将首先出现在编辑器中。然后单击 **Save**。

![Project](./assets/project.png)

## 创建作品集

单击 **New**，然后选择 __Portfolio__。

给它一个标题，例如 `My Project`。

在 __Permalink__ 中输入 `portfolio`。这将是显示此内容项的 URL。

当您单击 **Add Item** 时，您会注意到只有 __Project__ 可用，如在 `Portfolio` 的 `Bag` 部分中配置的那样。

创建一些项目，然后单击 **Publish**。

单击 __View__ 按钮，以查看它的外观。

![Portfolio](./assets/portfolio.png)

此时，它已经看起来像可以发布的东西，输入的所有信息都以某种方式呈现。下一步是了解如何呈现所有这些内容。

## 呈现逻辑

### 从数据库加载作品集

请求 URL `/portfolio` 时，将调用自定义操作以呈现与此 URL 关联的内容项。  
由于 __Autoroute__ 部分提供了此机制，因此 URL 与作品集相关联。  
路由器注册了自定义 URL 并存储了关联的内容项 ID。  
此时，操作将发出数据库请求以完整地加载作品集，包括项目，因为这是使用 __Bag__ 部分建模的方式。

### 显示内容项

有了作品集，就会调用主题引擎来呈现内容项。  
在那一刻发生的事情是，所有想要参与内容项呈现的组件都会被调用（显示驱动程序），每个组件都可以返回表示要呈现的内容的对象（形状）。

在我们的例子中，__Title__ 和 __Bag__ 部分由具有特定驱动程序的自定义模块提供。  
然后，每个 __Text__ 字段都将调用另一个显示驱动程序。  
__Bag__ 部分将递归调用所有可用的显示驱动程序，以呈现它拥有的 `Project` 内容项。

这些驱动程序中的每一个都会返回一个或多个形状，这些形状将添加到名为 `Content` 形状的全局形状的命名区域（或部分）中。

例如，`TitleDisplayDriver` 类将在 `Header` 区域中的主形状中的位置 `5` 返回一个类型为 `TitlePart` 的形状。请参阅 [TitlePartDisplayDriver](../../../../OrchardCore.Modules/OrchardCore.Title/Drivers/TitlePartDisplayDriver.cs#L14-L20)

然后，在主 `Content` 形状的 `Content` 区域中添加了 `BagPart` 形状，位置为 `5`。请参阅 [BagPartDisplayDriver](../../../../OrchardCore.Modules/OrchardCore.Flows/Drivers/BagPartDisplayDriver.cs#L39-L45)。

一旦所有部分和所有字段的所有驱动程序都将它们的形状返回到主 `Content` 形状的特定区域中，Orchard 将查找匹配的模板。  
模板匹配是动态完成的，如果没有为内容类型创建特定的模板，则使用文件 `Content.cshtml`（或 `Content.liquid`）。  
默认模板将遍历它所知道的所有区域，并呈现每个区域中包含的形状。  
请参阅 [Header](../../../../OrchardCore.Modules/OrchardCore.Contents/Views/Content.cshtml#L17)，其中呈现了 `Header` 区域形状（`TitlePart` 形状），以及 [Content](../../../../OrchardCore.Modules/OrchardCore.Contents/Views/Content.cshtml#L24)，其中呈现了 `Content` 区域形状（`BagPart` 和 `TextField` 形状）。

## 自定义模板

我们已经可以看到，许多形状和因此模板用于呈现单个内容项。但是，每个 HTML 片段都可以在本地（对于页面）或全局（对于整个站点）替换。

模板通常由创建相应形状类型的模块提供，但始终可以由主题重新定义。  
主题能够提供自定义模板，这些模板将用于替换默认模板。这意味着可以将模板复制到主题中以进行自定义。

### 替代

当呈现 __Content__ 形状为 HTML 时，它使用如前所述的 `Content.cshtml` 文件。  
类似地，__TitlePart__ 形状将使用 `TitlePart.cshtml` 模板进行转换。

这意味着我们可以从 `OrchardCore.Contents` 模块中复制 `Content.cshtml` 文件以自定义如何呈现 __Content__ 形状。

自定义 `Content.cshtml` 文件的一个问题是，它会更改如何呈现任何内容项。  
原始文件足够通用，以便可用于任何内容类型，并通过按顺序呈现所有预定义区域来实现此目的。  
每个驱动程序都提供了将针对这些预定义区域进行定位的形状，而 `Content.cshtml` 默认模板只是按顺序呈现它们。  
因此，除非您决定更改默认情况下呈现内容项的方式，否则不建议更改此文件，因为它可能会影响太多页面。

为了能够更改特定内容类型的呈现方式，主题引擎提供了 __Alternates__ 的概念。  
替代是可选的形状类型，如果可以找到模板，则应使用该类型。  
替代提供了更具体的自定义模板方式。  
在我们的 Portfolio __Content__ 形状的情况下，添加了一个名为 `Content__Portfolio` 的特殊替代，以便我们可以为仅用于 __Portfolio__ 内容项的 __Content__ 形状提供一个模板。

当形状名称中包含 `__` 时，主题引擎将使用 `-` 替换文件。  
在这种情况下，我们可以提供名为 `Content-Portfolio.cshtml` 的模板，以自定义如何呈现 __Portfolio__ 内容项。  
可以通过复制原始的 `Content.cshtml` 文件或创建全新的文件来创建此文件。

为了能够有选择地为内容项创建模板，许多其他替代可用。  
请参阅 [Content templates](../Templates/README.md#content-templates)

### 自定义部分模板

可能不需要更改 __Content__ 模板，而只需要更改单个部分或字段的呈现方式。  
这些也呈现为形状，并具有可以自定义的特定模板。

__Title__ 部分可以通过为 `TitlePart` 形状创建模板来自定义。请参阅 [`Title`](../Title/README.md)

更改每个内容项的标题的呈现方式意味着创建以下文件之一：

#### TitlePart.cshtml

```html
<h1>@Model.Title</h1>
```

#### TitlePart.liquid

```liquid
<h1>{{ Model.Title }}</h1>
```

假设仅应自定义 __Portfolio__ 内容项的标题，可以使用替代来创建一个专门的模板。内容部分形状具有特定于其父级内容类型的替代，例如 `Portfolio__TitlePart`，请参阅 [Content type, Display type, Part type](../Templates/README.md#contenttype_displaytype__parttype)

此形状的模板文件名为 `Portfolio-TitlePart.cshtml`。

### 自定义字段模板

由于可以将多个相同类型的字段添加到同一内容类型甚至同一内容部分中，因此它们的形状类型不是自定义模板的最佳方式。幸运的是，基于它们的名称可用不同的替代。

有关字段的可用形状替代列表，请参阅 [Content field templates](../Templates/README.md#content-field-templates)

在我们的情况下，__Project__ 具有名为 `Url` 的 __Text__ 字段。在这种情况下，最好要覆盖的形状是 `Project__Url`，它将匹配模板 `Project-Url.cshtml`。

从此字段可访问的模型在此处描述 [Available fields](../ContentFields/README.md#available-fields)

此页面解释了 __Text__ 字段包含一个名为 `Text` 的属性，其中包含字段的值。

为此字段创建链接，我们可以创建以下模板：

#### `Project-Url.cshtml`

```razor
<a href="@Model.Text">External url</a>
```

#### `Project-Url.liquid`

```liquid
<a href="{{ Model.Text }}">External url</a>
```

### 显示类型

当主题引擎呈现内容项时，会提供特定的 __Display Type__ 作为呈现元素将如何使用的上下文。默认显示类型称为 __Detail__。这是用于完整呈现内容项的类型。

当以列表形式呈现内容项时，惯例是使用 `Summary` 显示类型。  
查看我们的 __Portfolio__ 内容项如何呈现，__Project__ 内容项作为列表显示，使用 `Summary` 显示类型。  
替代存在以针对特定显示类型定位模板。  
例如，我们可以创建一个形状 `Content_Summary__Project` 的模板，以定制呈现为列表的 __Project__ 内容项。

在这里，形状名称中的 `_` 被模板名称中的点 (`.`) 替换，并将名称的点部分移动到末尾。

### 内容区域，差异化

如前所述，__Content__ 形状由包含所有由显示驱动程序提供的形状的区域组成，并解释了如何自定义这些形状的模板。

有时需要能够重新组织添加到这些区域中的形状的列表，例如删除列表中的某些形状或将它们移动到不同的位置。

在 __Project__ 主内容形状的情况下，`Content` 区域包含两个形状：

- `TextField`
- `MarkdownPart`

`TitlePart` 形状在名为 `Header` 的区域中呈现。

一些模板帮助程序提供了选择和删除这些形状的方法。

为了从区域中挑选特定的形状，形状被赋予了一个昵称，称为 __Differentiator__。  
这是必要的，因为可以将多个相同的形状类型添加到内容区域中。请参阅 [Shape differentiators](../Templates/README.md#shape-differentiators)

对于 `Url` 文本字段，差异化是 `Project-Url`。对于 __Markdown__ 部分，它是 `MarkdownPart`。

这使我们可以为 __Project__ 自定义 __Content__ 形状模板。

#### `Content-Project.cshtml`

```razor
<article>
    <header>
        @await DisplayAsync(Model.Header.TitlePart)
        @{ Model.Header.Remove("TitlePart") }

        @await DisplayAsync(Model.Header)
    </header>

        @await DisplayAsync(Model.Content["Project-Url"])
        @{ Model.Content.Remove("Project-Url") }

        @await DisplayAsync(Model.Content.MarkdownPart)
        @{ Model.Content.Remove("MarkdownPart") }

        @await DisplayAsync(Model.Content)
    <footer>
        @await DisplayAsync(Model.Footer)
    </footer>
</article>
```

#### `Content-Project.liquid`

```liquid
<article>
    <header>
        {{ Model.Header.TitlePart | shape_render }}
        {% shape_remove_item Model.Header "TitlePart" %}

        {{ Model.Header | shape_render }}
    </header>

        {{ Model.Content["Project-Url"] | shape_render }}
        {% shape_remove_item Model.Content "Project-Url" %}

        {{ Model.Content.MarkdownPart | shape_render }}
        {% shape_remove_item Model.Content "MarkdownPart" %}

        {{ Model.Content | shape_render }}

    <footer>
        {{ Model.Footer | shape_render }}
    </footer>
</article>
```

在这里，已知的形状从各自的区域中提取，然后显式呈现。  
最后，在每个区域中呈现了其余的形状，以防稍后将更多内容字段或部分添加到内容类型中。  
如果不呈现区域，则新元素将不会自动显示。

与呈现从区域中删除的形状不同，可以直接应用一些自定义呈现。  
重用形状的一个优点是，它的模板可能包含一些不易重用的复杂逻辑。  
例如，对于 `MarkdownPart` 形状，这是一个情况，因为它将必须将某些 markdown 内容转换为 HTML。  
但是，`TitlePart` 可以轻松忽略，并且 __Content__ 模板可以直接访问此形状的 `Title` 属性。
