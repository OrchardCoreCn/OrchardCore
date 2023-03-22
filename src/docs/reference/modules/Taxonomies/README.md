# 分类法 (`OrchardCore.Taxonomies`)

该模块提供了一个分类法内容类型，用于定义任何类型的受管理词汇表（类别）。分类法内容项由组织成层次结构的术语组成。使用分类法字段，可以将任何内容项与分类法的一个或多个术语相关联。

## Shapes

### TaxonomyPart

启用 `AutoroutePart` 设置中的 `Container routing` 功能，可以使分类法的显示可路由。然后，`TaxonomyPart` 形状呈现分类法的显示。

这使用 `TermShape` 来显示 `Terms` 的层次结构。

### TermPart

当使用 `AutoroutePart` 的 `Container routing` 功能显示 `Term` 时，将呈现 `TermPart`。

它呈现了由 `TaxonomyField` 分类为该 `Term` 层次结构的所有内容项的列表。

### Term Shape

`TermShape` 由 `TaxonomyPart` 显示使用，以呈现 `Taxonomy` 的术语层次结构的列表。

它是一个可重用的形状，也可以从内容项中调用，类似于 `MenuShape`，以呈现整个 `Taxonomy` 和术语层次结构，或 `Term` 层次结构的一部分。

您可以从内容模板中调用 `TermShape`，以呈现相关分类法术语的侧边栏。

=== "Liquid"

    ``` liquid
    {% shape "term", alias: "alias:Categories" %}
    ```

=== "Razor"

    ``` html
    <shape type="Term" alias="alias:Categories" />
    ```

您还可以指定 `TermContentItemId` 以呈现术语层次结构的一部分。

=== "Liquid"

    ``` liquid
    {% shape "term", TaxonomyContentItemId: "taxonomyContentItemId" TermContentItemId: "termContentItemId" %}
    ```

=== "Razor"

    ``` html
    <shape type="Term" TaxonomyContentItemId="taxonomyContentItemId" TermContentItemId="termContentItemId" />
    ```

| 属性 | 描述 |
| --------- | ------------ |
| `Model.TaxonomyContentItemId` | 如果定义，则包含要呈现的分类法的内容项标识符。 |
| `Model.Items` | 用于分类法的术语项形状的列表。这些是类型为 `TermItem` 的形状。 |
| `Model.Differentiator` | 如果定义，则包含分类法的格式化名称（显示文本）。例如 `Categories`。 |
| `Model.TermContentItemId` | 如果定义，则包含要开始呈现层次结构的术语的内容项标识符。 |

#### Term Alternates

| 定义 | 模板 | 文件名 |
| ---------- | --------- | ------------ |
| `Term__[Differentiator]` | `Term__Categories` | `Term-Categories.cshtml` |
| `Term__[ContentType]` | `Term__Category` | `Term-Category.cshtml` |

#### Term Examples

=== "Liquid"

    ``` liquid
    <ul class="list-group list-group-flush {{ Model.Classes | join: " " }}">
        {% for item in Model.Items %}
            {% shape_add_classes item "list-group-item border-0 pb-0" %}
            {{ item | shape_render }}
        {% endfor %}
    </ul>
    ```

=== "Razor"

    ``` html
    @model dynamic
    @if ((bool)Model.HasItems)
    {
        TagBuilder tag = Tag(Model, "ul");

        foreach (var item in Model.Items)
        {
            tag.InnerHtml.AppendHtml(await DisplayAsync(item));
        }

        @tag
    }
    else
    {
        <p class="alert alert-warning">@T["The list is empty"]</p>
    }
    ```

### TermItem

