# 模板 (`OrchardCore.Templates`)

模板模块允许编辑器创建自定义的Liquid模板。

## 可用模板

模板可以使用Web编辑器或主题定义。 模板通过其名称进行区分。
Orchard Core不直接呈现HTML，而是通常呈现称为**Shape**的东西，它是表示要呈现的对象的对象，
并具有呈现HTML所需的所有数据和元数据。

在呈现形状时，Orchard Core将查找特定的模板，并将形状传递给此模板。
Orchard Core可以与同一形状匹配许多模板。 这些潜在的模板称为**Alternates**。
一个形状包含一个可接受的模板名称列表（备用项），并将查找提供程序以获取最合适的模板。
例如，在呈现类型为Article的内容项时，将呈现相应的形状将配置为查找处理所有文章的模板，
但也处理列表中使用的文章等。

本文档提供了可用于呈现形状的预定义模板列表。 它使用模板的内部名称以及如果由主题提供，则使用文件名。

## 内容模板

### `Content__[ContentType]`

显示类型为“Detail”时，例如从其自己的URL访问时，将调用此模板以显示内容项。

#### 内容示例

| 模板 | 文件名|
| --------- | ------------ |
| `Content__BlogPost` | `Content-BlogPost.cshtml` |
| `Content__Article` | `Content-Article.cshtml` |

#### 内容可用属性

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Content` | 包含内容部分和字段生成的所有形状的区域形状。 |
| `Model.ContentItem` | 表示模板呈现的当前内容项。 |
| `Model.ContentItem.Content` | 包含内容项的所有数据的JSON对象。 |

### `Content_[DisplayType]__[ContentType]`

显示特定显示类型的内容项时，将调用此模板。
例如，当在列表中显示内容项时，通常使用“Summary”显示类型。

#### 具有显示类型的内容示例

| 模板 | 文件名|
| --------- | ------------ |
| `Content_Summary__BlogPost` | `Content-BlogPost.Summary.cshtml` |
| `Content_Summary__Article` | `Content-Article.Summary.cshtml` |

## 小部件模板

### `Widget__[ContentType]`

在页面上呈现小部件时，将调用此模板。

#### 小部件示例

| 模板 | 文件名|
| --------- | ------------ |
| `Widget__Paragraph` | `Widget-Paragraph.cshtml` |
| `Widget__Blockquote` | `Widget-Blockquote.cshtml` |

#### 小部件可用属性

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Content` | 包含小部件部分和字段生成的所有形状的区域形状。 |
| `Model.ContentItem` | 表示模板呈现的当前内容项。 |
| `Model.ContentItem.Content` | 包含内容项的所有数据的JSON对象。 |
| `Model.Classes` | 附加到小部件的所有类的数组。 |

## 内容部分模板

每个驱动程序都可以自由返回其选择的形状类型，但使用方式是
使用与类型名称匹配的名称的形状呈现内容部分。例如，`HtmlBodyPart`内容部分将返回一个类型为`HtmlBodyPart`的单个形状，
但是`ListPart`返回许多形状，其中之一是`ListPart`。

因此，以下模板列表使用`[ShapeType]`术语，其中
大多数时候它将等于内容部分的名称。 示例使用
常见的内容部分名称，因此是这个原因。

### 属性

呈现内容部分的形状上可用的属性对于每个内容是唯一的
部分。 请参阅每个内容部分的文档。

### `[ShapeType]`

呈现内容部分时，将调用此模板。

#### 形状示例

| 模板 | 文件名|
| --------- | ------------ |
| `HtmlBodyPart` | `HtmlBodyPart.cshtml` |
| `ListPartFeed` | `ListPartFeed.cshtml` |

### `[ShapeType]_[DisplayType]`

在特定显示类型中呈现内容部分形状类型时，将调用此模板。

#### 具有显示类型的形状示例

| 模板 | 文件名|
| --------- | ------------ |
| `HtmlBodyPart_Summary` | `HtmlBodyPart.Summary.cshtml` |

### `[ContentType]_[DisplayType]__[PartType]`

