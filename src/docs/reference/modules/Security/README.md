# 安全 (`OrchardCore.Security`)

该模块添加了`HTTP`头以遵循安全最佳实践。

## 安全设置

启用`OrchardCore.Security`模块将允许用户设置以下设置：

| 设置 | 描述 |
| --- | --- |
| `ContentSecurityPolicy` | 获取或设置`Content-Security-Policy` HTTP头。 |
| `ContentTypeOptions` | 获取或设置`X-Content-Type-Options` HTTP头。 |
| `PermissionsPolicy` | 获取或设置`Permissions-Policy` HTTP头。 |
| `ReferrerPolicy` | 获取或设置`Referrer-Policy` HTTP头。 |

## 安全设置配置

`OrchardCore.Security`模块允许用户使用配置值通过调用`ConfigureSecuritySettings()`扩展方法来覆盖`AdminSettings`。

以下配置值可以自定义：

```json
    "OrchardCore_Security": {
      "ContentSecurityPolicy": {},
      "PermissionsPolicy": { "fullscreen": "self" },
      "ReferrerPolicy": "no-referrer"
    }
```

有关更多信息，请参见[Configuration](../../core/Configuration/README.md)。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/nYfNq8sTIAg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
