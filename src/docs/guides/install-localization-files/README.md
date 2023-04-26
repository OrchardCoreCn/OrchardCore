# 安装本地化文件

在这个指南中，您将下载和安装由社区管理的本地化文件，以本地化Orchard Core CMS的管理界面。

## 你需要什么

- 当前版本的.NET SDK。您可以从这里下载：<https://dotnet.microsoft.com/download>.
- 一个文本编辑器和一个终端，您可以在其中输入dotnet命令。
- 已经运行的Orchard Core CMS网站。如果您还没有完成，请按照指南[创建Orchard Core CMS网站](../create-cms-application/README.md)进行操作。

## 下载本地化文件

本地化文件由社区在[Crowdin](https://crowdin.com/project/orchard-core)网站上管理。任何人都可以提供自定义语言或为现有语言做贡献。

![image](assets/crowdin-languages.jpg)

对于这个指南，我们将下载法语语言。

- 单击__French__，将显示一个包含所有`.pot`文件列表的页面。
- 单击页面右上角的__下载或上传__按钮。
- 选择 __下载__，你的浏览器将下载一个名为 `fr.zip` 的压缩文件。

![image](assets/crowdin-download.jpg)

## 提取本地化文件

你下载的压缩文件需要在你的 Orchard Core CMS 网站的“本地化”文件夹中解压缩。

- 创建一个名为 `[your_site_root]/Localization` 的文件夹，其中 `your_site_root` 是你的网站位置。
- 将文件 `fr.zip` 解压缩到“本地化”文件夹中。

结果应该类似于这样：

![image](assets/localization-folder.jpg)

## 配置支持的区域设置

默认情况下，新的 Orchard Core CMS 网站只接受默认系统区域设置。这一步将配置它接受法语作为备用语言。

- 打开 Orchard Core CMS 的管理部分，通过在 <https://localhost:5001/admin> 中打开浏览器。
- 在__Configuration__ > __Settings__ > __Cultures__部分中，选择 `fr | French` 然后点击__Add culture__。
- 点击__Save__，网站会重新加载。

## 启用本地化并测试网站

为了使Orchard Core CMS使用这些新文件，我们需要启用本地化功能。

- 在__Modules__页面搜索__Localization__并点击 __Enable__。如果该功能已经启用，则无需进行任何操作。
- 在当前的URL中，添加`?culture=fr`，链接应如下所示：<https://localhost:44300/blog/OrchardCore.Features/Admin/Features?culture=fr>。

此时大多数文本应该显示为法语。  
从现在开始，如果浏览器将法语配置为其默认的区域设置，则管理界面将使用我们下载的法语文本翻译显示。  
`?culture=fr`只是一种模拟当前请求应使用法语作为UI文化的方式之一。

![image](assets/localized-french.jpg)

## 总结

您刚刚在Orchard Core CMS中下载并启用了新的本地化。


> 该文档由Chat-GPT 翻译