在给定内容类型中呈现给定内容部分类型时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分类型的内容示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog__HtmlBodyPart` | `Blog-HtmlBodyPart.cshtml` |
| `LandingPage__BagPart` | `LandingPage-BagPart.cshtml` |
| `Blog_Summary__HtmlBodyPart` | `Blog-HtmlBodyPart.Summary.cshtml` |
| `LandingPage_Summary__BagPart` | `LandingPage-BagPart.Summary.cshtml` |

### `[ContentType]_[DisplayType]__[PartName]`

在给定内容类型中呈现给定内容部分名称时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分名称的内容示例

| 模板 | 文件名|
| --------- | ------------ |
| `LandingPage__Services` | `LandingPage-Services.cshtml` |
| `LandingPage_Summary__Services` | `LandingPage-Services.Summary.cshtml` |

### `[ContentType]_[DisplayType]__[PartType]__[ShapeType]`

在给定内容类型中呈现给定内容部分类型的形状类型时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分类型和形状的内容示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog__ListPart__ListPartFeed` | `Blog-ListPart-ListPartFeed.cshtml` |
| `Blog_Summary__ListPart__ListPartFeed` | `Blog-ListPart-ListPartFeed.Summary.cshtml` |

### `[ContentType]_[DisplayType]__[PartName]__[ShapeType]`

在给定内容类型中呈现给定内容部分名称的形状类型时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分名称和自定义形状的内容示例

| 模板 | 文件名|
| --------- | ------------ |
| `LandingPage__Services__CustomShape` | `LandingPage-Services-CustomShape.cshtml` |
| `LandingPage_Summary__Services__CustomShape` | `LandingPage-Services-CustomShape.Summary.cshtml` |

### 内容部分显示模式

支持不同显示模式的内容部分还提供以下显示模式备用项。

### `[ShapeType]_[DisplayType]__[DisplayMode]_Display`

在给定内容部分类型，给定显示模式，给定显示类型时，将调用此模板以呈现内容部分。

#### 具有形状类型的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `TitlePart_Summary__CustomMode_Display` | `TitlePart-CustomMode.Display.Summary.cshtml` |


### `[ContentType]_Display__[PartType]__[DisplayMode]`

在给定内容类型中，给定显示模式，不具有显示类型时，将调用此模板以呈现内容部分。

#### 具有部分类型的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `LandingPage_Display__TitlePart__CustomMode` | `LandingPage-TitlePart-CustomMode.Display.cshtml` |

### `[ContentType]_[DisplayType]__[PartType]__[DisplayMode]_Display`

在给定内容类型中，给定显示模式，给定显示类型时，将调用此模板以呈现内容部分。

#### 具有部分类型和显示类型的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `LandingPage_Summary__TitlePart__CustomMode_Display` | `LandingPage-TitlePart-CustomMode.Display.Summary.cshtml` |

### `[ContentType]_Display__[PartName]__[DisplayMode]`

在给定内容类型中，给定显示模式，不具有显示类型时，将调用此模板以呈现内容部分名称。

#### 具有部分名称的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `LandingPage_Display__Services__CustomMode` | `LandingPage-Services-CustomMode.Display.cshtml` |

### `[ContentType]_[DisplayType]__[PartName]__[DisplayMode]_Display`

在给定内容类型中，给定显示模式，给定显示类型时，将调用此模板以呈现内容部分名称。

#### 具有部分名称和显示类型的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `LandingPage_Summary__Services__CustomMode_Display` | `LandingPage-Services-CustomMode.Display.Summary.cshtml` |

### `[ContentType]_Display__[PartType]__[ShapeType]__[DisplayMode]`

在给定内容类型中，给定显示模式，不具有显示类型时，将调用此模板以呈现形状类型的内容部分。

#### 具有部分类型和形状的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog_Display__ListPart__ListPartFeed__CustomMode` | `Blog-ListPart-ListPartFeed-CustomMode.Display.cshtml` |


### `[ContentType]_[DisplayType]__[PartType]__[ShapeType]__[DisplayMode]_Display`

