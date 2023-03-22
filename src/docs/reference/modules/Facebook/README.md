# Facebook (`OrchardCore.Facebook`)

## Facebook 模块

`OrchardCore.Facebook` 提供以下功能：

- 核心组件
- Facebook 登录
- Facebook 小部件

可以通过管理仪表板中的 _Configuration -> Facebook_ 设置菜单设置配置。

## 核心组件

将 Facebook 应用程序注册到站点。

可用设置为：

- AppId：Facebook 应用程序 ID。
- AppSecret：应用程序密钥。
- Javascript SDK 版本：要加载的 FB SDK
- Javascript Sdk js：要加载的 sdk js 文件
- 在每个页面上初始化：如果设置了 sdk，则在每个页面上加载，否则按需加载。
- 用于 FB.init() 调用的参数：以逗号分隔的键值，这些键值传递给 FB.init() 函数

有关更多信息，请查看 [Facebook SDK for JavaScript](https://developers.facebook.com/docs/javascript/quickstart)。

AppId 和 AppSecret 设置可在 [Facebook for Developers 应用程序](https://developers.facebook.com/apps) 页面的基本设置中找到。

它使用 ResourceManager（资源：fb 和 fbsdk）注册 sdk，因此您可以从Liquid或Razor模板中使用它

## Facebook 登录

从 Facebook 验证用户。
如果站点允许注册新用户，则创建本地用户并链接 Facebook 登录。
如果找到具有相同电子邮件的本地用户，则在身份验证后将外部登录链接到该帐户。

应在 [Facebook for Developers 页面](https://developers.facebook.com/apps) 中启用 Facebook 登录产品，以供 Web 应用程序使用，并设置有效的 OAuth 重定向 URI。

可用设置为：

- CallbackPath：应用程序基本路径内的请求路径，其中将返回用户代理。中间件将在到达时处理此请求。
如果未提供任何值，则设置 facebook 应用程序以使用默认路径 /signin-facebook。

## 用户注册

- 如果要通过其 Facebook 登录启用新用户注册到站点，则必须启用 `OrchardCore.Users.Registration` 功能并相应地进行设置。
- 现有用户可以通过用户菜单中的外部登录链接将其帐户链接到其 Facebook 登录

## Facebook 社交插件小部件

此模块添加了一个 FacebookPlugin 部分，可用于集成 [社交插件](https://developers.facebook.com/docs/plugins)
它定义了以下小部件：

- 聊天
- 评论
- 继续
- 喜欢
- 引用
- 保存
- 分享

## Facebook 设置配置

`OrchardCore.Facebook` 模块允许用户使用配置值覆盖从管理区域配置的设置，通过在初始化应用程序时在 `OrchardCoreBuilder` 上调用 `ConfigureFacebookSettings()` 扩展方法。

可以自定义以下配置值：

```json
    "OrchardCore_Facebook": {
      "AppId": "",
      "AppSecret": "",
      "FBInit": false,
      "FBInitParams": "status:true,
xfbml:true,
autoLogAppEvents:true",
      "SdkJs": "sdk.js",
      "Version": "v3.2"
    }
```

有关更多信息，请参见 [Configuration](../../core/Configuration/README.md)。
> 该文档由ChatGPT 4 翻译
