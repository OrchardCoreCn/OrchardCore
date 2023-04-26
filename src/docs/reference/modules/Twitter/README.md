# Twitter (`OrchardCore.Twitter`)

该模块为OrchardCore添加了Twitter for Websites功能。

## Twitter集成

与Twitter集成。提供了一个客户端来集成Twitter API
可以通过管理仪表板中的_Twitter-> Twitter Integration_菜单设置配置。

可用设置为：

- API密钥：在Twitter应用程序的密钥和令牌选项卡中找到的API密钥。
- API秘密密钥：您的Twitter应用程序的API秘密密钥。
- 访问令牌：在Twitter应用程序的密钥和令牌选项卡中找到的访问令牌密钥。
- 访问令牌秘密：您的Twitter应用程序的访问令牌秘密密钥。

### 工作流

如果启用了OrchardCore.Workflows，则会添加一个新任务以更新Twitter状态

## 使用Twitter登录

使用其Twitter帐户对用户进行身份验证。

在[Twitter Developer Platform]（https://developer.twitter.com）中创建一个应用程序，并启用使用Twitter登录。
在应用程序详细信息中，必须配置回调URL。 OrchardCore中的默认URL为[tenant] / signin-twitter。

可以通过管理仪表板中的_Twitter->使用Twitter登录_设置菜单设置配置。

可用设置为：

- ConsumerKey：在Twitter应用程序的密钥和令牌选项卡中找到的API密钥。
- ConsumerSecret：您的Twitter应用程序的API秘密密钥。
- CallbackPath：用户代理将返回的应用程序基本路径内的请求路径。当它到达时，中间件将处理此请求。
如果未提供任何值，请在Twitter应用程序中设置回调URL以使用默认路径/signin-twitter。

### 用户注册

- 如果要通过其Twitter帐户启用新用户注册到站点，则必须启用`OrchardCore.Users.Registration`功能并相应地进行设置。
- 现有用户可以通过用户菜单中的外部登录链接将其帐户链接到其Twitter帐户。

## Twitter设置配置

`OrchardCore.Twitter`模块允许用户通过在初始化应用程序时调用`OrchardCoreBuilder`上的`ConfigureTwitterSettings（）`扩展方法来使用配置值覆盖从管理区域配置的设置。

可以自定义以下配置值：

```json
    "OrchardCore_Twitter": {
      "ConsumerKey": "",
      "ConsumerSecret": "",
      "AccessToken": "",
      "AccessTokenSecret": ""
    }
```

有关更多信息，请参见[Configuration]（../../core/Configuration/README.md）。
> 该文档由ChatGPT 4 翻译
