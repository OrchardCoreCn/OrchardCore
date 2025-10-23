# Orchard Core 中文文档

文档中心： [简体中文](https://docs.orchardcore.net/zh_CN/dev/) | [English](https://docs.orchardcore.net/en/latest/)

## 注意：本文档不再更新，为了防止与主干分支脱节，请异步 [官方英文文档](https://docs.orchardcore.net/en/latest/)

## 推荐翻译工具： [沉浸式翻译](https://microsoftedge.microsoft.com/addons/detail/%E6%B2%89%E6%B5%B8%E5%BC%8F%E7%BF%BB%E8%AF%91-%E7%BD%91%E9%A1%B5%E7%BF%BB%E8%AF%91%E6%8F%92%E4%BB%B6-pdf%E7%BF%BB%E8%AF%91-/amkbmndfnliijdhojkpoglbnaaahippg)

Orchard Core 是基于 [Orchard CMS](https://github.com/OrchardCMS/Orchard) 使用 [ASP.NET Core](https://docs.microsoft.com/aspnet/core/) 重新构建的。

Orchard Core 由两个不同的目标组成:

- **Orchard Core Framework**: 一个应用程序框架，用于构建**模块化**、**多租户** 的ASP.NET Core应用程序。
- **Orchard Core CMS**: 一个建立在Orchard Core Framework之上的网络内容管理系统（CMS)。

需要注意框架和 CMS 之间的差异非常重要。一些想要开发 SaaS 应用程序的开发人员只会对模块化框架感兴趣。其他想要构建可管理网站的用户将专注于 CMS 并构建模块来增强其网站或整个生态系统。

QQ 群：877196442

[![Orchard Core CN 中文官方](https://pub.idqqimg.com/wpa/images/group.png)](//shang.qq.com/wpa/qunwpa?idkey=48721591a71ee7586316604a7a4ee99d26fd977c6120370a06585085a5936f62)
[![Join the chat at https://gitter.im/OrchardCMS/OrchardCore](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/OrchardCMS/OrchardCore?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![BSD-3-Clause License](https://img.shields.io/badge/license-BSD--3--Clause-blue.svg)](LICENSE)
[![Documentation](https://readthedocs.org/projects/orchardcore/badge/)](https://docs.orchardcore.net/)
[![Crowdin](https://badges.crowdin.net/orchard-core/localized.svg)](https://crowdin.com/project/orchard-core)

## Local communities

中文资源

[![Orchard Core CN 中文讨论组](https://docs.orchardcore.net/en/latest/docs/assets/images/orchard-core-cn-community-logo.png)](https://shang.qq.com/wpa/qunwpa?idkey=48721591a71ee7586316604a7a4ee99d26fd977c6120370a06585085a5936f62)

## Build Status

Stable (release/1.5): 

[![Build status](https://github.com/OrchardCMS/OrchardCore/actions/workflows/release_ci.yml/badge.svg)](https://github.com/OrchardCMS/OrchardCore/actions?query=workflow%3A%22Release+-+CI%22)
[![NuGet](https://img.shields.io/nuget/v/OrchardCore.Application.Cms.Targets.svg)](https://www.nuget.org/packages/OrchardCore.Application.Cms.Targets)

Nightly (main): 

[![Build status](https://github.com/OrchardCMS/OrchardCore/actions/workflows/preview_ci.yml/badge.svg)](https://github.com/OrchardCMS/OrchardCore/actions?query=workflow%3A%22Preview+-+CI%22)
[![Cloudsmith](https://api-prd.cloudsmith.io/badges/version/orchardcore/preview/nuget/OrchardCore.Application.Cms.Targets/latest/x/?render=true&badge_token=gAAAAABey9hKFD_C-ZIpLvayS3HDsIjIorQluDs53KjIdlxoDz6Ntt1TzvMNJp7a_UWvQbsfN5nS7_0IbxCyqHZsjhmZP6cBkKforo-NqwrH5-E6QCrJ3D8%3D)](https://cloudsmith.io/~orchardcore/repos/preview/packages/detail/nuget/OrchardCore.Application.Cms.Targets/latest/)

## 现状

### 1.5.0

The software is finished -- and by finished, we mean there are no show-stopping, little-children-killing bugs in it. That we know of. There are probably numerous lower-priority bugs triaged into the next point release or service pack, as well.

这是一个更详细的 [路线图](https://github.com/OrchardCMS/OrchardCore/wiki/Roadmap).

### 文档

- Clone the repository using the command `git clone https://github.com/OrchardCMS/OrchardCore.git` and checkout the `main` branch.

官方手册：[简体中文](https://docs.orchardcore.net/zh_CN/dev/) | [English](https://docs.orchardcore.net/en/dev/)

- Install the latest version of the .NET SDK from this page <https://dotnet.microsoft.com/download>
- Next, navigate to `./OrchardCore/src/OrchardCore.Cms.Web`.
- Call `dotnet run`.
- Then open the `http://localhost:5000` URL in your browser.

看这里 [编码规范](./CODE-OF-CONDUCT.md)

- Download Visual Studio 2022 (v17.5+) from https://www.visualstudio.com/downloads/
- Open `OrchardCore.sln` and wait for Visual Studio to restore all Nuget packages.
- Ensure `OrchardCore.Cms.Web` is the startup project and run it.

### Docker

- Run `docker run --name orchardcms -p 8080:80 orchardproject/orchardcore-cms-linux:latest`

Docker images and parameters can be found at <https://hub.docker.com/u/orchardproject/>  
See [Docker documentation](https://docs.docker.com/engine/reference/commandline/run/#publish-or-expose-port--p---expose) to expose different port.

### Documentation

The documentation can be accessed here: <https://docs.orchardcore.net/>

## Code of Conduct

See [CODE-OF-CONDUCT](./CODE-OF-CONDUCT.md)

## .NET Foundation

This project is supported by the [.NET Foundation](http://www.dotnetfoundation.org).
