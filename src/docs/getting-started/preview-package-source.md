# 添加预览包源

在本文中，我们将添加一个指向预览软件包的新软件包源。预览软件包是每次在`dev`分支上提交代码时构建的，与在NuGet上构建的软件包不同，后者是从`master`分支构建的。它们是最最新的版本，但不是最稳定的，可能包含破坏性变更。

!!! 警告
    我们不建议您在生产中使用 dev 软件包。

## 添加 Orchard Core 预览 Feed 到 Visual Studio

为了能够从Visual Studio使用__预览__源，打开NuGet包管理器下的Tools菜单-->包管理器设置。该源网址为<https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json>

![image](assets/add-preview-package-source.png)


## 使用 NuGet.config 添加 Orchard Core 预览 Feed

您还可以使用NuGet.config文件添加软件包源：
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <clear />
    <add key="NuGet" value="https://api.nuget.org/v3/index.json" />
    <add key="OrchardCorePreview" value="https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json" />
  </packageSources>
  <disabledPackageSources />
</configuration>
``` 
该文档为XML配置文件，配置了NuGet 包管理器的包源。其中，NuGet 是一个公共的包源，而OrchardCorePreview 是一种自定义的包源。'disabledPackageSources' 中的包源被设为禁用状态。



> 该文档由Chat-GPT 翻译