# Flows (`OrchardCore.Flows`)

Flows模块提供了在另一个内容项中直接显示内容项的方法。这是通过Flow Part和Bag Part实现的。

一个很好的例子是一个带有FAQ部分的页面。FAQ内容类型可能有一个问题和一个答案字段，内容编辑器可以在编辑页面时直接添加新的FAQ。

## 空的Flows和Bags

不包含任何内容项的Flows和Bags将显示不同的形状名称。对于空的Flows，形状名称为`FlowPart_Empty`；对于空的Bags，它是`BagPart_Empty`。

这允许您有条件地显示或隐藏空的Flows或Bags。例如，要隐藏没有项目的Flow部分，您可以将以下内容添加到放置文件中：

```json
  "FlowPart_Empty": [
    {
      "place": "-"
    }
  ]
```

如果您想要使用相同的模板来处理具有项目和没有项目的Flow部分，您可以添加以下内容：

```json
  "FlowPart_Empty": [
    {
      "shape": "FlowPart"
    }
  ]
```

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/ufEhMXYZPy4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
