# Placements (`OrchardCore.Placements`)

Placements模块允许编辑器添加自定义放置逻辑。

## 一般概念

主题和模块可以使用`placement.json`文件提供自定义放置。

放置模块提供了一种从管理站点定义放置的方法。

### 放置优先级

放置模块中定义的放置会覆盖主题和模块中定义的放置。

### 放置存储

使用此模块定义的放置默认存储在数据库中。

您还可以选择通过启用“放置文件存储”(`OrchardCore.Placements.FileStorage`)功能将放置存储在文件中。

### 形状放置格式

放置由形状名称定义。
对于每个形状，您可以定义一组放置规则。
放置规则是一个JSON数组，类似于`placement.json`文件条目中定义的内容，如[放置文档](../../core/Placement/README.md#format)中所定义的内容。
> 该文档由ChatGPT 4 翻译
