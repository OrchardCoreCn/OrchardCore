# Starter Recipes and Themes included with Orchard Core

可以通过两个不同的 NuGet 元包去使用 Orchard Core 。

- `OrchardCore.Application.Cms.Core.Targets`
- `OrchardCore.Application.Cms.Targets`

第一个包 `OrchardCore.Application.Cms.Core.Targets` 用于以下情况 

- 开发一个解耦的网站
- 开发无头网站
- 从头开始开发主题网站

`Core.Targets` 包包含设置 Orchard Core 安装所需的最低要求。 它包含 `TheAdmin` 主题和两个安装配方，但没有前端主题。

!!! 提示
    您可以在安裝後啟用任何未包括在安裝配方裡的功能，
    通过菜单 _Configuration -> Features_ 。

第二个包 `OrchardCore.Application.Cms.Targets` 包含以上所有内容以及

- 适用于主题的配方
- 多个 CMS 起始主题

Orchard Core 中的配方通过启用功能帮助您设置站点，
和/或为您的网站创建内容类型和内容。

Orchard Core 主题可以包含 Razor 或 Liquid 视图，默认情况下使用 Orchard Core Display Management 技術去渲染內容。

## OrchardCore.Application.Cms.Core.Targets

### Empty Recipe

The Empty recipe enables content management features, but does not set a current theme.
You can use this recipe when starting Orchard Core in Decoupled Mode,
or when building your own theme.

Alternatively you can start with another recipe,
and change the active theme after setup.

#### Empty Recipe Contents 

- Content management features
- Enables templating via Liquid
- Activates `TheAdmin` theme

### Headless Recipe

The Headless recipe is intended to get you started when using Orchard Core
as an API, and Content Management System, with Administrator access to the host.

#### Headless Recipe Contents

- Content management features
- Secure GraphQL API support
- OpenID authentication features
- Activates `TheAdmin` theme and set Admin as the home route

!!! tip
    You will want to review the default security configuration to be certain
    it suits your requirements.

## OrchardCore.Application.Cms.Targets

### TheBlogTheme and Blog Recipe

The Blog recipe sets up a range of content types, and widgets, the initial content,
and sets the current theme to the TheBlogTheme.

TheBlogTheme is based on the [Start Bootstrap Clean Blog Theme](https://startbootstrap.com/themes/clean-blog/)

#### Blog Recipe Contents

- Content management features
- Blog related Content Types, and Widgets
- A blog, and a first blog post, based on the `ListPart`
- Liquid templates, in the TheBlogTheme source code
- Bootstrap

### TheAgencyTheme and Agency Recipe

The Agency recipe sets up a range of content types, and widgets, the initial content,
and sets the current theme to TheAgencyTheme.

TheAgencyTheme is based on the [Start Bootstrap Agency Theme](https://startbootstrap.com/themes/agency/)

#### Agency Recipe Contents

- Content management features
- Agency related Content types, and widgets
- A LandingPage, based on the `BagPart`
- Liquid templates, in TheAgencyTheme source code, and Templates feature
- Bootstrap

### ComingSoon Recipe and TheComingSoonTheme

This recipe sets up a range of Content Types, and Widgets, and the initial content of TheComingSoonTheme.
It also includes Email, Recaptcha, Forms, Workflows and User Registration Forms.

TheComingSoon theme is based on the [Start Bootstrap Coming Soon Theme](https://startbootstrap.com/themes/coming-soon/)

#### ComingSoon Recipe Contents

- Content management features
- A Coming Soon landing page, using the the `FlowPart`, and form `Widgets`
- Liquid layout template, in TheComingSoon Source Code
- Liquid content templates stored in the database with the Templates features
- Bootstrap

### SaaS Recipe with TheTheme

The Saas recipe includes a Software as a Service multi tenancy configuration.

It configures the site to use TheTheme, and you are then able to create Tenants 
using any of the other recipes.

#### Saas Recipe Contents

- Multi-tenancy feature
- Razor home page and Layout with bootstrap and jQuery

## Creating your own recipe

You can create your own recipes for deployment of your Orchard Core websites.

See the [Recipes](../reference/modules/Recipes/README.md) document for more information.
