# 集成 Facebook 社交插件

要集成 Facebook 的社交插件，必须启用和配置 `OrchardCore.Facebook.Widgets` 模块。您可以在 [此处](../../reference/modules/Facebook/README.md) 了解更多信息。

## 您将构建什么

您将构建一个博客，并让您的读者能够使用 [引用插件](https://developers.facebook.com/docs/plugins/quote) 分享引用。

## 您需要什么

按照指南来 [创建一个新的 Orchard Core CMS 网站](../../guides/create-cms-application/README.md)

## 登录管理面板并启用 Facebook 社交插件小部件功能

导航至 <https://localhost:5001/admin> 并启用 Facebook 社交插件小部件功能。

![image](assets/enable-facebook-widgets.jpg)

在 <https://developers.facebook.com/apps/> 上创建一个新的应用，并将应用程序 ID 和应用程序密钥复制到 <https://localhost:5001/Admin/Settings/OrchardCore.Facebook>。
在页面底部添加Facebook引用小部件。在Orchard Core的管理界面“层”（Layers）中添加“Facebook引用”部件，并发布。然后在博客文章页面上，通过选择文章的标题，即可将其作为Facebook引用内容分享。
你刚刚将“Facebook 引用”社交插件集成到你的博客！


> 该文档由Chat-GPT 翻译
