# 管理员 (`OrchardCore.Admin`)

管理员模块为您的网站提供了一个管理仪表板。

## 自定义管理员前缀

如果您想在URL中指定另一个前缀以访问管理员部分，则可以通过在appsettings.json中使用此选项来更改它：

``` json
  "OrchardCore": {
    "OrchardCore_Admin": {
      "AdminUrlPrefix": "YourCustomAdminUrl"
      }
    }
```
## 自定义管理员品牌

默认情况下，OrchardCore的标志和站点名称显示在顶部导航栏中。

您可以通过覆盖“AdminBranding”形状来更改它，无论是从[自定义管理员主题](../../../guides/create-admin-theme/README.md)还是使用管理员模板功能。
您还可以使用此形状来定义管理员favicon。

以下是使用媒体模块的徽标和favicon的示例。

=== "Liquid"

    ``` liquid
    {% assign favicon_url = 'favicon.ico' | asset_url %}
    {% link rel:'shortcut icon', type:'image/x-icon', src:favicon_url %}
    {% a area: 'OrchardCore.Admin', controller: 'Admin' , action: 'Index', class: 'ta-navbar-brand' %}
        <img src="{{ 'logo.png' | asset_url }}" alt="{{ Site.SiteName }}" />
        <span>{{ Site.SiteName }}</span>
    {% enda %}
    ```

=== "Razor"

    ``` html
    <link asp-src="~/media/favicon.ico" type="image/x-icon" rel="shortcut icon" />

    <a class="ta-navbar-brand" asp-route-area="OrchardCore.Admin" asp-controller="Admin" asp-action="Index">
        <img src=@Url.Content("~/media/logo.png") alt="@Site.SiteName" />
        <span>@Site.SiteName</span>
    </a>
    ```
> 该文档由ChatGPT 4 翻译
