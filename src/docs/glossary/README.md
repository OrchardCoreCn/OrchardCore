# 术语表

列出了在Orchard Core中可以找到的术语和概念。

它们按角色分组：用户、主题设计师、管理员。

## 已认证用户

### 内容项

包含特定内容类型的一些内容的单个文档，可以进行版本控制和本地化。内容项的示例包括页面、博客文章和产品。它们通常与站点上的唯一URL（地址）相关联。

### 内容项版本

表示内容项的特定版本的单个文档。它们可以是草稿、已发布或过去的版本。

### 内容类型

定义内容部件和内容字段的列表，这些内容部件和内容字段可以组成一个内容项。一种类比是将它们与类进行比较，其实例是内容项。

### 内容部分

内容部分是构建特定一致行为所需的元素，并且可以在内容类型之间重复使用。
详情请参见[内容部分（Content Parts）](../reference/modules/ContentParts/README.md)

### 内容字段

内容字段是使用命名数据扩展内容类型的组成部分。一个内容类型或内容部分可以有多个相同类型的字段附加。例如，__描述__ 可以是定义在 __产品__ 内容类型上的字段。 
详情请参见[内容字段（Content Fields）](../reference/modules/ContentFields/README.md)

### 显示类型

指定内容元素在哪种上下文中呈现的方式（例如：详细信息，摘要，概述管理）。每种显示类型都可以定义一个模板。

### 字段编辑器

一个字段可以有不同的编辑器（例如：数值字段的值可以通过输入或滑块进行设置）。

### Autoroute动态创建并注册访问内容项的URL的部分。可以使用Liquid模式进行自动生成。
请参见[Autoroute](../reference/modules/Autoroute/README.md)。

### Bag

父级内容项中特定类型的内容项的集合。内容项在包中聚合。

### List

父容器的内容项列表（例如，博客包含博客文章列表）。内容项已被引用。
请参见[列表](../reference/modules/Lists/README.md)。

### 分类法

用作其他内容项引用的内容项的层次结构。也称为托管词汇表。例如，颜色列表，然后可以将其附加到产品。通常也用于文章类别等。
请参阅[分类法](../reference/modules/Taxonomies/README.md)。

### 管理菜单菜单项的层次结构，显示在站点的管理部分中。
详见[管理菜单](../reference/modules/ AdminMenu/ README.md)

### 别名

允许指定别名的一部分。一种使用关键字标识项目，以便调用它而不是使用 ID 的方法。
详见[别名](../reference/modules/Alias/README.md)

### 内容预览

允许预览和实时编辑内容。
详见[内容预览](../reference/modules/ContentPreview/README.md)

### 索引

定义内容索引的方式，以便从查询中搜索它。
详见[索引](../reference/modules/Indexing/README.md)

### 查询管理员定义的参数化Lucene或SQL查询。参见[Queries](../reference/modules/Queries/README.md)

### 租户

具有自己的URL的独立子站点。  
一个实例可以有多个租户。  
它们只能在默认的一个中进行管理。  
参见[Tenants](../reference/modules/Tenants/README.md)

## 主题设计师

### 主题

包含资产（图像、样式、脚本）和视图的模块，用于自定义显示。  
它还可以包含一个配方来初始化某些内容类型和内容项。

### Liquid资源

一个可以在视图中代替Razor或模板语法的语法。参见 [Liquid](../reference/modules/Liquid/README.md)。

### 替代模板

使用主题中的文件来覆盖内容类型、部分或字段。参见 [替代模板](../reference/modules/Templates//README.md#shape-differentiators)。

### 布局

一个映射文件，用于设置特定内容类型、部分/字段名称或主题中的显示类型的出现顺序或隐藏内容。参见 [布局](../reference/core/Placement/README.md)。

### 资源

在管理界面中，这是媒体库。在主题中，这些是位于wwwroot文件夹中的文件夹。样式或脚本库是使用特定版本和可能被压缩的文件以及CDN网址注册的。  
请参见[资源](../reference/modules/Resources/README.md)。

### 形状

[GitHub讨论](https://github.com/OrchardCMS/OrchardCore/issues/4121#issuecomment-539608731)

### 模板

Liquid中管理员对形状或显示类型的重写。  
请参见[模板](../reference/modules/Templates/README.md)。

### 区域

布局中的部分（例如页脚），可以在其中呈现项。

### 层

显示规则，其中您可以指定要呈现的条件（例如isHomepage（））。查看[图层(Layers)](../reference/modules/Layers/README.md)

### 构造型

默认情况下，内容项(Content Items)没有构造型(stereotype)，但是某些模块将会使用定义好的构造型来确定哪些内容类型可以被它们使用。    
其中的例子包括菜单构造型(Munu Stereotype)和小部件构造型(Widget Stereotype)。

### 小部件

一种在特定区域和层(layer)中显示的内容项。   
它在其内容定义中具有`Widget`构造型。

### 流程

可向其中添加小部件的页面布局。   
请参见[流程(Flow)](../reference/modules/Flow/README.md)。

### 短代码

短代码是一小段包含在[方括号]中的代码，可以为内容编辑器添加一些行为，例如嵌入媒体文件。请查看[简码](../reference/modules/Shortcodes/README.md)

## 管理员

### 配方

用于执行不同导入和配置步骤的JSON文件。

### 安装配方

在安装期间执行的导入步骤，例如设置主题、定义类型、导入数据等。

### 权限

允许角色中的用户访问特定操作。

### 部署步骤

特定信息集的导出（配置或数据）。### 部署计划

一个批量导出工具，可以执行多个部署步骤。
$END_OF_FILE$