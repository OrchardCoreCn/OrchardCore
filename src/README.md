# Orchard Core 中文文档

由于目前中文文档翻译工作刚刚起步，欢迎小伙伴们踊跃报名参加文档翻译工作。
QQ 群：877196442

Orchard Core 是基于 [Orchard CMS](https://github.com/OrchardCMS/Orchard) 使用 [ASP.NET Core](https://docs.microsoft.com/aspnet/core/) 重新构建的。

Orchard Core 由两个不同的目标组成:

- **Orchard Core Framework**: 一个应用程序框架，用于构建**模块化**、**多租户** 的ASP.NET Core应用程序。
- **Orchard Core CMS**: 一个建立在Orchard Core Framework之上的网络内容管理系统（CMS)。

需要注意框架和 CMS 之间的差异非常重要。一些想要开发 SaaS 应用程序的开发人员只会对模块化框架感兴趣。其他想要构建可管理网站的用户将专注于 CMS 并构建模块来增强其网站或整个生态系统。

[![Join the chat at https://gitter.im/OrchardCMS/OrchardCore](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/OrchardCMS/OrchardCore?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![BSD-3-Clause License](https://img.shields.io/badge/license-BSD--3--Clause-blue.svg)](https://github.com/OrchardCMS/OrchardCore/blob/master/LICENSE)
[![Documentation](https://readthedocs.org/projects/orchardcore/badge/)](https://docs.orchardcore.net/)
[![Crowdin](https://badges.crowdin.net/orchard-core/localized.svg)](https://crowdin.com/project/orchard-core)

## 使用Orchard Core Framework 构建软件服务 （SaaS） 解决方案 

了解Orchard Core Framework是独立于nuget.org上的CMS分发的，这一点非常重要。我们在 <https://github.com/OrchardCMS/OrchardCore.Samples> 提供了一些示例程序，它将指导您如何仅使用Orchard Core Framework来构建**模块化** 和 **多租户** 的应用程序，而无需任何 CMS 特定功能。

我们的目标之一是启用托管应用程序的基于社区的生态系统，这些生态系统可以通过模块（如电子商务系统、博客引擎等）进行扩展。Orchard Core Framework支持模块化环境，允许不同的团队处理应用程序的不同部分，并使组件跨项目可重用。

## 使用Orchard Core CMS 构建网站 

Orchard Core CMS 是基于Orchard CMS 使用ASP.NET Core 重写的。它不仅仅是一个端口，因为我们希望大幅提高性能，并尽可能与的ASP.NET Core 的开发模型一致。

- **性能**. 这可能是开始使用Orchard Core CMS时最明显的变化。这对CMS来说是非常快的。速度如此之快，以至于我们甚至都没有关心输出缓存模块的工作。让你体验一下，没有缓存的Orchard Core CMS比以前的版本快了20倍。

- **跨平台**. 现在，您可以在 Windows、Linux 和 macOS 上开发和部署果园核心 CMS。我们也有 Docker 映像可供使用。

- **文档数据库** 抽象化. Orchard Core CMS仍然需要一个关系数据库，并且兼容SQL Server, MySQL, PostgreSQL和SQLite，但是它现在使用的是一个抽象化文档数据库(YesSql)，它提供了一个文档数据库API来存储和查询文档。对于CMS系统来说，这是一种更好的方法，可以显著提高性能。

- **NuGet 包**. 模块和主题现在作为NuGet包共享。用Orchard Core CMS创建一个新网站其实很简单，只需从NuGet gallery引用一个元包。它还意味着更新到一个新的版本只涉及更新这个包的版本号。

- **实时预览**. 在编辑内容项时，甚至在保存内容之前，就可以实时看到它在站点上的样子。它也适用于模板，您可以浏览任何页面，在您键入更改时检查更改对模板的影响。

- **Liquid 模板支持**. 编辑者可以使用Liquid模板语言安全地更改HTML模板。 选择它的原因在于它有据可查（Jekyll，Shopify等），又非常安全。

- **自定义查询**. 我们希望为开发人员提供一种尽可能简单地访问他们所有数据的方式。我们创建了一个模块，允许您创建自定义临时 SQL 和 Lucene 查询，这些查询可用于重新显示自定义内容或公开为 API 终结点。您可以使用它创建高效的查询，或将数据公开给 SPA 应用程序。

- **部署计划**. 部署计划是可以包含构建网站的内容和元数据的脚本。您现在可以包含二进制文件，甚至可以使用它们来远程部署站点，例如从准备环境部署到生产环境。它们也可以是NuGet软件包的一部分，允许你发送预定义的网站。

- **可伸缩性**. 由于 Orchard Core 是一个多租户系统，因此您可以通过单个部署托管尽可能多的网站。然后，典型的云计算机可以并行承载数千个站点，包括数据库、内容、主题和用户隔离。

- **工作流**. 创建内容审批工作流、响应 Webhook、在提交表单时执行操作，以及要使用用户友好的 UI 实现的任何其他流程。

- **GraphQL**.我们提供非常灵活的 GraphQL API，以便任何授权的外部应用程序都可以重用您的内容，如 SPA 应用程序或静态站点生成器。

### 不同的网站构建策略

Orchard Core CMS支持所有主要的网站建设策略：

- **完整 CMS**. 在此模式下，网站使用主题和模板来呈现您的内容，旨在实现很少或完全没有自定义开发。

- **解耦 CMS(Decoupled CMS)**. 除了内容管理端之外，网站从空白开始。您可以使用 Razor Pages 或 MVC 操作创建所需的所有模板，并通过内容服务访问您的内容。参考:[B站](https://www.bilibili.com/video/BV1nE411M7FV?from=search&seid=808810272737390403)  ，[油管](https://www.youtube.com/watch?v=yWpz8p-oaKg)

- **无头 CMS(Headless CMS)**. 该网站只管理内容，您创建一个单独的应用程序，该应用程序将使用 GraphQL 或 REST API 获取托管内容。参考： [B站](https://www.bilibili.com/video/BV15E411s7kz?from=search&seid=16124424122784013302)，[油管](https://www.youtube.com/watch?v=4o9zG17cfa0)

## 项目状态

Orchard Core的最新发行版本是 `1.0.0-rc2`。
发行说明可以在这里找到： <https://github.com/OrchardCMS/OrchardCore/releases/tag/1.0.0-rc2>

该软件几乎已准备好最终发布。 不进行功能开发或软件增强； 在此阶段仅允许严格限定范围的错误修复，除非出现重大的错误。

这是一个更详细的[路线图](https://github.com/OrchardCMS/OrchardCore/wiki/Roadmap).

## 从这里开始

- 使用命令 `git clone https://github.com/OrchardCMS/OrchardCore.git` 克隆此仓库，并切换到 `dev` 分支。

- 观看Orchard Core演示的ASP.NET社区站立视频: <https://www.youtube.com/watch?v=HeDjv3blBjQ&t=2246s&list=PL1rZQsJPBU2StolNg0aqvQswETPcYnNKL&index=24>

- 参考这个示例 <https://github.com/OrchardCMS/OrchardCore.Samples> 它将引导你如何构建一个 **模块化** 和 **多租户** 的应用程序。

- 按照这个教程 [Training Demo Module](https://github.com/Lombiq/Orchard-Training-Demo-Module) 你将学习如何开发Orchard Core模块。（这里是包含中文翻译的版本：[码云仓库](https://gitee.com/hyzx86/Orchard-Training-Demo-Module/)）

### 命令行

- 从这个页面安装最新版的 .NET Core SDK  <https://www.microsoft.com/net/download/core>
- 接下来,导航到 `D:\OrchardCore\src\OrchardCore.Cms.Web` 或你的文件夹，在管理员模式下命令行。
- 执行 `dotnet run`
- 然后在浏览器中打开 `http://localhost:5000` 

你也可以参考 [代码生成模板文档](docs/getting-started/templates/README.md) 从预定义的模板创建新的应用程序。

### Visual Studio

- 从 https://www.visualstudio.com/downloads/ 下载 Visual Studio 2019 (任何版本) 
- 打开解决方案： `OrchardCore.sln`，然后等待恢复所有包
- 确保启动项目为： `OrchardCore.Cms.Web` ，然后运行
- 可选安装 [Lombiq Orchard Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=LombiqVisualStudioExtension.LombiqOrchardVisualStudioExtension) 它可以帮助您向您的Visual Studio添加一些有用的实用程序，例如错误日志监视程序或依赖项注入器。

### Docker

- 运行 `docker run --name orchardcms orchardproject/orchardcore-cms-linux:latest`

Docker 镜像和参数可以到这里查看 <https://hub.docker.com/u/orchardproject/>
