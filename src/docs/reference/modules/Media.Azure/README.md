# Azure 媒体存储 (`OrchardCore.Media.Azure`)

Azure 媒体存储功能使得支持将资源存储在 Microsoft Azure Blob 存储中。

该功能将默认的 `App_Data` 基于文件的媒体存储库替换为 Azure 媒体存储提供程序。

媒体仍由 Orchard Core 网站提供，并且媒体缓存模块负责从 Azure Blob 存储容器中动态获取媒体。

这使得 Azure 媒体存储功能能够通过与 ImageSharp.Web 的集成来支持即时图像调整大小。

`AssetUrl` 助手生成的 URL 指向 Orchard Core 网站。

## 配置

默认情况下使用以下配置值，可以进行自定义：

```json
{
   "OrchardCore": {
    "OrchardCore_Media_Azure": {
      // 设置为 Azure 存储帐户连接字符串。
      "ConnectionString": "", 
      // 设置为 Azure Blob 容器名称。容器名称必须是有效的 DNS 名称并符合 Azure 容器命名规则，例如仅小写。
      "ContainerName": "somecontainer",
      // 可选地，设置为在容器内的子目录中存储媒体的路径。
      "BasePath": "some/base/path",
      "CreateContainer": true
    }
  }
}
```

还可以参考 [配置部分](../../core/Configuration/README.md) 和 [媒体部分](../Media/README.md) 以获取其他媒体相关配置设置。

如果将 `CreateContainer` 选项设置为 `true`，则激活事件将在 `Startup` 上检查有效的连接字符串，并测试容器是否存在，如果不存在则创建。
如果容器已经存在，请将 `CreateContainer` 设置为 `false` 以禁用此检查。

如果在 `appSettings.json` 中不存在这些内容，则不会启用该功能，并在日志文件中报告错误消息。

## 模板配置

您可以选择使用Liquid模板进一步配置 Azure 媒体存储，例如为每个租户创建一个容器，或者为每个租户创建一个带有基本路径的单个容器。

`ShellSettings` 属性可用于Liquid模板。
`ContainerName` 属性和 `BasePath` 属性是唯一可模板化的属性。

!!! 注意
    当使用 `{{ ShellSettings.Name }}` 模板化 `ContainerName` 时，租户的名称将自动转换为小写，但是，您还必须确保 `ContainerName` 符合 Azure 的其他 Blob 命名约定，如 Azure 文档中所述。

### 为每个租户配置一个容器。

```json
{
  "OrchardCore": {
    "OrchardCore_Media_Azure": {
      // 设置为 Azure 存储帐户连接字符串。
      "ConnectionString": "", 
      // 可选地使用Liquid配置。容器名称必须是有效的 DNS 名称并符合 Azure 容器命名规则，例如仅小写。
      "ContainerName": "{{ ShellSettings.Name }}-media",
      // 可选地使用Liquid配置。
      "BasePath": "Media",
      "CreateContainer": true
    }
  }
}
```
# Azure 媒体存储 (`OrchardCore.Media.Azure`)

Azure 媒体存储功能使得支持将资源存储在 Microsoft Azure Blob 存储中。

该功能将默认的 `App_Data` 基于文件的媒体存储库替换为 Azure 媒体存储提供程序。

媒体仍由 Orchard Core 网站提供，并且媒体缓存模块负责从 Azure Blob 存储容器中动态获取媒体。

这使得 Azure 媒体存储功能能够通过与 ImageSharp.Web 的集成来支持即时图像调整大小。

`AssetUrl` 助手生成的 URL 指向 Orchard Core 网站。

## 配置

默认情况下使用以下配置值，可以进行自定义：

```json
{
   "OrchardCore": {
    "OrchardCore_Media_Azure": {
      // 设置为 Azure 存储帐户连接字符串。
      "ConnectionString": "", 
      // 设置为 Azure Blob 容器名称。容器名称必须是有效的 DNS 名称并符合 Azure 容器命名规则，例如仅小写。
      "ContainerName": "somecontainer",
      // 可选地，设置为在容器内的子目录中存储媒体的路径。
      "BasePath": "some/base/path",
      "CreateContainer": true
    }
  }
}
```

