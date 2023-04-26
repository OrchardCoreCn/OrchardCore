# 网站地图 (`OrchardCore.Sitemaps`)

网站地图模块提供了自动生成网站地图的功能。

它支持创建符合标准网站地图协议的XML网站地图文件和XML网站地图索引文件。

有关网站地图的更多信息，请参见[sitemaps.org](https://www.sitemaps.org/)

## 常规概念

通过创建网站地图并添加网站地图源来配置网站地图。

通过创建网站地图索引并选择包含在索引中的网站地图来配置网站地图索引。

## 如何创建网站地图

- 确保启用了网站地图功能。

- 转到 _Configuration -> SEO -> Sitemaps_

- 创建一个新的网站地图。

- 设置网站地图的路径，注意路径必须以`.xml`结尾

- 给它一个名称。

- 选择编辑。

- 向其中添加网站地图源。

- 现在可以浏览网站地图，并在指定的路径上提供服务。

## 网站地图内容类型源

内容类型源将为您的内容项提供网站地图，每个内容类型为一组。

您可以选择索引所有内容类型，或指定内容类型。

您还可以选择默认优先级和更改频率，无论是所有内容类型还是单个内容类型。

您还可以选择限制项目。

限制项目选项通常与网站地图索引结合使用，以限制网站地图的大小，并使维护网站地图更加容易。

Google和Bing将网站地图的大小限制为50,000个项目或10MB，以先达到为准。

如果需要限制网站地图中的内容项数量

- 取消选中索引所有内容类型。

- 选中限制项目。

- 选择要索引的内容类型。

- 选择跳过`x`个内容项并获取`x`个内容项。

对于剩余的内容项，请创建另一个网站地图，并根据需要重复选择不同的跳过和获取值。

对于其他内容类型，请创建另一个网站地图，并将所有这些网站地图包含在网站地图索引中。

!!! 注意
    仅列出了具有`AutoroutePart`附加的内容类型以包含在网站地图中。
    要包括没有`Autoroute`路由的内容项，请实现`IRouteableContentTypeProvider`

## 网站地图部分

将SitemapPart添加到内容类型中，以在内容项级别提供网站地图配置。

此处的设置可以覆盖任何网站地图配置。

- 选中以覆盖网站地图配置。

- 排除内容项。

- 更改优先级。

- 更改更改频率。

!!! 注意
    您不必将SitemapPart添加到内容类型中，以使其成为网站地图的一部分。

## 本地化网站地图

要支持Google hreflang网站地图扩展，请启用本地化内容项网站地图功能。

这将自动在您的网站地图中包含任何本地化的内容项。

有关此协议的更多信息，请参见[Google Sitemap Extensions](https://support.google.com/webmasters/answer/189077)。

## 解耦的Razor页面

要包括使用Razor页面显示的内容类型，请启用解耦的Razor页面网站地图功能。

在您的`Program.cs`中，配置`SitemapsRazorPagesOptions`以支持您的内容类型的路由。

```csharp
builder.Services.Configure<SitemapsRazorPagesOptions>(options =>
{
    options.ConfigureContentType("DecoupledBlogPost", o =>
    {
        o.PageName = "DecoupledBlogPost";
        o.RouteValues = (contentItem) => new { area = "OrchardCore.Sitemaps", slug = contentItem.ContentItemId };
    });
});    
```

!!! 注意
    确保在路由值中包含区域。

## 网站地图缓存

网站地图在`wwwroot/sm-cache`文件夹中以租户为基础进行缓存。

当发布内容项时，缓存会自动清除。

要手动清除缓存，请使用_Configuration -> SEO -> Sitemaps Cache_功能。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/fG_rFD0wffw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## CREDITS

### IDeliverable.Seo

<https://github.com/IDeliverable/IDeliverable.Seo>  

版权所有 (c) IDeliverable, Ltd. 

BSD-3
