# 包含在Orchard Core中的起始配方和主题

Orchard Core可以通过两个不同的NuGet元包供使用。

- `OrchardCore.Application.Cms.Core.Targets`
- `OrchardCore.Application.Cms.Targets`

第一个包 `OrchardCore.Application.Cms.Core.Targets` 适用于以下情况：

- 开发解耦Web站点
- 开发Headless Web站点
- 从头开始开发主题Web站点

`Core.Targets` 包包含了设置Orchard Core安装所需的最少量。它包含 `TheAdmin` 主题和两个基于配方的安装方案，但没有前端主题。

!!! tip
    在选择设置配方后，可以通过 _Configuration -> Features_ 菜单启用未启用的任何功能。第二个包`OrchardCore.Application.Cms.Targets`包含了以上所有内容，还包括：

- 主题的设置配方（Setup recipes）
- 多个 CMS 初始主题（Multiple CMS Starter Themes）

Orchard Core 中的配方（Recipes）可以帮助您通过启用功能和/或为您的站点创建内容类型和内容来设置站点。

Orchard Core 主题可以包含 Razor 或 Liquid 视图，并且默认情况下使用 Orchard Core 显示管理技术来呈现内容。

## OrchardCore.Application.Cms.Core.Targets

### 空白配方（Blank Recipe）

空白配方启用内容管理功能，但不设置当前主题。您可以在启动 Orchard Core 的分离模式下使用此配方，或在构建自己的主题时使用它。或者，您可以选择其他的配置文件，然后在设置后更改活动主题。

#### 空白配置文件内容

- 内容管理功能
- 激活`TheAdmin`主题

### Headless配置文件

Headless配置文件旨在帮助您使用Orchard Core作为API和内容管理系统，并具有对主机的管理员访问权限。

#### Headless配置文件内容

- 内容管理功能
- 安全的GraphQL API支持
- OpenID身份验证功能
- 激活`TheAdmin`主题，并将管理员设置为主页路由!!! tip
    您将需要检查默认的安全配置以确保它符合您的要求。

## Headless Recipe Video

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/dbABI1wECPg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## OrchardCore.Application.Cms.Targets

### TheBlogTheme和Blog Recipe

Blog recipe设置了一系列内容类型、小部件、初始内容，并将当前主题设置为TheBlogTheme。

TheBlogTheme基于[Start Bootstrap Clean Blog Theme](https://startbootstrap.com/themes/clean-blog/)。

#### Blog Recipe的内容- 内容管理功能
- 与博客相关的内容类型和小部件
- 基于`ListPart`的博客和第一篇博客文章
- `TheBlogTheme`源代码中的Liquid模板
- Bootstrap

### 博客-Lucene查询配方

Lucene查询配方是`TheBlogTheme`中的一个可选配方。此配方运行博客Lucene搜索配方，并作为示例，将近期博客文章的SQL查询替换为Lucene查询。

#### 博客Lucene查询配方内容

- 运行博客Lucene搜索配方
- 用Lucene替换近期博客文章的SQL查询

### 博客-Lucene搜索配方Lucene Search食谱是TheBlogTheme中的可选食谱。该食谱启用了Lucene功能，并创建了搜索设置、Lucene索引和权限。

#### Blog Lucene Search食谱内容

- 启用Lucene功能
- 设置Lucene索引
- 创建搜索设置
- 搜索索引权限

### TheAgencyTheme和Agency食谱

Agency食谱设置了一系列内容类型和小部件、初始内容，并将当前主题设置为TheAgencyTheme。

TheAgencyTheme基于[Start Bootstrap Agency Theme](https://startbootstrap.com/themes/agency/)。

#### Agency食谱内容### 内容管理特性
该功能可用于管理网站的所有内容，包括页面、博客、图片、视频等。可以轻松地添加、编辑和删除内容，以确保网站的动态更新。 

### 与机构相关的内容类型和小部件
为了方便网站使用者，该功能设置了与机构相关的内容类型和小部件，包括机构信息、团队成员、客户案例和服务内容等，以更好地展示机构特色。

### 基于 `BagPart` 的登陆页面
该功能使用 `BagPart` 创建了一个登录页面，用户可以使用此页面登录网站，以便访问特定的网站内容。

### TheAgencyTheme源代码中的Liquid模板和模板特性
`Liquid` 是一种模板语言，可以用来创建动态数据的静态页面。在该功能中，通过修改 `TheAgencyTheme` 的源代码，添加了 `Liquid` 模板和模板特性。

### Bootstrap
这是一个流行的前端框架，包括HTML、CSS和JavaScript的库，用于快速开发响应式、移动设备优化的网站。在该功能中使用了 `Bootstrap` 来进行网站的前端开发。 

### ComingSoon Recipe 和 TheComingSoonTheme

该功能包含了许多内容类型和小部件，并提供了 `TheComingSoonTheme` 的初始内容。它还包括电子邮件、验证码、表单、工作流和用户注册表单等功能。

`TheComingSoonTheme` 基于 [Start Bootstrap Coming Soon Theme](https://startbootstrap.com/themes/coming-soon/) 开发。

#### ComingSoon Recipe 内容

- 内容管理功能
- 一个 "即将上线" 的页面，使用 `FlowPart` 和表单 `Widgets`
- 存储在数据库中的 `Liquid` 内容模板
- `TheComingSoon` 源代码中的 `Liquid` 布局模板- Bootstrap

### 使用TheTheme的SaaS配方

SaaS配方包括软件即服务多租户配置。

它配置网站使用TheTheme，然后您可以使用任何其他配方创建租户。

#### SaaS配方内容

- 多租户功能
- 使用Bootstrap和jQuery的Razor主页和布局

## 创建自己的配方

您可以为部署Orchard Core网站创建自己的配方。

请参阅[配方](../reference/modules/Recipes/README.md)文档获取更多信息。
$END_OF_FILE$