`TermItem` 形状用于呈现术语项。

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Term` | 拥有此项的 `Term` 形状。 |
| `Model.TaxonomyContentItem` | `TaxonomyContentItem`。 |
| `Model.TermContentItem` | 此项的 `TermContentItem`。 |
| `Model.Level` | 术语项的级别。顶级术语项为 `0`。 |
| `Model.Items` | 子术语项形状的列表。这些是类型为 `TermItem` 的形状。 |
| `Model.Terms` | 层次结构中较低项的术语内容项列表。 |
| `Model.Differentiator` | 如果定义，则包含分类法的格式化名称。例如 `Categories`。 |

!!! note
    使用 `TermContentItemId` 属性呈现术语的部分层次结构时，级别始终基于分类法根。

#### TermItem Alternates

| 定义 | 模板 | 文件名 |
| ---------- | --------- | ------------ |
| `TermItem__level__[level]` | `TermItem__level__2` | `TermItem-level-2.cshtml` |
| `TermItem__[ContentType]` | `TermItem__Category` | `TermItem-Category.cshtml` |
| `TermItem__[ContentType]__level__[level]` | `TermItem__Category__level__2` | `TermItem-Category-level-2.cshtml` |
| `TermItem__[Differentiator]` | `TermItem__Categories` | `TermItem-Categories.cshtml` |
| `TermItem__[Differentiator]__level__[level]` | `TermItem__Categories__level__2` | `TermItem-Categories-level-2.cshtml` |
| `TermItem__[Differentiator]__[ContentType]` | `TermItem__Categories__Category` | `TermItem-Categories-Category.cshtml` |
| `TermItem__[Differentiator]__[ContentType]__level__[level]` | `TermItem__Categories__ContentType__level__2` | `TermItem-Categories-Category-level-2.cshtml` |

#### TermItem Example

=== "Liquid"

    ``` liquid
    <li class="list-group-item border-0 pb-0">
        {% shape_clear_alternates Model %}
        {% shape_type Model "TermContentItem" %}
        {{ Model | shape_render }}
        {% if Model.HasItems %}
            <ul class="list-group list-group-flush">
                {% for item in Model.Items %}
                    {% shape_add_classes item "list-group-item border-0 pb-0" %}
                    {{ item | shape_render }}
                {% endfor %}
            </ul>
        {% endif %}
    </li>
    ```

=== "Razor"

    ``` html
    @model dynamic
    @{
        // Morphing the shape to keep Model untouched
        Model.Metadata.Alternates.Clear();
        Model.Metadata.Type = "TermContentItem";
    }
    <li>
        @await DisplayAsync(Model)
        @if ((bool)Model.HasItems)
        {
            <ul>
                @foreach (var item in Model.Items)
                {
                    @await DisplayAsync(item)
                }
            </ul>
        }
    </li>
    ```

### TermContentItem

`TermContentItem` 形状用于呈现术语内容项。
此形状通过将 `TermItem` 形状变形为 `TermContentItem` 来创建。因此，所有属性
在 `TermItem` 形状上可用。

| 属性 | 描述 |
| --------- | ------------ |
| `Model.Term` | 拥有此项的 `Term` 形状。 |
| `Model.TaxonomyContentItem` | `TaxonomyContentItem`。 |
| `Model.TermContentItem` | 此项的 `TermContentItem`。 |
| `Model.Level` | 术语项的级别。顶级术语项为 `0`。 |
| `Model.Items` | 子术语项形状的列表。这些是类型为 `TermItem` 的形状。 |
| `Model.Terms` | 层次结构中较低项的术语内容项列表。 |
| `Model.Differentiator` | 如果定义，则包含术语的格式化名称。例如 `Travel`。 |

#### TermContentItem Alternates

| 定义 | 模板 | 文件名 |
| ---------- | --------- | ------------ |
| `TermContentItem__level__[level]` | `TermContentItem__level__2` | `TermContentItem-level-2.cshtml` |
| `TermContentItem__[ContentType]` | `TermContentItem__Category` | `TermContentItem-Category.cshtml` |
| `TermContentItem__[ContentType]__level__[level]` | `TermContentItem__Category__level__2` | `TermContentItem-Category-level-2.cshtml` |
| `TermContentItem__[Differentiator]` | `TermContentItem__Categories` | `TermContentItem-Categories.cshtml` |
| `TermContentItem__[Differentiator]__level__[level]` | `TermContentItem__Categories__level__2` | `TermContentItem-Categories-level-2.cshtml` |
| `TermContentItem__[Differentiator]__[ContentType]` | `TermContentItem__Categories__Category` | `TermContentItem-Categories-Category.cshtml` |
| `TermContentItem__[Differentiator]__[ContentType]__level__[level]` | `TermContentItem__Categories__Category__level__2` | `TermContentItem-Categories-Category-level-2.cshtml` |

#### TermContentItem Example

=== "Liquid"

    ``` liquid
    {{ Model.TermContentItem | shape_build_display: "Summary" | shape_render }}
    ```

=== "Razor"

    ``` html
    @model dynamic

    @await Orchard.DisplayAsync(Model.TermContentItem as ContentItem, "Summary")
    ```

### TaxonomyField

当 `TaxonomyField` 附加到内容部分时，将呈现此形状。
形状基类的类型为 `DisplayTaxonomyFieldViewModel`。

在 `TaxonomyField` 类上可用以下属性。
| 属性 | 类型 | 描述 |
| --------- | ---- |------------ |
| `TaxonomyContentItemId` | `string` | 与字段关联的分类法的内容项 ID。 |
| `TermContentItemIds` | `string[]` | 为此字段选择的术语的内容项 ID 列表。 |

### DisplayTaxonomyFieldViewModel

此类用于显示字段。

`DisplayTaxonomyFieldViewModel` 类上可用以下属性。

| 属性 | 类型 | 描述 |
| --------- | ---- |------------ |
| `Field` | `TaxonomyField` | `TaxonomyField` 实例 |
| `Part` | `ContentPart` | 附加到此字段的部分 |
| `PartFieldDefinition` | `ContentPartFieldDefinition` | 部分字段定义 |

## Orchard 帮助程序

### GetTaxonomyTermAsync

按其内容项 ID 和分类法返回术语。

```csharp
@foreach(var termId in Model.TermContentItemIds)
{
    @await Orchard.GetTaxonomyTermAsync(Model.TaxonomyContentItemId, termId);
}
```

### GetInheritedTermsAsync

返回包括其父项的术语列表。

```csharp
@foreach(var termId in Model.TermContentItemIds)
{
    <div>
    @foreach(var parent in await Orchard.GetInheritedTermsAsync(Model.TaxonomyContentItemId, termId))
    {
        @parent
    }
    </div>
}
```

### QueryCategorizedContentItemsAsync

提供一种查询已分类为特定术语的内容项的方法。

#### QueryCategorizedContentItemsAsync 示例 

以下示例查询与当前内容项相关的内容项，但排除当前内容项的术语类别。

```csharp
@using YesSql.Services
@{
    var termContentItemIds = Model.TermContentItemIds;
    var contentItems = await Orchard.QueryCategorizedContentItemsAsync(
        query => query.Where(index => index.TermContentItemId.IsIn(termContentItemIds) &&
            index.ContentItemId != Model.Part.ContentItem.ContentItemId));

    foreach(var contentItem in contentItems)
    {
        ...
    }
}
```

## Liquid 标签

### taxonomy_terms

`taxonomy_terms` 过滤器加载指定的术语内容项。

#### taxonomy_terms 示例 

以下示例列出与 **BlogPost** 内容类型上的 **Colors** 字段相关的所有术语，然后呈现它们。

```liquid
{% assign colors = Model.ContentItem.Content.BlogPost.Colors | taxonomy_terms %}
{% for c in colors %}
  {{ c }}
{% endfor %}
```

只要第一个参数是分类法内容项 ID，`taxonomy_terms` 也接受术语内容项 ID 作为输入。

#### 示例

以下示例显示所有颜色及其层次结构：

```liquid
{% assign taxonomyId = Model.ContentItem.Content.BlogPost.Colors.TaxonomyContentItemId %}