在给定内容类型中，给定显示模式，给定显示类型时，将调用此模板以呈现形状类型的内容部分。

#### 具有部分类型和形状以及显示类型的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog_Summary__ListPart__ListPartFeed__CustomMode_Display` | `Blog-ListPart-ListPartFeed-CustomMode.Display.Summary.cshtml` |

### `[ContentType]_Display__[PartName]__[ShapeType]__[DisplayMode]`

在给定内容类型中，不具有显示类型时，将调用此模板以呈现形状类型的内容部分名称。

#### 具有部分名称和自定义形状但不具有显示类型的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog_Display__Services__ListPartFeed__CustomMode` | `Blog-Services-ListPartFeed-CustomMode.Display.cshtml` |

### `[ContentType]_[DisplayType]__[PartName]__[ShapeType]__[DisplayMode]_Display`

在给定内容类型中，给定显示类型时，将调用此模板以呈现形状类型的内容部分名称。

#### 具有部分名称和自定义形状以及显示类型的显示模式示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog_Summary__Services__ListPartFeed__CustomMode_Display` | `Blog-Services-ListPartFeed-CustomMode.Display.Summary.cshtml` |



### 小部件部分/构造型部分

附加到构造型（例如`Widget`，`Menu`）的内容类型的内容部分也具有以下备用项。这适用于除`Content`之外的任何构造型。

### `[Stereotype]_[DisplayType]__[PartType]`

在给定构造型中呈现内容部分时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分类型的构造型示例

| 模板 | 文件名|
| --------- | ------------ |
| `Widget__HtmlBodyPart` | `Widget-HtmlBodyPart.cshtml` |
| `Widget_Summary__HtmlBodyPart` | `Widget-HtmlBodyPart.Summary.cshtml` 

### `[Stereotype]_[DisplayType]__[PartType]__[PartName]`

在给定构造型中呈现可重用的命名部分时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分类型的构造型示例

| 模板 | 文件名|
| --------- | ------------ |
| `Widget__ServicePart__Services` | `Widget-ServicePart-Services.cshtml` |
| `Widget_Summary__ServicePart__Services` | `Widget-ServicePart-Services.Summary.cshtml` |

### `[Stereotype]_[DisplayType]__[PartType]__[ShapeType]`

在给定构造型类型中呈现自定义形状类型时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分名称和形状类型的构造型示例

| 模板 | 文件名|
| --------- | ------------ |
| `Widget__ListPart__ListPartFeed` | `Widget-ListPart-ListPartFeed.cshtml` |
| `Widget_Summary__ListPart__ListPartFeed` | `Widget-ListPart-ListPartFeed.Summary.cshtml` |

### `[Stereotype]_[DisplayType]__[PartType]__[PartName]__[ShapeType]`

在给定构造型类型中呈现自定义形状类型的可重用命名内容部分时，具有或不具有给定显示类型时，将调用此模板。

#### 具有部分名称和自定义形状的构造型示例

| 模板 | 文件名|
| --------- | ------------ |
| `Widget__ServicePart__Services__CustomShape` | `Widget-ServicePart-Services-CustomShape.cshtml` |
| `Widget_Summary__ServicePart__Services__CustomShape` | `Widget-ServicePart-Services-CustomShape.Summary.cshtml` |

以下备用项可用于支持不同显示模式并附加到构造型的内容部分。

### `[Stereotype]_Display__[PartType]__[DisplayMode]`

该模板在给定的立体类型、给定的显示模式、没有显示类型的情况下呈现内容部分时调用。

#### 显示模式与部件类型而没有显示类型的示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Display__TitlePart__CustomMode` | `Widget-TitlePart-CustomMode.Display.cshtml` |


### `[Stereotype]_[DisplayType]__[PartType]__[DisplayMode]_Display`

该模板在给定的立体类型、给定的显示模式、具有显示类型的情况下呈现内容部分时调用。

#### 具有立体类型和显示类型的显示模式示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Summary__TitlePart__CustomMode_Display` | `Widget-TitlePart-CustomMode.Display.Summary.cshtml` |

