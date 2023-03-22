# GitHub (`OrchardCore.GitHub`)

该模块为OrchardCore添加了GitHub身份验证。

## 使用GitHub进行身份验证

使用用户的GitHub帐户进行身份验证。

在[GitHub开发人员应用程序](https://GitHub.com/settings/developers)中创建OAuth应用程序。  
在应用程序详细信息中，您必须配置授权回调URL。 OrchardCore中的默认URL为[tenant] / signin-github。

可以通过管理仪表板中的_GitHub->使用GitHub进行身份验证_设置菜单进行配置。

可用设置为：

- ClientID：在GitHub应用程序中找到的客户端ID。
- Client Secret：您的GitHub应用程序的秘密密钥。
- CallbackPath：应用程序基本路径内的请求路径，其中将返回用户代理。中间件将在到达时处理此请求。  
如果未提供任何值，请在GitHub应用程序中设置授权回调URL以使用默认路径/signin-github。

## 用户注册

- 如果要启用新用户通过其GitHub帐户注册到站点，则必须启用和相应设置`OrchardCore.Users.Registration`功能。
- 现有用户可以通过用户菜单中的外部登录链接将其帐户链接到其GitHub帐户。

## GitHub设置配置

`OrchardCore.GitHub`模块允许用户使用配置值覆盖从管理区域配置的设置，方法是在初始化应用程序时在`OrchardCoreBuilder`上调用`ConfigureGitHubSettings()`扩展方法。

可以自定义以下配置值：

```json
    "OrchardCore_GitHub": {
      "ClientID": "",
      "ClientSecret": "",
      "CallbackPath": "/signin-github",
      "SaveTokens": false
    }
```

有关更多信息，请参见[Configuration](../../core/Configuration/README.md)。
> 该文档由ChatGPT 4 翻译
