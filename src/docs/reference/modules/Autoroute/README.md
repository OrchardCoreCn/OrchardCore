# Autoroute (`OrchardCore.Autoroute`)

该模块允许您为内容项指定自定义URL（永久链接）。

## Autoroute部分

将此部分附加到内容类型以指定内容项的自定义URL。

然后，转到内容类型的定义并编辑Autoroute部分：

- 使用Liquid表达式输入模式，该表达式将表示生成的slug。

例如，对于使用它来生成slug的TitlePart的内容：

```liquid
{{ ContentItem | display_text | slugify }}
```

例如，对于具有ListPart和TitlePart（例如：嵌套在Blog中的BlogPost）的内容类型，它将使用容器和标题来生成slug：

```liquid
{{ ContentItem | container | display_text | slugify }}/{{ ContentItem | display_text | slugify }}
```

- 如果您想在编辑内容项时能够输入自定义路径，请勾选“允许自定义路径”。
- 如果您想将内容项设置为主页，请勾选“显示主页选项”。

### 在模式中使用字段

字段也可以用作模式的一部分。以下示例使用名为Color的Text字段，在Product内容类型上。将文本slugify，以使其与URL兼容。

```liquid
{{ ContentItem.Content.Product.Color.Text | slugify }}
```

## Autoroute Slug Handle

具有Autoroute的内容项可以通过URL检索，您可以在任何可以通过其slug handle检索内容的地方检索它（请参见下面的示例）。其语法为`slug：<URL>`，例如`slug：my-blog/my-blog-post`。

## Liquid

启用Autoroute后，您可以在Liquid视图和模板中通过URL检索内容：

```liquid
{% assign my_content = Content["slug:my-blog/my-blog-post"] %}
```

或

```liquid
{% assign my_content = Content.Slug["my-blog/my-blog-post"] %}
```

## 容器路由

AutoroutePart支持父内容项的子项的路由。

### 容器和包含的定义

### 容器定义

容器内容项是父内容项，其中包含子内容项。

例如：

- 附加了BagPart的内容项是容器或父内容项。

- Taxonomy也是容器内容项。

### 包含的定义

包含的内容项是指包含在容器内容项中的内容项。

例如：

- 包含在BagPart中的内容项被视为包含或子内容项。

- Taxonomy的术语由Taxonomy包含。

包含的内容项作为json的一部分存储在容器文档中。

### 支持的容器

AutoroutePart支持这些容器类型的路由。

- BagPart和由BagPart包含的内容项
- Taxonomy内容项和包含的术语

### 配置

要启用包含的内容项的路由，必须正确配置AutoroutePart。

- 将AutoroutePart添加到容器或父内容类型定义中。
- 在AutoroutePart设置上启用“允许包含项路由”。
- 在容器内容项上启用“路由包含项”。

可选地，将AutoroutePart添加到包含或子内容项的内容类型定义中，以管理包含项路由。

#### 路径生成

默认情况下，当将AutoroutePart添加到容器内容项中并启用“路由包含项”时，生成的路由将由容器段、包含的内容项的ContentItemId以及如果存在，则由DisplayText组成。

例如：
`https://www.mysite.com/categories/47twnxzx9hs5k3dyn9j1mc5rny-travel`

要为子内容项添加友好的slug，请将AutoroutePart添加到这些内容类型的内容类型定义中。

然后，您可以使用Liquid模式生成友好的slug。

例如：
`https://www.mysite.com/categories/travel`

路由默认相对于父级，并且与ListPart不同，没有父级的Liquid过滤器

#### AutoroutePart设置

AutoroutePart上的设置允许站点管理员控制如何为用户启用容器和包含的路由。

##### 允许路由包含项

在容器内容类型定义上启用此选项，即父项，以允许用户为包含的即子项启用路由。

##### 管理包含项路由

在包含的内容类型定义上启用此选项，即子项，以允许AutoroutePart配置对这些内容项的单独路由控制。

##### 允许绝对路径

启用`AllowAbsolutePath`以允许用户将路径设置为绝对路径。

默认情况下，容器路由将构建相对于容器路由的URL。

##### 允许禁用

启用此选项以允许内容编辑器禁用路由生成。

当您有两个BagParts的容器并且应仅启用一个路由时，请使用此选项。

#### AutoroutePart内容项编辑器

##### 禁用

内容编辑器可以选择禁用特定的包含内容项的路由生成。

##### 路由包含项

在编辑内容项时，在容器内容项上选择“路由包含项”以为包含的内容项启用路由。

##### 绝对

当选中时，将路由从相对路由强制为绝对路由。

!!! 注意
    当您为BagPart配置容器路由时，您将希望更改部分的默认显示类型为“Summary”，并创建“Summary”和“Detail”模板，以在其容器中显示该项时使用，以及在通过路由详细显示时使用。

> 该文档由ChatGPT 4 翻译
