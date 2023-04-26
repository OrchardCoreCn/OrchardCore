# 别名 (`OrchardCore.Alias`)

该模块允许您为内容项指定友好的标识符。别名也可以导入和导出，这意味着在运行配方或部署内容时它们是持久化的（而内容项ID不是）。

## 别名部件

将此部件附加到内容类型以指定内容项的别名。

## Liquid

启用别名后，您可以在Liquid视图和模板中通过其别名句柄检索内容：

```liquid
{% assign my_content = Content["alias:footer-widget"] %}
```

或

```liquid
{% assign my_content = Content.Alias["footer-widget"] %}
```
> 该文档由ChatGPT 4 翻译