### `[Stereotype]_Display__[PartType]__[PartName]__[DisplayMode]`

该模板在给定的立体类型、给定的显示模式、没有给定显示类型的情况下呈现内容部分名称时调用。

#### 显示模式与部件名称而没有显示类型的示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Display__ServicePart__Services__CustomMode` | `Widget-ServicePart-Services-CustomMode.Display.cshtml` |

### `[Stereotype]_[DisplayType]__[PartType]__[PartName]__[DisplayMode]_Display`

该模板在给定的立体类型、给定的显示模式、具有给定显示类型的情况下呈现内容部分名称时调用。

#### 具有部件名称和显示类型的显示模式示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Summary__ServicePart__Services__CustomMode_Display` | `Widget-ServicePart-Services-CustomMode.Display.Summary.cshtml` |

### `[Stereotype]_Display__[PartType]__[ShapeType]__[DisplayMode]`

该模板在给定的内容部件类型中呈现形状类型时，在给定的立体类型、给定的显示模式而没有给定的显示类型的情况下调用。

#### 具有部件类型和形状而没有显示类型的显示模式示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Display__ListPart__ListPartFeed__CustomMode` | `Widget-ListPart-ListPartFeed-CustomMode.Display.cshtml` |

### `[Stereotype]_[DisplayType]__[PartType]__[ShapeType]__[DisplayMode]_Display`

该模板在给定的内容部件类型中呈现形状类型时，在给定的立体类型、给定的显示模式和给定的显示类型的情况下调用。

#### 具有部件类型和形状的显示模式示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Summary__ListPart__ListPartFeed__CustomMode_Display` | `Widget-ListPart-ListPartFeed-CustomMode.Display.Summary.cshtml` |

### `[Stereotype]_Display__[PartType]__[PartName]__[ShapeType]__[DisplayMode]`

该模板在给定的内容部件名称中呈现形状类型时，在给定的立体类型、给定的显示模式而没有给定的显示类型的情况下调用。

#### 具有部件名称和自定义形状而没有显示类型的显示模式示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Display__ListPart__Services__ListPartFeed__CustomMode` | `Widget-ListPart-Services-ListPartFeed-CustomMode.Display.cshtml` |

### `[Stereotype]_[DisplayType]__[PartType]__[PartName]__[ShapeType]__[DisplayMode]_Display`

该模板在给定的内容部件名称中呈现形状类型时，在给定的立体类型、给定的显示模式和给定的显示类型的情况下调用。

#### 具有部件名称和自定义形状的显示模式示例

| 模板 | 文件名 |
| --------- | ------------ |
| `Widget_Summary__ListPart__Services__ListPartFeed__CustomMode_Display` | `Widget-ListPart-Services-ListPartFeed-CustomMode.Display.Summary.cshtml` |

## 内容字段模板

每个驱动程序都可以自由返回其选择的形状类型，但使用方式是使用与字段类型相同的类型名称呈现形状。例如，`TextField`内容字段将返回类型为`TextField`的单个形状，但其他字段可能会返回多个形状。

### 形状属性

呈现为内容字段的形状上可用的属性对于每个内容字段都是唯一的。请参阅每个内容字段的文档。

### `[ShapeType]_[DisplayType]`字段

该模板在给定的显示类型中呈现内容字段类型时调用。

#### 字段和显示类型示例

| 模板 | 文件名 |
| --------- | ------------ |
| `TextField_Summary` | `TextField.Summary.cshtml` |

### `[PartType]__[FieldName]`

该模板在给定的内容部件类型中呈现内容字段名称时，在形状类型与字段类型匹配的情况下，具有或没有给定的显示类型。

#### 部件类型和字段示例

| 模板 | 文件名 |
| --------- | ------------ |
|  `HtmlBodyPart__Description` |  `HtmlBodyPart-Description.cshtml` |
|  `HtmlBodyPart_Summary__Description` |  `HtmlBodyPart-Description.Summary.cshtml` |

