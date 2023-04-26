# 用户 (`OrchardCore.Users`)

用户模块启用身份验证UI和用户管理。

## 特征

该模块除了基本功能外，还包含以下功能：

- 用户更改电子邮件：允许用户更改其电子邮件地址。
- 用户注册：允许外部用户注册网站并要求确认其电子邮件。
- 重置密码：允许用户重置密码。
- 用户时区：提供一种设置每个用户的时区的方法。
- 自定义用户设置：请参阅[其自己的文档页面](CustomUserSettings/README.md)。
- [用户身份验证票证存储](./TicketStore.md)：将用户身份验证票证存储在服务器内存缓存中，而不是cookie。如果启用了分布式缓存功能，则会将身份验证票证存储在分布式缓存中。

## 自定义路径

如果您想指定自定义路径以访问身份验证相关的URL，则可以使用appsettings.json中的此选项更改它们：

``` json
  "OrchardCore": {
    "OrchardCore_Users": {
      "LoginPath": "Login",
      "LogoffPath": "Users/LogOff",
      "ChangePasswordUrl": "ChangePassword",
      "ChangePasswordConfirmationUrl": "ChangePasswordConfirmation",
      "ExternalLoginsUrl": "ExternalLogins"
    }
  }
```

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/78m04Inmilw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/ZgDkWUi2HGs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
> 该文档由ChatGPT 4 翻译
