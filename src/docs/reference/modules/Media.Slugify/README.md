# 媒体Slugify (`OrchardCore.Media.Slugify`)

媒体Slugify功能使媒体库中的资产变得更加友好。

## 目的

默认情况下，媒体库不限制文件夹和文件的命名。例如，在文件夹“Images＆docs”中拥有文件“ The team（2020）.jpg”是允许的。此资产的URL将是`/media/Images%20&%20docs/The%20team%20(2020).jpg`。

这显然不是一个非常友好的URL。

通过启用Media Slugify功能，可以自动将文件夹和文件的名称转换为slug。这将把上面的示例重命名为`the-team-2020.jpg`，并将其放在文件夹`images-docs`中。URL将是`/media/images-docs/the-team-2020.jpg`。

不同的文件可以具有相同的slug，这使得无法上传两个文件而不重命名其中一个（例如`The team（2020）.jpg`和`The Team 2020.jpg`）。

!!! 注意
    启用Media Slugify功能不会重命名现有的文件夹和文件，只有新文件夹和文件才会被slugify。

> 该文档由ChatGPT 4 翻译