### `[ContentType]__[PartName]__[FieldName]`

该模板在给定的内容类型和内容部件名称中呈现内容字段名称时，在形状类型与字段类型匹配的情况下，具有或没有给定的显示类型。

#### 内容类型、部件名称和字段名称示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog__HtmlBodyPart__Description` | `Blog-HtmlBodyPart-Description.cshtml` |
| `LandingPage__Services__Image` | `LandingPage-Services-Image.cshtml` |
| `Blog_Summary__HtmlBodyPart__Description` | `Blog-HtmlBodyPart-Description.Summary.cshtml` |
| `LandingPage_Summary__Services__Image` | `LandingPage-Services-Image.Summary.cshtml` |

### `[ContentType]__[FieldType]`

当形状类型与字段类型匹配时，为给定内容类型呈现内容字段时，调用此模板，带或不带给定的显示类型。

#### 内容类型和字段类型示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog__TextField` | `Blog-TextField.cshtml` |
| `LandingPage__TextField` | `LandingPage-TextField.cshtml` |

### `[FieldType]__[ShapeType]`

当为给定的内容字段类型呈现内容字段形状类型时，带或不带给定的显示类型时，调用此模板。

#### 字段类型和形状类型示例

| 模板 | 文件名|
| --------- | ------------ |
| `CustomField__CustomFieldSummary` | `CustomField-CustomFieldSummary.cshtml` |
| `CustomField_Summary__CustomFieldSummary` | `CustomField-CustomFieldSummary.Summary.cshtml` |

### `[PartType]__[FieldName]__[ShapeType]`

当为给定内容部件类型中的给定内容字段名称呈现内容字段形状类型时，带或不带给定的显示类型时，调用此模板。

#### 部件类型、字段名称和形状类型示例

| 模板 | 文件名|
| --------- | ------------ |
|  `HtmlBodyPart__Description__CustomFieldSummary`| `HtmlBodyPart__Description__CustomFieldSummary.cshtml` |
|  `HtmlBodyPart_Summary__Description__CustomFieldSummary`| `HtmlBodyPart__Description__CustomFieldSummary.Summary.cshtml` |

### `[ContentType]__[PartName]__[FieldName]__[ShapeType]`

当为给定内容类型中的给定内容部件名称中的给定内容字段名称呈现内容字段形状类型时，带或不带给定的显示类型时，调用此模板。

#### 内容类型、部件名称、字段名称和形状类型示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog__HtmlBodyPart__Description__CustomFieldSummary` | `Blog-HtmlBodyPart-Description-CustomFieldSummary.cshtml` |
| `LandingPage__Services__Description__CustomFieldSummary` | `LandingPage-Services-Description-CustomFieldSummary.cshtml` |
| `Blog_Summary__HtmlBodyPart__Description__CustomFieldSummary` | `Blog-HtmlBodyPart-Description-CustomFieldSummary.Summary.cshtml` |
| `LandingPage_Summary__Services__Description__CustomFieldSummary` | `LandingPage-Services-Description-CustomFieldSummary.Summary.cshtml` |

### `[ContentType]__[FieldType]__[ShapeType]`

当为给定内容类型中的给定内容字段类型呈现内容字段形状类型时，带或不带给定的显示类型时，调用此模板。

#### 内容类型、字段类型和形状类型示例

| 模板 | 文件名|
| --------- | ------------ |
| `Blog__TextField__TextFieldSummary` | `Blog-TextField-TextFieldSummary.cshtml` |
| `LandingPage__TextField__TextFieldSummary` | `LandingPage-TextField-TextFieldSummary.cshtml` |

## 形状区分器

区分器在区域中唯一标识形状。在呈现内容项时，形状具有包含所有形状的“Content”属性，这些形状由内容显示驱动程序提供，包括内容部件和内容字段的形状。

区分器可用于配置放置信息（参见[放置文档页面](../../core/Placement/)），或使用这些模板助手访问区域中的特定形状：

### 内容部件区分器

