# 图层 (`OrchardCore.Layers`)

图层可以从管理页面的 `设计 > 小部件` 进行管理。

一个图层有一个名称、一个描述和一个规则，您可以在其中指定一个条件来呈现将与此图层关联的小部件。

当您将小部件添加到区域中时，您还必须选择一个图层来关联小部件。如果图层的相应显示规则计算为 true，则显示小部件。

您可以选择每个图层旁边的复选框，以突出显示左侧的关联小部件。

在规则中，您可以指定多个必须计算为 true 的条件，或者您可以使用条件组 `All` 或 `Any` 来变化规则。

!!! 注意
    图层规则已从单个 javascript 规则升级为条件，在 RC2 中，因此此文档可能因您的版本而异。
    迁移将现有的 javascript 规则转换为匹配条件或 javascript 条件。

## 条件

以下是一些可用的条件：

| 条件 | 描述 |
| -------- | ----------- |
| `Homepage` | 当前页面是否为站点主页 |
| `Is anonymous` | 当前用户是否匿名，即未经身份验证。 |
| `Is authenticated` | 当前用户是否已通过身份验证。 |
| `Role` | 角色条件将当前用户的角色与值进行比较。 |
| `Url` | url 条件将当前 url 与值进行比较。 |
| `Culture` | culture 条件将当前 ui culture 与值进行比较。 |
| `Content Type` | content type 条件将当前显示的内容类型与值进行比较。 |
| `Javascript` | 用 javascript 编写的脚本条件。 |
| `All` | all 条件组包含其他条件，这些条件都必须为 true。 |
| `Any` | any 条件组包含其他条件，但只需要任何条件为 true。 |
| `Boolean` | 评估为 `true` 或 `false` 的布尔条件。 |

例如：`Always` 图层具有设置为 `true` 的 `Boolean Condition`，因此此图层上的小部件将始终显示。

有关创建自定义条件的更多信息，请参见 [Rules](../Rules/README.md)。

有关可用 javascript 方法的更多信息，请参见 [Scripting](../Scripting/README.md#layers-orchardcorelayers)。

## 区域

列出的区域可以在管理页面的 `设计 > 设置 > 区域` 中设置。

您必须在主题中将相应的区域声明为部分：

=== "Liquid"

    ``` liquid
    {% render_section "Header", required: false %}
    ```

=== "Razor"

    ``` html
    @await RenderSectionAsync("Header", required: false)
    ```

> 该文档由ChatGPT 4 翻译
