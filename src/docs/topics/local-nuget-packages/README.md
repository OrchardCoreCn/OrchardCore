# 使用 Orchard Core 源代码的本地副本作为 nuget 包

在本文中，我们将创建自己的本地 nuget feed，从我们的 Orchard Core 源代码副本中添加新的包源指向本地包。

## 从您的本地源代码创建NuGet包。

有关 dotnet pack 的更多信息，请参见：<https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-pack>

- 从命令行转到 Orchard Core 源代码的分支/主目录的根文件夹。
- 将所有 NuGet 包打包到一个您选择的输出文件夹中。  
例如：`dotnet pack -c 发布 -o c:\OrchardCoreNuget`

## 发布到您的 NuGet feed
对于此示例，我们将使用本地 Feed 方法。有关此的更多信息，请参见：<https://docs.microsoft.com/en-us/nuget/hosting-packages/local-feeds>

- 创建一个文件夹用于您的 NuGet Feed。  
对于此示例，我们使用 `\\{YourServer}\NuGetServer`
- 将 NuGet 包添加到本地 feed 中。  
例如：`nuget init c:\OrchardCoreNuget \\{YourServer}\NuGetServer`
## 更新项目以使用 NuGet 源

- 更新 nuget.config 文件，使其指向您的本地源。
https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file#packagesources
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <clear />
        <add key="MyFeed" value="\\{YourServer}\NuGetServer" />
        <add key="NuGet" value="https://api.nuget.org/v3/index.json" />
      </packageSources>
      <disabledPackageSources />
    </configuration>
```
- 确保所有的项目都是使用本地源中的 OrchardCore 版本。


> 该文档由Chat-GPT 翻译