如果形状类型与内容部件名称相同，则形状将命名为`[PartName]`，例如`HtmlBodyPart`，`Services`。
如果形状类型与内容部件名称不同，则为`[PartName]-[ShapeType]`，例如`ListPart-ListPartFeed`

### 内容字段区分器

如果形状类型与内容字段名称相同，则形状将命名为`[PartName]-[FieldName]`，例如`HtmlBodyPart-Description`，`Services-Image`。
如果形状类型与内容字段名称不同，则为`[PartName]-[FieldName]-[ShapeType]`，例如`HtmlBodyPart-Description-CustomFieldSummary`，`Services-Image-ImageFieldSummary`

#### Razor

按名称访问特定形状：

```csharp
Model.Content.HtmlBodyPart;
Model.Content.Named("ListPart-ListPartFeed");
```

按名称删除特定形状：

```csharp
Model.Content.Remove("HtmlBodyPart");
```

#### Liquid

删除特定名称的形状后显示形状：

```liquid
{% shape_remove_item Model.Content "HtmlBodyPart" %}
{{ Model.Content | shape_render }}
```

按名称显示特定形状：

```liquid
{{ Model.Content.HtmlBodyPart | shape_render }}
```


要访问或呈现直接添加到内容类型的字段的形状，`[PartName]`等于内容类型。
例如，给定一个名为`Article`的内容类型，其中包含一个名为`Description`的文本字段，此字段的形状将命名为`"Article-Description"`。要在Liquid中呈现这些形状：

```liquid
{{ Model.Content["Article-Description"] | shape_render }}
```

在此示例中，需要使用索引语法，因为不同iator的名称与Liquid语言不兼容。

您还可以访问其属性而不是直接呈现形状。在文本字段的情况下，您可以访问具有`Text`属性的`Field`属性。

```liquid
{{ Model.Content["Article-Description"].Field.Text }}
```

## 区域模板

### `Zone__[ZoneName]`

在显示布局区域时调用此模板。

#### 区域示例

| 模板 | 文件名|
| --------- | ------------ |
| `Zone__Footer` | `Zone-Footer.cshtml` |
| `Zone__Content` | `Zone-Content.cshtml` |

可用的区域取决于当前主题。

!!! 注意
    `Zone__Content`替代品是用于名为“Content”的布局区域的，它（根据主题）通常是页面的主要内容区域。它与内容项形状无关。

#### 区域可用属性

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Items` | 虽然该属性不是区域形状的唯一属性，但请记住，`Model.Items`包含应从该区域显示的子形状。在Razor中，但不在Liquid中，直接迭代`Model`也会产生子形状。 |
| `Model.Parent` | 当前区域的父区域形状。对于根级区域，这将是布局形状。 |
| `Model.ZoneName` | 区域的`string`名称，例如“Footer”和“Content”。 |

=== "Liquid"

    ``` liquid
    {% for shape in Model.Items %}
        {{ shape | shape_render }}
    {% endfor %}
    ```

=== "Razor"

    ``` csharp
    @foreach (var shape in Model)
    {
        @await DisplayAsync(shape);
    }
    ```

## 覆盖视图

某些模块（即OrchardCore.Users模块）允许您在主题中覆盖其某些视图。由于这些视图不是形状，因此覆盖它们的方式与上述方式略有不同。

### 视图解析路径

`ThemeViewLocationExpanderProvider.cs`文件定义了RazorViewEngine使用的搜索路径。

```
Views/{2}/{1}/{0}.cshtml
Views/{2}/Shared/{0}.cshtml
Views/Shared/{0}.cshtml
```

- 2 = 区域/模块
- 1 = 控制器
- 0 = 操作

### 覆盖登录视图

例如，如果要覆盖`OrchardCore.Users\Views\Account\Login.cshtml`视图，则需要在主题中创建一个文件，并将其放置在`YourTheme\Views\OrchardCore.Users\Account\Login.cshtml`下。  
对于此特定文件，您还需要在管理中选择“配置->登录”页面下的“使用站点主题作为登录页面”选项。


> 该文档由ChatGPT 4 翻译