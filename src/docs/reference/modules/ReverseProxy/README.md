# 反向代理 (`OrchardCore.ReverseProxy`)

启用反向代理的托管场景配置，例如要转发哪些 HTTP 标头。

## 反向代理设置配置

`OrchardCore.ReverseProxy` 模块允许用户使用配置值来覆盖通过调用 `OrchardCoreBuilder` 上的 `ConfigureReverseProxySettings()` 扩展方法从管理区域配置的设置来初始化应用程序。

以下配置值可以自定义：

```json
    "OrchardCore_ReverseProxy": {
      "ForwardedHeaders": "None"
    }
```

有关更多信息，请参见[配置](../../core/Configuration/README.md)。
