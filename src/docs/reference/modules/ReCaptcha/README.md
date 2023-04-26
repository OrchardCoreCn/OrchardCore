# ReCaptcha (`OrchardCore.ReCaptcha`)

OrchardCore.ReCaptcha模块可用于防止机器人滥用您的OrchardCore网站。

该模块有四个功能：

## 配置
为了激活ReCaptcha功能，您必须在Google上创建一个帐户，并在管理部分中输入密钥和站点密钥。
您可以在此处注册：https://developers.google.com/recaptcha/

### 用户保护
您可以在管理部分中启用此功能，您的登录页面将受到机器人的保护。
该功能将使用请求的IP地址来计算登录尝试次数。
当登录尝试次数的阈值被打破时，登录页面上会显示验证码，以防止机器人进行进一步的请求。

### 表单
您可以在设计表单时包含recaptcha字段，以保护表单免受机器人攻击。

### 工作流
您可以在工作流中添加验证ReCaptcha任务。
您可以使用此功能验证您在OrchardCore.Forms表单上显示的验证码。

### 手动验证
您可以使用[ValidateReCaptcha]属性装饰您的控制器。
此属性与<recaptcha /> HTML元素配合使用，两者都需要配置。
标准模式是PreventAbuse，当怀疑有机器人时，它将显示验证码。
AlwaysShow模式始终在页面上显示验证码。
如果您需要以特定语言显示验证码，则可以使用语言属性将其设置为所需的语言，使用语言的区域字符串或语言的两个字母ISO代码。

## 扩展模块
如果您有要求，必须使用IP地址以外的另一种方法来保护自己免受机器人攻击，
您可以创建自己的IDetectRobots接口实现，它将加入机器人检测器。

## 使用来自javascript框架的Content-Type =“application / json”的表单提交

ReCaptcha api使用data-callback属性返回验证ReCaptcha小部件时生成的令牌。这允许从Angular，Vue.js表单提交中发布该令牌。如果要从Workflow任务验证ReCaptcha，则需要将令牌作为“g-recaptcha-response”传递到请求的标头中。
> 该文档由ChatGPT 4 翻译
