# 贡献Orchard Core文档

首先，感谢您考虑为文档做出贡献！特别是在您刚开始使用Orchard时，您的经验和发现可以帮助其他新手很多。

[Orchard Core文档网站](https://docs.orchardcore.net/)使用[MkDocs](https://www.mkdocs.org/)构建，并从[Read the docs](https://readthedocs.org/projects/orchardcore/)提供服务。

## 编辑文档页面

在每个文档页面上，包括此页面在内，您会看到右上角有一个编辑图标。如果您单击它，就可以在GitHub中立即进行快速编辑。

或者，您可以克隆整个[Orchard Core代码库](https://github.com/OrchardCMS/OrchardCore)并在那里编辑任何文档文件。您可以在`src/docs`文件夹下找到它们。如果您在Visual Studio或其他IDE中打开Orchard Core解决方案（根目录中的`OrchardCore.sln`），则可以在`solution`文件夹下的`docs`解决方案文件夹中浏览`OrchardCore.Docs`项目中的文件。如果您使用Markdown编辑器，如[Markdown Editor VS扩展](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.MarkdownEditor)，则可以以所见即所得的方式查看所有Markdown格式和嵌入式图像，并且链接也将正常工作。

要嵌入YouTube视频，请确保在生成嵌入代码时选中“启用增强隐私模式”。（这将创建引用`youtube-nocookie.com`的代码）。

## 为新扩展或主题添加文档

如果您正在为新开发的扩展添加文档，或者添加其他全新的主题（在这种情况下，将其适应于您要添加主题的部分），请按照以下步骤操作：

1. 在`reference/modules`下添加一个与模块项目的名称相同（不含`OrchardCore.`）的文件夹。例如，对于`OrchardCore.AuditTrail`，请使用`AuditTrail`。
2. 在其中添加一个`README.md`文件，遵循现有这些文件的格式，在那里添加扩展功能的概述、配置和嵌入演示视频。
## 在 `reference/modules/README.md` 中链接到 `README.md` 文件。
## 在存储库根目录的 `mkdocs.yml` 文件中链接到 `README.md` 文件。
## 如果模块包含内容部分，则还需从 `reference/modules/ContentParts/README.md` 链接到 `README.md` 文件。

## 在本地运行文档站点

使用 MkDocs，您也可以在本地获取完整的 docs.orchardcore.net 体验。如果您想贡献大量的文档，请在本地运行站点，以确保所写的内容看起来实际上像应该的样子。

1. 按照 [MkDocs 安装指南](https://www.mkdocs.org/#installation) 安装 Python。一旦安装了 Python，就不需要手动安装 MkDocs，我们会在下一步中完成安装。如果您使用的是 Windows，请务必根据注释将 Python 的 `Scripts` 文件夹添加到 `PATH` 中，否则将找不到任何 `mkdocs` 命令。您还可能需要将用户的 Scripts 文件夹（类似于 `C:\Users\<用户名>\AppData\Roaming\Python\Python39\Scripts`）添加到 `PATH` 中。
2. 在克隆存储库的根目录中打开命令行。
3. 运行 `pip3 install -r src/docs/requirements.txt` 安装依赖项。
4. 运行 `mkdocs serve` 启动站点。然后，您就可以在 http://127.0.0.1:8000 下浏览它。


> 该文档由Chat-GPT 翻译
