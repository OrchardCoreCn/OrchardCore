# Amazon S3 媒体存储 (`OrchardCore.Media.AmazonS3`)

Amazon 媒体存储功能支持将资源存储在 Amazon S3 存储桶中。

该功能将默认的 App_Data 基于文件的媒体存储替换为 Amazon 媒体存储提供程序。

媒体仍由 Orchard Core 网站提供，媒体缓存模块负责从 Amazon S3 存储桶中动态获取媒体。

这使得 Amazon 媒体存储功能通过与 `ImageSharp.Web` 集成支持动态调整图像大小。

AssetUrl 助手生成的 URL 指向 Orchard Core 网站。

## 配置

默认情况下使用以下配置值，可以进行自定义：

```json
{
   "OrchardCore": {
       "OrchardCore_Media_AmazonS3": {
           // 如果您已安装并配置了 AWS CLI，则可以只指定配置文件名称。
           "Profile": "",
           // 如果您的 AWS 配置文件不在默认位置，则需要指定位置。
           "ProfilesLocation": "",
           "Region": "",
           // 仅在 Orchard 不在 AWS 云中托管时需要此部分
           // 您可以在 IAM 管理控制台中获取所有这些信息
            "Credentials": {
              "SecretKey": "",
              "AccessKey": ""
           },
           // 可选地，设置为存储容器内子目录中的媒体路径。
           "BasePath": "/media",
           "CreateBucket": false,
           // 您的 AWS S3 存储桶名称。
           "BucketName": ""
       }
  }
}
```

有两种托管选项：在 AWS 内部和外部。
如果您在 AWS（EC2、EKS 等）内部托管 Orchard Core，则只需要配置 `BucketName`，并且可以删除或注释掉其他部分。

如果您在 AWS 之外托管 Orchard Core，则应填写 `Credentials` 部分，或者如果您在服务器上安装并配置了 AWS CLI，则可以仅指定已配置的配置文件名称（如果在 AWS CLI 配置期间未选择配置文件名称，则默认为 `default`）。

您可以在 [官方 AWS S3 文档](https://docs.aws.amazon.com/general/latest/gr/s3.html) 中找到区域端点，参见区域列。例如，对于法兰克福地区，您应该使用 `eu-central-1`

## AWS 凭据及其加载顺序

`OrchardCore_Media_AmazonS3` 是 `AWSOptions` 配置的子集，并且应该与通用的 [AWSOptions](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/net-dg-config-netcore.html) 配置相同。

### 凭据加载顺序

1. `AWSOptions` 的凭据属性。
2. 共享凭据文件（自定义位置）。当指定了配置文件和配置文件位置时。
3. SDK 存储（仅限 Windows）。当提供了 `AWSOptions` 的实例并且仅设置了配置文件时（配置文件位置为 null 或为空）。
4. 共享凭据文件（默认位置）。当提供了 `AWSOptions` 的实例并且仅设置了配置文件时（配置文件位置为 null 或为空）。
5. AWS Web 身份验证凭据。当存在 OIDC 令牌文件并在环境变量中设置时。
6. `CredentialsProfileStoreChain`
   1. SDK 存储（仅限 Windows），使用 Windows 数据保护 API 加密。
   2. 默认位置的共享凭据文件。
7. 环境变量。当设置了 Access Key ID 和 Secret Access Key 环境变量时。
8. ECS 任务凭据或 EC2 实例凭据。当使用 ECS 任务和 ECS 实例的 IAM 角色时。

!!! 注意
    AWS 团队希望鼓励使用配置文件而不是直接将凭据嵌入到 `appsettings.X.json` 文件中，因为这样会意外地检查到源代码控制。
    如果您有使用配置文件或环境变量的选项，则应使用它，而不是直接凭据。

## AWS S3 存储桶配置

如果将 `CreateBucket` 配置为 `true`，并且 `BucketName` 遵循官方的 [Bucket 命名规则](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html)，则将创建一个新的存储桶。
由于安全原因，新存储桶将不带有 [访问控制列表](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acl-overview.html)。如果手动创建存储桶，则需要启用 ACL。当使用先前创建的存储桶时，您可能需要手动配置 ACL：

1. 打开您的存储桶。
2. 转到权限选项卡。
3. 编辑“阻止公共访问”。
4. 选中“阻止所有公共访问”。

### S3 存储桶策略
默认情况下，AWS 3S 存储桶对新上传的文件有限制。如果您希望媒体文件可以从 AWS 外部访问，则应设置存储桶权限。

最简单的方法是添加策略：
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AddPerm",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/YOR-BASE-PATH/*"
        }
    ]
}
```
将此策略添加到存储桶权限后，所有新添加的文件都将具有读取权限，并且可以从 Amazon Cloud 外部访问。

## 模板配置

您可以使用 Liquid 模板进一步配置 Amazon 媒体存储，例如为每个租户创建一个存储桶，或者为每个租户创建一个带有基本路径的单个存储桶。

`ShellSettings` 属性可用于 Liquid 模板。
`BucketName` 属性和 `BasePath` 属性是唯一可模板化的属性。

!!! 注意
    当使用 `{{ ShellSettings.Name }}` 模板化 `BucketName` 时，租户的名称将自动转换为小写，但是您还必须确保 `BucketName` 符合 Amazon 的其他命名约定，如 Amazon 的文档中所述。

### 为每个租户配置一个存储桶

```json
{
    "OrchardCore": {
        "OrchardCore_Media_AmazonS3": {
            "BucketName": "{{ ShellSettings.Name }}-media",
            "Region": "",
            "Credentials": {
                "SecretKey": "",
                "AccessKey": ""
            },
            "BasePath": "/media",
            "Profile": "",
            "ProfilesLocation": ""
        }
    }
}
```

### 配置单个存储桶，每个租户都有一个基本文件夹

```json
{
    "OrchardCore": {
        "OrchardCore_Media_AmazonS3": {
            "BucketName": "",
            "Region": "",
            "Credentials": {
                "SecretKey": "",
                "AccessKey": ""
            },
            "BasePath": "{{ ShellSettings.Name }}/Media",
            "Profile": "",
            "ProfilesLocation" : ""
        }
    }
}
```

## 媒体缓存

当启用 Amazon 媒体存储时，将自动启用媒体缓存功能。

媒体缓存功能将缓存存储在 Amazon S3 存储中的文件，以支持图像调整大小。

媒体缓存功能允许清除本地存储的媒体缓存文件。

如果您正在使用 CDN 前端媒体资产，则可能会选择使用清除功能。
在 CDN 获取了足够多的媒体资产（包括调整大小和全尺寸的媒体资产）之后，您可能会考虑清除缓存。

但是请记住，您的 CDN 提供商可能在全球范围内拥有多个 POP，每个 POP 都会维护自己的缓存，因此当本地 CDN PoP 具有资产时，另一个 PoP 可能没有资产，直到请求为止。此时，如果必要，媒体缓存将从 Amazon S3 存储桶中动态获取资产，并将其提供给 CDN PoP。

CDN 提供商还会在其自己的设计的预定时间清除其缓存，因此尽管 CDN 是有价值的缓存和性能资产，但重要的是始终能够根据需要重新获取源文件，媒体缓存模块将自动处理此问题。

!!! 注意
    媒体功能旨在一次支持一个存储提供程序，无论是
    本地文件存储（默认值）、Azure Blob 存储还是 Amazon S3 存储。