还可以参考 [配置部分](../../core/Configuration/README.md) 和 [媒体部分](../Media/README.md) 以获取其他媒体相关配置设置。

如果将 `CreateContainer` 选项设置为 `true`，则激活事件将在 `Startup` 上检查有效的连接字符串，并测试容器是否存在，如果不存在则创建。
如果容器已经存在，请将 `CreateContainer` 设置为 `false` 以禁用此检查。

如果在 `appSettings.json` 中不存在这些内容，则不会启用该功能，并在日志文件中报告错误消息。

## 模板配置

您可以选择使用Liquid模板进一步配置 Azure 媒体存储，例如为每个租户创建一个容器，或者为每个租户创建一个带有基本路径的单个容器。

`ShellSettings` 属性可用于Liquid模板。
`ContainerName` 属性和 `BasePath` 属性是唯一可模板化的属性。

!!! 注意
    当使用 `{{ ShellSettings.Name }}` 模板化 `ContainerName` 时，租户的名称将自动转换为小写，但是，您还必须确保 `ContainerName` 符合 Azure 的其他 Blob 命名约定，如 Azure 文档中所述。

### 为每个租户配置一个容器。

```json
{
  "OrchardCore": {
    "OrchardCore_Media_Azure": {
      // 设置为 Azure 存储帐户连接字符串。
      "ConnectionString": "", 
      // 可选地使用Liquid配置。容器名称必须是有效的 DNS 名称并符合 Azure 容器命名规则，例如仅小写。
      "ContainerName": "{{ ShellSettings.Name }}-media",
      // 可选地使用Liquid配置。
      "BasePath": "Media",
      "CreateContainer": true
    }
  }
}
```

### 配置单个容器，每个租户都有一个基本文件夹。

```json
{
  "OrchardCore": {
    "OrchardCore_Media_Azure": {
      // 设置为 Azure 存储帐户连接字符串。
      "ConnectionString": "", 
      // 可选地使用Liquid配置。容器名称必须是有效的 DNS 名称并符合 Azure 容器命名规则，例如仅小写。
      "ContainerName": "somecontainer",
      // 可选地使用Liquid配置。
      "BasePath": "{{ ShellSettings.Name }}/Media",
      "CreateContainer": true
    }
  }
}
```

!!! 注意
    在解析Liquid模板时，仅可用默认的Liquid过滤器和标记。
    额外的过滤器（如 `slugify`）将不可用。

## 媒体缓存

启用 Azure 媒体存储时，将自动启用媒体缓存功能。

媒体缓存功能将缓存从 Azure Blob 存储中存储的文件，以支持图像调整大小。

媒体缓存功能允许清除本地存储的媒体缓存文件。

如果您正在使用 CDN 前端媒体资产，则可以选择使用清除功能。
在允许 CDN 从媒体缓存中获取足够长的时间后，包括调整大小和全尺寸的媒体资产，您可能考虑清除缓存。

但是，请记住，您的 CDN 提供商可能在全球范围内拥有多个点，
每个点都将维护自己的缓存，因此，即使本地 CDN PoP 具有资产，另一个 PoP 也可能没有资产，直到请求为止。
在此阶段，如果必要，媒体缓存将从 Azure Blog 存储中重新获取资产，并在需要时提供给 CDN PoP。

CDN 提供商还会在其自己的设计的预定时间清除其缓存，因此，虽然 CDN 是有价值的缓存和性能资产，
但重要的是，它们始终能够在需要时重新获取源文件，媒体缓存模块将自动处理此操作。

!!! 注意
    媒体功能旨在一次仅支持一个存储提供程序，无论是
    本地文件存储（默认值）、Azure Blob 存储还是 Amazon S3 存储。

> 该文档由ChatGPT 4 翻译
