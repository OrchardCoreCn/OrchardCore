# 本地化 (`OrchardCore.Localization`)

该模块提供了支持PO（Portable Object）本地化文件格式所需的基础设施。它还支持复数形式。

## 在线翻译

[![Crowdin](https://badges.crowdin.net/orchard-core/localized.svg)](https://crowdin.com/project/orchard-core)

不同文化的本地化文件可在[Crowdin](https://crowdin.com/project/orchard-core)上找到。

## PO文件位置

PO文件位于以下位置：

- 对于每个模块和主题，所有与`[ModuleLocation]/Localization/[CultureName].po`匹配的文件
- 所有与`/Localization/[CultureName].po`匹配的文件
- 对于每个租户，所有与`/App_Data/Sites/[TenantName]/Localization/[CultureName].po`匹配的文件
- 对于每个模块和主题，所有与以下匹配的文件
  - `/Localization/[ModuleId]/[CultureName].po`
  - `/Localization/[ModuleId]-[CultureName].po`
  - `/Localization/[CultureName]/[ModuleId].po`

`[CultureName]`可以是文化中性部分，例如`fr`，也可以是完整的部分，例如`fr-CA`。

如果您使用docker，则建议将本地化文件放在`/Localization/`文件夹中。特别是如果在`/App_Data/`上挂载卷，则会隐藏预先存在的文件。

!!! 注意
    如果您编辑了`.po`文件，则需要重新启动应用程序才能使更改生效。

### 发布本地化文件

PO文件需要包含在发布输出目录中。 
将以下配置添加到您的`[Web Project].csproj`文件中，以将它们包含为内容。

```xml
  <ItemGroup>
    <Content Include="Localization\**" >
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </Content>
  </ItemGroup>
```

!!! 注意
    翻译文件可以由模块提供，在这种情况下，它们会自动嵌入到模块程序集中，除非Visual Studio在项目文件中添加了一些错误的项组。

## 配方步骤

可以使用设置步骤在配方中添加文化。以下是一个示例步骤：

```json
{
  "name": "settings",
  "LocalizationSettings": {
    "DefaultCulture":  "fr",
    "SupportedCultures": [ "fr", "en" ]
  }
},
```

### 示例

- `/Localization/fr.po`
- `/Localization/fr-CA.po`
- `/Localization/es-MX.po`

## 文件格式

本文解释了如何组织PO文件，包括复数形式。

<https://www.gnu.org/software/gettext/manual/html_node/PO-Files.html>

## 翻译上下文

为了防止不同PO文件中的条目互相覆盖，它们为每个翻译字符串定义了上下文。  
例如，两个视图可以使用名为`Hello`的字符串，但它们可能具有不同的翻译。然后需要提供两个条目，并指定每个翻译关联的上下文。在这种情况下，每个视图名称都是上下文。

### 来自视图

上下文字符串必须与视图位置匹配，直到模块文件夹。

#### 视图

假设视图的路径为`TheAdmin\Views\Layout.cshtml`。

#### PO文件

```
msgctxt "TheAdmin.Views.Layout"
msgid "Hello"
msgstr "Bonjour"
```

### 来自服务

上下文字符串必须与注入本地化器的类型的完整名称匹配。

#### 源

```csharp
namespace MyNamespace
{
    public class MyService : IMyService
    {
        private readonly IStringLocalizer S;

        public MyService(IStringLocalizer<MyService> localizer)
        {
            S = localizer;
        }

        public void DoSomething()
        {
            Console.WriteLine(S["Hello"]);
        }
    }
}
```

#### PO文件

```
msgctxt "MyNamespace.MyService"
msgid "Hello"
msgstr "Bonjour"
```

## 复数形式

该模块还提供了复数形式的支持。
需要引用`OrchardCore.Localization.Abstractions`包才能使用它。

### 示例PO文件

```
msgctxt "TheAdmin.Views.Layout"
msgid "1 book"
msgid_plural "{0} books"
msgstr[0] "[1 livre]"
msgstr[1] "[{0} livres]"
```

### 用法

- 导入`using Microsoft.Extensions.Localization`命名空间。
- 注入`IStringLocalizer`或`IViewLocalizer`的实例（在以下示例中表示为`T`变量）。

```csharp
T.Plural(count, "1 book", "{0} books")
```

### 提取翻译到PO文件

为了生成`.po`文件，您可以使用[此工具](https://github.com/OrchardCoreContrib/OrchardCoreContrib.PoExtractor)。

使用此命令安装它的最简单方法是：

```bash
dotnet tool install --global OrchardCoreContrib.PoExtractor
```

然后，您将能够运行此命令来生成`.po`文件：

``` bash
extractpo <INTPUT_PATH> <OUTPUT_PATH> [-l|--language {"C#"|"VB"}] [-t|--template {"razor"|"liquid"}]
