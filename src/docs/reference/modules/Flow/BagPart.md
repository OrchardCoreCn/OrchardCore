# Bag (`OrchardCore.Flows`)

BagPart是Flows模块的一部分，是一个可以直接包含多种类型内容项的内容部分。

它们作为一个单一文档存储在数据库中，这使它们非常强大。

BagPart在设计上与FlowPart有很多相似之处，不同之处在于，使用BagPart可以精确指定可以包含其中的内容类型。

这是通过BagPart的设置完成的。

BagPart也可以作为NamedPart添加到Content Type Definition中。这允许一个容器项具有多个BagParts。

在TheAgencyTheme中可以找到使用四个Named BagParts的示例。

- Services
- Portfolio
- About
- Team

## 以分离的方式进行模板化。

在以分离的方式进行模板化时，可以通过BagPart的名称直接访问内容项。

=== "Liquid"

    ``` liquid
    {% for service in Model.ContentItem.Content.Services.ContentItems %}
        <h4 class="service-heading">{{ service.DisplayText }}</h4>
        <p class="text-muted">{{ service.HtmlBodyPart.Html | raw }}</p>
    {% endfor %}
    ```

=== "Razor"

    ``` html
    @foreach (var item in Model.ContentItem.Content.Services.ContentItems)
    {
        <h4 class="service-heading">@item.DisplayText</h4>
        <p class="text-muted">@Html.Raw(item.Content.HtmlBodyPart.Html)</p>
    }
    ```

在此示例中，Services是一个Named BagPart。

## 使用显示管理进行模板化

在Liquid中，使用`shape_build_display`过滤器对包含的项进行构建，以构建内容项的显示形状，然后使用`shape_render`过滤器来呈现这些形状。

在Razor中，使用`IContentItemDisplayManager`对包含的项进行构建，以调用`BuildDisplayAsync`来构建内容项的显示形状，然后使用`DisplayAsync`来呈现这些形状。

=== "Liquid"

    ``` liquid
    <section class="flow">
        {% for item in Model.ContentItems %}
            {{ item | shape_build_display: "Detail" | shape_render }}
        {% endfor %}
    </section>
    ```

=== "Razor"

    ``` html
    @using OrchardCore.Flows.ViewModels

    @model BagPartViewModel
    @inject OrchardCore.ContentManagement.Display.IContentItemDisplayManager ContentItemDisplayManager

    <section class="flow">
        @foreach (var item in Model.BagPart.ContentItems)
        {
            var itemContent = await ContentItemDisplayManager.BuildDisplayAsync(item, Model.BuildPartDisplayContext.Updater, Model.Settings.DisplayType ?? "Detail", Model.BuildPartDisplayContext.GroupId);

            @await DisplayAsync(itemContent)
        }
    </section>
    ```

## 模板备选项

BagPart支持标准备选项，对于Named BagPart，您可以在备选项中包含部分名称。

`MyBag-BagPart.liquid`

`MyBag-MyNamedBagPart.liquid`

在第一个示例中，我们有一个备选项，指定Content Type `MyBag`和`BagPart`。

在第二个示例中，我们有一个Content Type `MyBag`和Named BagPart `MyNamedBagPart`的备选项。

包含在BagPart中的Content Items的模板和备选项名称与标准Content Items相同。

!!! note
    更多备选项可用。您可以使用`ConsoleLog` Razor Helper或`console_log`Liquid过滤器来检查这些备选项。

## 放置差异化

BagPart的名称用作`placement.json`中的差异化器。

```json
  "BagPart": [
    {
      "differentiator": "MyNamedBagPart"
    }
  ]
```
> 该文档由ChatGPT 4 翻译
