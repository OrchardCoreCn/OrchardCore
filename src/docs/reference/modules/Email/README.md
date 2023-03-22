# Email (`OrchardCore.Email`)

该模块提供了使用 `SMTP` 发送电子邮件所需的基础设施。

## SMTP 设置

启用 `OrchardCore.Email` 模块将允许用户设置以下设置：

| 设置 | 描述 |
| --- | --- |
| `DefaultSender` | 发件人的电子邮件。 |
| `DeliveryMethod` | 发送电子邮件的方法，`SmtpDeliveryMethod.Network`（在线）或 `SmtpDeliveryMethod.SpecifiedPickupDirectory`（离线）。 |
| `PickupDirectoryLocation` | 邮箱的目录位置（`SmtpDeliveryMethod.SpecifiedPickupDirectory`）。 |
| `Host` | SMTP 服务器。 |
| `Port` | SMTP 端口号。 |
| `AutoSelectEncryption` | SMTP 是否自动选择加密。 |
| `RequireCredentials` | SMTP 是否需要用户凭据。 |
| `UseDefaultCredentials` | SMTP 是否使用默认凭据。 |
| `EncryptionMethod` | SMTP 加密方法 `SmtpEncryptionMethod.None`、`SmtpEncryptionMethod.SSLTLS` 或 `SmtpEncryptionMethodSTARTTLS`。 |
| `UserName` | 发件人的用户名。 |
| `Password` | 发件人的密码。 |
| `ProxyHost` | 代理服务器。 |
| `ProxyPort` | 代理端口号。 |

!!! 注意
    如果 SMTP 服务器通过代理服务器运行，则必须配置 `ProxyHost` 和 `ProxyPort`。

## 电子邮件设置配置

`OrchardCore.Email` 模块允许用户使用配置值通过在初始化应用程序时调用 `OrchardCoreBuilder` 上的 `ConfigureEmailSettings()` 扩展方法来覆盖从管理区域配置的设置。

以下配置值可以自定义：

```json
    "OrchardCore_Email": {
      "DefaultSender": "",
      "DefaultSender": "Network",
      "PickupDirectoryLocation": "",
      "Host": "localhost",
      "Port": 25,
      // Uncomment if SMTP server runs through a proxy server
      //"ProxyHost": "proxy.domain.com",
      //"ProxyPort": 5050,
      "EncryptionMethod": "SSLTLS",
      "AutoSelectEncryption": false,
      "UseDefaultCredentials": false,
      "RequireCredentials": true,
      "Username": "",
      "Password": ""
    }
```

有关更多信息，请参见 [Configuration](../../core/Configuration/README.md)。

## Credits

### MailKit

<https://github.com/jstedfast/MailKit>

版权所有 2013-2019 Xamarin Inc
根据 MIT 许可证许可
> 该文档由ChatGPT 4 翻译
