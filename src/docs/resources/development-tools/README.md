# 开发工具

我们推荐哪些工具来构建你的Orchard Core应用程序，或者在Orchard上开发工作？最终，选择什么工具完全取决于你的个人偏好，因为只要你能够编辑源文件并构建应用程序，你就可以在任何平台上使用.NET Core支持的任何工具。以下是一些有用的工具，帮助你进行一般的编辑体验。

## 编辑器和IDE

- Visual Studio：.NET开发人员在Windows上的首选IDE，功能丰富，还有一个免费版本。从<https://www.visualstudio.com/downloads/>下载最新的Visual Studio（任何版本）。
  - 可以选择安装[Lombiq Orchard Visual Studio扩展程序](https://marketplace.visualstudio.com/items?itemName=LombiqVisualStudioExtension.LombiqOrchardVisualStudioExtension)以在Visual Studio中添加一些有用的实用程序，例如错误日志监视器或依赖注入器。
  - 可以选择安装[Orchard Dojo Library中的代码片段](https://orcharddojo.net/orchard-resources/CoreLibrary/Utilities/VisualStudioSnippets/)，以快速生成在模块和主题开发过程中的某些常见场景的代码。
  - 在[Orchard Dojo Library](https://orcharddojo.net/orchard-resources/CoreLibrary/DevelopmentGuidelines/DevelopmentEnvironment)中有一些进一步推荐的扩展和其他使用Visual Studio的技巧。
- Visual Studio Code：免费跨平台编辑器，可以从<https://code.visualstudio.com/>获取。
- JetBrains Rider：功能丰富的跨平台IDE，在30天的免费试用期内，你可以从<https://www.jetbrains.com/rider/download/>获取。

## 工具

- [DB Browser for SQLite](https://sqlitebrowser.org/)是一个免费且开源的工具，可以浏览Orchard创建的SQLite数据库文件。你可以使用它打开`App_Data/Sites`下租户文件夹中的`yessql.db`文件，浏览表格，运行查询，并以漂亮格式显示JSON文档。
- [smtp4dev](https://github.com/rnwood/smtp4dev)是一个可以在本地运行的小型SMTP服务器，可以用于测试发送电子邮件。只需通过`dotnet`安装它，并将Orchard配置为使用它作为SMTP服务器。它甚至具有一个Web界面，你可以在其中浏览处理过的电子邮件。


> 该文档由Chat-GPT 翻译
