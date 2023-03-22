# 通知 (`OrchardCore.Notifications`)

`Notifications` 模块提供了向应用程序用户发送通知所需的基础设施。

## 通知方法

有许多方法可以向用户发送通知（例如，电子邮件、Web 推送、移动推送、短信等）。除了通知中心外，OrchardCore 还提供了基于电子邮件的通知功能。要允许用户通过电子邮件接收通知，请启用 `电子邮件通知` 功能。

!!! 注意
使用 `电子邮件通知` 功能时，您还必须配置 [SMTP 服务](../Email/README.md)。当启用多个通知方法时，用户可以通过编辑其个人资料选择接收/不接收任何方法。

## 添加自定义通知提供程序
要添加新的通知方法，例如 `Web 推送`、`移动推送` 或 `短信`，只需实现 `INotificationMethodProvider` 接口。然后，注册您的新实现。例如，在 `电子邮件通知` 功能中，我们像这样注册电子邮件通知提供程序

```C#
[Feature("OrchardCore.Notifications.Email")]
public class EmailNotificationsStartup : StartupBase
{
    public override void ConfigureServices(IServiceCollection services)
    {
        services.AddScoped<INotificationMethodProvider, EmailNotificationProvider>();
    }
}
```

## 如何发送通知

您可以通过注入 `INotificationService` 并调用 `SendAsync(...)` 方法来通过代码向用户发送通知。或者，您可以使用工作流来通知用户有关发生的事件。

## 工作流活动
当启用 `OrchardCore.Workflows` 功能时，您将看到新的活动，这些活动将允许您使用工作流通知用户。以下是一些可用的工作流任务
 - 通知内容的所有者任务
 - 通知用户任务