{% for colorId in Model.ContentItem.Content.BlogPost.Colors.TermContentItemIds %}
  <div>
    {% assign parentColors = colorId | inherited_terms: taxonomyId %}
    {% for c in  parentColors %}
      {{ c }}
    {% endfor %}
  </div>
{% endfor %}
```

### inherited_terms

`inherited_terms` 过滤器加载给定术语的所有父项。  
输入必须是术语内容项或内容项 ID。  
第一个参数必须是分类法内容项或内容项 ID。

## 分类法索引

`TaxonomyIndex` SQL 表包含与分类法字段关联的所有内容项的列表。  
每个记录对应于字段的选择术语。

| 列 | 类型 | 描述 |
| --------- | ---- |------------ |
| TaxonomyContentItemId | `string` | 分类法的内容项 ID |
| ContentItemId | `string` | 分类的内容的内容项 ID |
| ContentType | `string` | 分类的内容的内容类型 |
| ContentPart | `string` | 包含字段的内容部分 |
| ContentField | `string` | 内容部分中字段的名称 |
| TermContentItemId | `string` | 分类术语的内容项 ID |

例如，如果字段有两个选择的术语，则将有两个记录，所有列值都相同，除了 `TermContentItemId`。

## 标签

标签是用于允许在编辑时对内容项进行标记的编辑器和显示模式。

使用 `Tags` 模式时，存储标签的显示文本属性以及 `TermContentItemId`。

您可以使用以下访问器直接访问 `TagNames` 属性：

=== "Liquid"

    ``` liquid
    {% for tagName in Model.ContentItem.Content.BlogPost.Tags.TagNames %}
        <span class="badge bg-secondary">
            <i class="fas fa-tag fa-xs fa-rotate-90 align-middle" aria-hidden="true"></i>
            <span class="align-middle"> {{ tagName }} </span> 
        </span>
    {% endfor %}
    ```

=== "Razor"

    ``` html
    @foreach (var tagName in Model.ContentItem.Content.BlogPost.Tags.TagNames)
    {
        <span class="taxonomy-tag-term">@tagName</span>
    }
    ```

!!! note
    如果术语的显示文本属性已更新，则需要重新发布任何内容项才能反映此更改。

## 分类法内容列表过滤器

在管理内容列表中提供分类法过滤器。

## Videos

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/DpaN02c2sDI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/nyPgQMwizbU" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
