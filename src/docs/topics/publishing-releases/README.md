# 发布新的Orchard Core版本

这些笔记主要是为Orchard的核心贡献者编写的，以指导如何准备新的版本。

## 版本控制

我们遵循[语义化版本控制2.0.0](https://semver.org/)。

## 发布清单

您可以将以下清单复制到GitHub的问题中，并随着进展逐一勾选。 
虽然清单是按推荐顺序排列的，但不是每个步骤严格依赖于前面的步骤。 
“<版本名称>”应替换为当前版本，例如“1.0.0”或“rc2”。

```markdown
### 准备项目

在[主要存储库](https://github.com/OrchardCMS/OrchardCore)上进行GitHub的一些整理。

- [ ] 关闭版本中的所有问题（包括合并相应的pull请求，如果合适）或将它们分配到下一个版本。
- [ ] 将关闭的问题（包括通配符版本号如“1.0.x”）分配到这个版本（里程碑）下。

### 准备代码

更新源代码使其看起来和新版本一样。

- [ ] 从`main`分支创建一个 `release/<version name>` 分支，例如 `release/1.3.1`。
- [ ] 更新 `OrchardCore.Commons.props` 文件，使用 `<VersionSuffix> </VersionSuffix>`使预览构建号不会注入到程序包中。验证 `VersionPrefix` 标签是否与发布的版本匹配。
- [ ] 更新 `src/OrchardCore/OrchardCore.Abstractions/Modules/Manifest/ManifestConstants.cs` 中的模块版本。
- [ ] 在所有文档文件中更新命令行中的版本号。

### 测试发布

确保一切都正常。

- [ ] 确保[OrchardCore.Samples可以正常运行](https://github.com/OrchardCMS/OrchardCore.Samples)。
- [ ] 使用Cloudsmith feed中的NuGet包测试`release/`下的分支（分支下的内容将自动发布）。至少测试以下指南：
    - [创建一个模块化的ASP.NET Core应用程序](https://docs.orchardcore.net/en/latest/docs/guides/create-modular-application-mvc/)
    - [创建一个Orchard Core CMS网站](https://docs.orchardcore.net/en/latest/docs/guides/create-cms-application/)
    - [创建一个新的解耦CMS网站](https://docs.orchardcore.net/en/latest/docs/guides/decoupled-cms/)
### 准备和发布Orchard Core翻译

在 [Translations project](https://github.com/OrchardCMS/OrchardCore.Translations)中更新所有内容。一定要等到所有代码更改完成后才执行此操作，因为本地化字符串可能会一直变化。

- [ ] 使用[PoExtractor](https://github.com/lukaskabrt/PoExtractor)更新.po文件，这也会更新[Crowdin](https://crowdin.com/project/orchard-core)。
- [ ] 在NuGet上发布新版本。
- [ ] 更新主存储库中的\_src/OrchardCore.Build/Dependencies.props\_文件中的“OrchardCore.Translations.All”包引用，以引用新的NuGet包。

### 准备文档

更新文档，以包含有关新版本的信息，因此一旦发布该版本，您只需要指向新的信息即可。

- [ ] 创建一个具有“vx.y.z”标签的新的__Draft__ 发布，该标签在发布时创建。自动生成发布说明。
- [ ] 在特定的文档部分中创建发行说明。您可以将前一个发行说明作为模板。
    - 发布的亮点和目标概述。您希望人们记住这个版本的什么？
    - 先决条件。您需要哪个框架版本，还需要使用Orchard的其他内容吗？
    - 升级步骤，在以前版本中是否需要进行任何迁移，是否有不兼容变化。

### 发布版本
在发布版本之前先完成公开发布的难点部分。这应该在完成以上所有步骤之后进行。

- [ ] 将`release/<版本名>`合并到`main`。
     - 合并到`main`需要两个批准，因此您需要创建一个拉取请求。
     - 将其作为合并提交（merge commit）合并，而不是压缩合并（squash merge）。
- [ ] 发布草稿版本。
- [ ] 使用现在自动发布到 NuGet 的包测试指南（guides）。请测试至少以下指南：
     - [创建模块化 ASP.NET Core 应用程序](https://docs.orchardcore.net/en/latest/docs/guides/create-modular-application-mvc/)
     - [创建 Orchard Core CMS 网站](https://docs.orchardcore.net/en/latest/docs/guides/create-cms-application/)
     - [创建一个新的解耦 CMS 网站](https://docs.orchardcore.net/en/latest/docs/guides/decoupled-cms/)
- [ ] 更新 [Try Orchard Core](https://github.com/OrchardCMS/TryOrchardCore)。

### 公开发布版本

让全世界都知道我们闪亮的新版本。享受这一部分！执行以下步骤将发布版本公开，因此仅在准备好所有其他内容后执行这些步骤。

- [ ] 在所有引用最新版本的地方更新文档，例如在 CLI 模板、命令、[创建一个新的解耦 CMS 网站](https://docs.orchardcore.net/en/latest/docs/guides/decoupled-cms/)指南中（进行一个包版本字符串搜索即可），以及根 README 中的 [Status](https://docs.orchardcore.net/en/latest/#status)。
- [ ] 在 GitHub 上更新标记的[发布版本](https://github.com/OrchardCMS/OrchardCore/releases)：将其标题更改为更详细的内容（例如“Orchard Core 1.0.0 RC 2”），在其描述中添加一个指向文档中发布说明的链接（类似于`有关此版本的详细信息，请参见[文档中的发布说明](链接到此)`)。在根 README 的 [Status](https://docs.orchardcore.net/en/latest/#status) 下添加此版本的链接。
- [ ] 在网站上发布一篇博客文章。


### 发行前

- [ ] 请求在 [DevBlogs](https://devblogs.microsoft.com/) 上发布博客文章。
- [ ] 请求在 [.NET Foundation 新闻](https://dotnetfoundation.org/news) 上发布博客文章。
- [ ] 推文

### 发行后

- [ ] 创建下一个版本号的新里程碑。
- [ ] 更新 `OrchardCore.Commons.props` 文件的下一个版本号，并使用 `<VersionSuffix>preview</VersionSuffix>` 以便预览版使用新版本。
```


> 该文档由Chat-GPT 翻译
