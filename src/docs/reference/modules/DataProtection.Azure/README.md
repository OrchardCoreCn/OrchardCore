# 数据保护（Azure 存储）(`OrchardCore.DataProtection.Azure`)

## 目的

数据保护（Azure 存储）启用了默认按租户隔离并存储在 Azure Blob 存储容器中的数据保护密钥环。这对于负载平衡环境非常有用，其中每个活动节点都需要共享相同的密钥环。

## 配置

您需要指定存储帐户连接字符串和有效的容器名称。如果容器不存在，则会自动创建容器。

这些设置需要对 `IShellConfiguration` 实现可用。在最简单的情况下，这将意味着更新您的 `appsettings.json` 文件：

```json
{
  "OrchardCore": {
    "OrchardCore_DataProtection_Azure": {
      "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccountname>;AccountKey=<myaccountkey>;EndpointSuffix=core.windows.net",
      // 设置为 Azure Blob 容器名称。容器名称必须是有效的 DNS 名称并符合 Azure 容器命名规则，例如仅小写。
      "ContainerName": "dataprotection",
      "BlobName": "",
      "CreateContainer": true
    }
  }
}
```

默认情况下，这将使用单个容器存储所有基于每个租户配置的数据保护密钥。

`dataprotection/Sites/tenant_name/DataProtectionKeys.xml`

在 `Startup` 期间，如果将 `CreateContainer` 设置为 true，则数据保护将检查容器是否存在，并在不存在时创建它。
如果容器已经存在，请将 `CreateContainer` 设置为 `false` 以禁用此检查。

## 模板配置

您可以选择使用Liquid模板进一步配置数据保护。
`ShellSettings` 属性可用于Liquid模板。
`ContainerName` 属性和 `BlobName` 属性是唯一可模板化的属性。
如果未提供，则 `BlobName` 将自动默认为每个租户配置的文件夹，即 `Sites/tenant_name/DataProtectionKeys.xml`

!!! 注意
当使用 `{{ ShellSettings.Name }}` 模板化 `ContainerName` 时，租户的名称将自动转换为小写，但是，您还必须确保 `ContainerName` 符合 Azure 的其他 Blob 命名约定，如 Azure 文档中所述。

### 配置每个租户的容器。

```json
{
  "OrchardCore": {
    "OrchardCore_DataProtection_Azure": {
      "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccountname>;AccountKey=<myaccountkey>;EndpointSuffix=core.windows.net",
      // 可选使用Liquid模板进行配置。容器名称必须是有效的 DNS 名称并符合 Azure 容器命名规则，例如仅小写。
      "ContainerName": "{{ ShellSettings.Name }}-dataprotection",
      "BlobName": "{{ ShellSettings.Name }}DataProtectionKeys.xml",
      "CreateContainer": true
    }
  }
}
```

!!! 注意
    在解析Liquid模板期间，仅可用默认Liquid过滤器和标记。
    额外的过滤器，如 `slugify`，将不可用。

还请参阅[配置部分](../../core/Configuration/README.md)。
> 该文档由ChatGPT 4 翻译
