# Google (`OrchardCore.Google`)

该模块为OrchardCore添加了Google功能。

## Google Analytics

启用Google Analytics功能以在前端网站上集成跟踪。

转到[Google Analytics](https://analytics.google.com/analytics/web)门户网站并选择要使用的分析帐户。

从Admin->Tracking Info->Tracking Code链接中复制跟踪ID。

要配置Orchard Core，请转到管理仪表板中的_Google->Google Analytics_设置菜单并输入跟踪ID。

## Google Tag Manager

启用Google Tag Manager功能以在前端网站上集成。

转到[Google Tag Manager](https://tagmanager.google.com/)门户网站并创建一个标记管理器帐户。

复制为您生成的_Container ID_以在您的网站上使用。

要配置Orchard Core，请转到管理仪表板中的_Google->Google Tag Manager_设置菜单并输入容器ID。

## Google身份验证

启用Google身份验证以允许用户使用其Google帐户登录。

在[Google API控制台](https://console.developers.google.com/projectselector/apis/library)中创建一个项目。

将Google+ API添加到您的项目中。导航到凭据并为您的站点创建凭据。

在“您使用哪个API”问题中选择Google+ API

在“您将从哪里调用API”问题中选择Web服务器（例如node.js，Tomcat）。

在“您将访问哪些数据”问题中选择“用户数据”

现在单击“我需要什么凭据？”按钮并设置ClientID。 
您还必须将授权重定向URI设置为指向您的Orchard实例。 OrchardCore中的默认URL为[tenant] / signin-google

下一步是参数化将显示给用户的同意屏幕。

现在，您可以下载您的凭据。

可以通过管理仪表板中的_Google->Google身份验证_设置菜单设置配置。

可用设置包括：

+ ClientID：下载的json文件中的client_id字段值。
+ ClientSecret：下载的json文件中的client_secret字段值。
+ CallbackPath：用户代理将返回的应用程序基本路径内的请求路径。 中间件将在到达时处理此请求。 
如果未提供任何值，请在Google API中设置回调URL以使用默认路径/signin-google。

## 用户注册

+ 如果您想通过他们的Google帐户启用新用户注册到站点，则必须启用和相应设置`OrchardCore.Users.Registration`功能。
+ 现有用户可以通过用户菜单中的外部登录链接将其帐户链接到其Google帐户。

## Google设置配置

`OrchardCore.Google`模块允许用户使用配置值覆盖从管理区域配置的设置，通过在初始化应用程序时调用`OrchardCoreBuilder`上的`ConfigureGoogleSettings()`扩展方法。

可以自定义以下配置值：

```json
    "OrchardCore_Google": {
      "ClientID": "",
      "ClientSecret": "",
      "CallbackPath": "/signin-google",
      "SaveTokens": false
    }
```

有关更多信息，请参见[Configuration](../../core/Configuration/README.md)。
> 该文档由ChatGPT 4 翻译
