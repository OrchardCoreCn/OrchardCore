# 添加预览包源

在本文中，我们将添加一个指向预览包的新包源。   
每次在 dev 分支上提交一些代码时都会构建预览包，和从master分支构建的Nuget包相比，
它们是最新版本，但不是最稳定的，并且可能包含重大更改。 

!!! 警告
    我们不建议您在生产中使用 dev 包。

## 将 Orchard Core 预览源添加到 Visual Studio

为了能够在 Visual Studio 裡使用预览源，请打开 Tools → NuGet Package Manager → Package Manager Settings, 
並添加包源网址 <https://nuget.cloudsmith.io/orchardcore/preview/v3/index.json>

![image](assets/add-preview-package-source.png)


## 使用 NuGet.config 添加 Orchard Core 预览源

您也可以通过 `NuGet.config` 文件添加包源，将 该文件添加到你的解决方案中，并放置于sln 相同目录：

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
