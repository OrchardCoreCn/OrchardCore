# 创建一个Liquid Widget

Widget是一种可重复使用的内容类型，可以被多个功能所使用，例如`Layers`和`Flows`，可以添加Widget到你的内容中。

Liquid Widget允许编辑使用liquid模板语言编写内容，包括html，css和javascript。

!!! warning
    如果出于安全原因，不希望编辑器能够编写javascript，请勿创建此内容类型。

## 你将构建什么

你将向内容类型定义添加一个Liquid Widget，并创建一个模板。

## 你需要什么

- 一个运行的Orchard Core CMS网站。

## 创建Liquid Widget

- 进入Orchard Core CMS管理区域<https://localhost:5001/admin>
- 通过管理菜单选择_Content -> Content Definition -> Content Types_

- 选择 `Create new type` 并输入显示名称 `Liquid Widget`。技术名称将自动填充，删除空格。然后选择`Create`

- 接下来将显示`Add content parts`视图，从中选择 `Liquid`，然后保存您的新内容类型。

- 当内容类型保存后，视图将返回到您的新Liquid Widget的定义。

- 删除 `Creatable` 和 `Listable` 标志，因为这些标志是用于在内容管理列表中显示的内容类型。我们正在构建一个将被 `Layers` 和 `Flows` 功能使用的小部件。

- 将 `Widget` 添加到`Stereotype`，然后选择 `Save`。

现在，您的小部件可以从 `Layers` 模块中使用，方法是导航到 _Design -> Widgets_，也可以从 `Page` 内容类型中使用。

继续阅读以了解如何自定义模板。

##创建 Liquid Widget 模板

默认情况下，此小部件将使用标准小部件模板。
标准模板包含包装 div，您可能希望使用自己的 HTML 或 CSS 类定制此小部件的呈现方式。

``` html
<div class="widget widget-liquid-widget widget-align-justify widget-size-100">
    <div class="widget-body">
        <!-- The content of your widget is rendered here. -->
    </div>
</div>
```

要自定义此模板：

- 导航到“_设计->模板_”

- 选择 `添加模板` 并将模板命名为 `Widget__LiquidWidget`

- 创建以下内容的模板
{{ Model.Content.LiquidPart | shape_render }}

```

此模板仅呈现“ LiquidPart”，并将覆盖默认的小部件模板。

您还可以选择呈现小部件大小调整类。

```liquid
<article class="{{ Model.Classes | join: " " }}">
    {{ Model.Content.LiquidPart | shape_render }}
</article>
```

Razor 模板的名称为 `Widget-LiquidWidget.cshtml`

```html
<article class="@String.Join(" ", Model.Classes.ToArray())">
    @await DisplayAsync(Model.Content.LiquidPart)
</article>
```
## 笔记

您可以使用此技术构建更复杂的小部件，其中可能包含多个字段或部分。

## 摘要

您刚学会了如何添加创建一个Liquid小部件和模板。


> 该文档由Chat-GPT 翻译
