# 工作流 (`OrchardCore.Workflows`)

工作流模块提供了一种使用流程图表实现业务规则的方式。

## 基本概念

工作流是一组相互连接的**活动**。这些连接称为**转换**。  
活动及其转换存储在**工作流定义**中。

工作流本质上是一个可视化脚本，其中每个活动都是该脚本的语句。

有两种类型的活动：**任务**和**事件**。  
任务活动通常执行操作，例如发布内容项，而事件活动通常在执行继续之前监听事件。

为了执行工作流，必须将至少一个活动标记为*工作流的开始*。  
只有事件活动可以标记为工作流的开始。  
这样的事件活动的示例是_内容已创建_，它在创建内容项时执行。  
工作流可以有多个开始事件。这使您可以响应各种类型的事件触发（运行）工作流。

每个活动都有一个或多个**结果**，它们表示可以从中连接到下一个活动的源端点，这些活动称为转换。  
通过连接活动，您实际上正在创建一个可以响应多种事件的程序。

![工作流编辑器](docs/workflow-editor.png)

1. 活动选择器（任务/事件）
2. 活动操作（单击活动以显示活动操作）
3. 配置为工作流的起始活动的活动。
4. 活动。
5. 活动的结果（“完成”）。
6. 两个活动之间的转换（从“内容已创建”通过“完成”结果到“发送电子邮件”活动）。
7. 工作流编辑器设计表面。
8. 编辑工作流定义属性（名称、启用等）。
9. 列出此工作流定义的工作流实例。

## 词汇表

在使用 Orchard 工作流时，您将遇到以下术语：

### 工作流定义

包含有关工作流的所有必要信息的文档（作为“文档-DB”文档），例如其名称、是否启用、其一组活动及其转换。

### 工作流实例

表示工作流定义的“实例”的文档。工作流实例包含工作流的运行时状态。  
每当启动工作流时，都会创建给定工作流定义的新工作流实例。

### 活动

工作流定义中的一步。  
活动执行操作并提供零个或多个结果，这些结果用于连接到要执行的下一个活动。  
有两种类型的活动：任务和事件。

### 任务

活动的一种专门类型。任务执行操作，例如发送电子邮件、发布内容和进行 HTTP 请求。

### 事件

活动的一种专门类型。  
与任务一样，事件可以执行操作，但通常它们只是停止工作流，等待事件发生，然后继续执行下一个活动。  
当事件配置为工作流的起始活动时，当触发该事件时，将启动该工作流。

### 工作流编辑器

允许您使用拖放可视界面创建和管理工作流定义的编辑器。

### 活动编辑器

大多数活动公开可通过活动编辑器配置的设置。  
要配置活动，可以在工作流编辑器的设计表面上双击活动，或者单击活动一次以激活提供各种活动操作的小弹出窗口之一。  
其中之一是*编辑*操作。

### 活动选择器

当您在工作流编辑器中时，可以使用活动选择器将活动添加到设计表面。  
单击**添加任务**或**添加事件**以分别添加任务或事件来打开活动选择器。

### 结果

每个活动都有零个或多个结果。当活动已执行时，它将与一组结果一起将控制权返回到工作流管理器，  
工作流管理器使用此结果列表确定要执行的活动。

尽管许多活动支持多个结果，但它们通常在执行完成后仅返回其中一个。  
例如，_发送电子邮件_活动有两个可能的结果：“完成”和“失败”。  
当成功发送电子邮件时，它将“完成”作为结果，否则将返回“失败”。

### 转换

转换是将一个活动的结果连接到另一个活动的过程。转换是在工作流编辑器中使用拖放操作创建的。

### 工作流管理器

可以执行工作流的服务类。执行工作流时，它负责创建工作流实例，然后执行该实例。

### 工作流执行上下文

当工作流管理器执行工作流时，它创建一个称为工作流执行上下文的对象。工作流执行上下文是与工作流执行相关的所有信息的集合。例如，它包含对工作流实例、工作流定义、关联值、输入、输出和属性的引用。每个活动都可以访问此执行上下文。

### 关联

关联是将工作流实例与一个或多个标识符关联的行为。这些标识符可以是任何东西。例如，当工作流以“内容创建”事件作为起点时，工作流实例将与刚刚创建的内容项ID相关联，或者更确切地说，与之相关联。这允许长时间运行的工作流方案，其中仅恢复与给定内容项ID相关联的工作流实例。

### 输入

当执行工作流时，调用者可以向工作流实例提供输入。此输入存储在工作流执行上下文的“输入”字典中。这类似于向函数提供参数。

### 输出

当工作流执行时，每个活动都可以向工作流实例提供输出值。此输出存储在工作流执行上下文的“输出”字典中。这类似于从函数返回值。

### 属性

当工作流执行时，每个活动都可以将属性值设置为工作流实例。这些属性存储在工作流执行上下文的“属性”字典中。每个活动都可以设置和访问这些属性，允许工作流计算和检索可以由链中其他活动进一步处理的信息。这类似于函数设置本地变量。

## 工作流执行

当工作流执行时，**工作流管理器**创建一个**工作流实例**和一个**工作流执行上下文**。工作流实例维护有关执行的状态，例如要执行的下一个活动以及可以由各个活动提供的状态。工作流实例最终在底层数据存储提供程序中持久存在，而工作流执行上下文仅在工作流执行期间在内存中存在。工作流可以是**短期运行**和**长期运行**。

### 短期运行的工作流

当工作流执行时，没有遇到任何**阻塞**活动（即等待事件发生的活动，例如_Signal_）时，工作流将一次性运行到完成。

### 长期运行的工作流

当工作流执行并遇到阻塞活动（例如事件）时，工作流管理器将_停止_执行并将工作流实例持久化到底层持久性层。当触发适当的事件时（可能会在几秒、几天、几周甚至几年后发生），工作流管理器将从存储中加载工作流实例并恢复执行。

## 脚本和表达式

许多活动具有可以包含**JavaScript**或**Liquid**语法的设置。例如，当添加**Notify**活动时，其编辑器显示以下字段：  
这些类型的字段允许您输入Liquid标记，从而访问系统范围的变量和过滤器以及来自**工作流执行上下文**的变量。

### JavaScript 函数

以下 JavaScript 函数默认可用于支持脚本表达式的任何活动：

| 函数 | 描述 | 签名 |
| -------- | ----------- | --------- |
| `workflow` | 返回 `WorkflowExecutionContext`，它提供了访问当前工作流执行上下文的所有信息。 | `workflow(): WorkflowExecutionContext` |
| `workflowId` | 返回唯一的工作流 ID。 | `workflowId(): String` |
| `input` | 返回具有指定名称的输入参数。当工作流由工作流管理器执行时，会提供工作流的输入。 | `input(name: string): any` |
| `output` | 使用指定的名称设置输出参数。工作流输出可以由工作流的调用者收集。 | `output(name: string, value: any): void` |
| `property` | 返回具有指定名称的属性值。属性是工作流活动可以从中读取和写入信息的字典。 | `property(name: string): any` |
| `setProperty` | 在工作流属性中存储指定的数据。 | `setProperty(name: string,data:any):void` |
| `executeQuery` | 返回查询的结果，请参见[更多](../Queries/#scripting)。 | `executeQuery(name: String, parameters: Dictionary<string,object>): IEnumerable<object>` |
| `log` | 根据指定的日志级别输出日志。允许的日志级别：`'Trace','Debug','Information','Warning','Error','Critical','None'` | `log(level: string, text: string, param: object): void` |
| `lastResult` | 返回上一个活动提供的值（如果有）。 | `lastResult(): any` |
| `correlationId` | 返回工作流实例的相关值。 | `correlationId(): string` |
| `signalUrl` | 返回带有受保护的 SAS 令牌的工作流触发器 URL，其中编码了指定的信号名称。使用此功能生成可以与受信任的方共享以触发当前工作流的 URL，如果当前工作流被配置为使用相同的信号名称的信号活动阻塞，则可以使用此功能。 | `signalUrl(signal: string): string` |

#### HTTP 活动中的 JavaScript 函数

以下 JavaScript 函数默认可用于支持脚本表达式的任何 HTTP 活动：

| 函数 | 描述 | 签名 |
| -------- | ----------- | --------- |
| `httpContext` | 返回封装有关单个 HTTP 请求的所有 HTTP 特定信息的 `HttpContext`。 | `httpContext(): HttpContext` |
| `queryString` | 在不带参数的情况下调用时返回整个查询字符串（包括前导 `?`），或者返回传递的参数名称的值。 | `queryString(): String`<br/>`queryString(name: String): String` 或 `Array` |
| `responseWrite` | 将参数字符串直接写入 HTTP 响应流。 | `responseWrite(text: String): void` |
| `absoluteUrl` | 返回相对路径参数的绝对 URL。 | `absoluteUrl(relativePath: String): String` |
| `readBody` | 返回原始的 HTTP 请求正文。 | `readBody(): String` |
| `requestForm` | 返回传递的表单字段名称的值。 | `requestForm(): String`<br/>`requestForm(name: String): String` 或 `Array` |
| `deserializeRequestData` | 自动反序列化发送 JSON 或表单数据的请求数据。返回整个请求数据作为 JSON 对象。替换了 queryStringAsJson 和 requestFormAsJson 方法 | `deserializeRequestData(): { "field1": [ "field1-value1", "field1-value2" ], "field2": [ "field2-value1", "field2-value2" ], ... }` |

### Liquid 表达式

以下 Liquid 标记、属性和过滤器默认可用于支持 Liquid 表达式的任何活动：

| 表达式 | 类型 | 描述 | 示例 |
| ---------- | ---- | ----------- | ------- |
| `Workflow.CorrelationId` | 属性 | 返回工作流实例的相关值。 | `{{ Workflow.CorrelationId }}` |
| `Workflow.Input` | 属性 | 返回输入字典。 | `{{ Workflow.Input["ContentItem"] }}` |
| `Workflow.Output` | 属性 | 返回输出字典。 | `{{ Workflow.Output["SomeResult"] }}` |
| `Workflow.Properties` | 属性 | 返回属性字典。 | `{{ Workflow.Properties["Foo"] }}` |
| `signal_url` | 过滤器 | 返回工作流触发器 URL。您可以使用 `input("Signal")` JavaScript 方法检查触发的信号。 | `{{ 'Approved' \| signal_url }}` |

除了在三个工作流字典 `Input`、`Output` 和 `Properties` 上使用索引器语法之外，您还可以使用点符号表示法，例如：

```liquid
{{ Workflow.Input.ContentItem }}
```

### Liquid 表达式和 ContentItem 事件

使用工作流处理与内容相关的事件时，相关的内容项通过 `Input` 字典提供给工作流。  
例如，如果您有一个以 **Content Created Event** 活动开始的工作流，您可以发送电子邮件或进行 HTTP 请求，并从启用了 Liquid 的字段引用内容项，如下所示：

```liquid
{{ Workflow.Input.ContentItem | display_url }}
{{ Workflow.Input.ContentItem | display_text }}
{{ Workflow.Input.ContentItem.DisplayText }}
```

有关支持的内容项过滤器的更多示例，请参见 [Liquid](../Liquid/README.md) 文档。

## 开箱即用的活动

以下活动在任何默认的Orchard安装中都可用：

| 活动 | 类型 | 描述 |
| -------- | ---- | ----------- |
| **工作流** | * | * |
| Correlate | 任务 | 将当前工作流实例与一个值相关联。 |
| For Each | 任务 | 遍历列表。 |
| Fork | 任务 | 将工作流执行分叉成单独的执行路径。 |
| For Loop | 任务 | 迭代N次。 |
| If / Else | 任务 | 评估布尔条件并根据结果继续执行。 |
| Join | 任务 | 将分叉的工作流执行合并回单个执行路径。 |
| Log | 任务 | 写入日志条目。 |
| Notify | 任务 | 显示通知。 |
| Script | 任务 | 执行脚本并根据返回的结果继续执行。 |
| Set Output | 任务 | 评估脚本表达式并将结果存储到工作流的输出中。 |
| Set Property | 任务 | 执行脚本并根据返回的结果继续执行。 |
| While Loop | 任务 | 在条件为真时进行迭代。 |
| **HTTP工作流活动** | * | * | * |
| HTTP Redirect | 任务 | 将用户代理重定向到指定的URL（301/302）。 |
| HTTP Request | 任务 | 对给定的URL执行HTTP请求。 |
| Filter Incoming HTTP Request | 事件 | 在指定的HTTP请求到达时执行。类似于MVC操作筛选器。 |
| Signal | 事件 | 在触发信号时执行。 |
| **电子邮件** | * | * | * |
| Send Email | 任务 | 发送电子邮件。 |
| **计时器工作流活动** | * | * | * |
| Timer | 事件 | 根据指定的CRON表达式重复执行。 |
| **内容** | * | * | * |
| Content Created | 事件 | 在创建内容时执行。 |
| Content Deleted | 事件 | 在删除内容时执行。 |
| Content Published | 事件 | 在发布内容时执行。 |
| Content Unpublished | 事件 | 在取消发布内容时执行。 |
| Content Updated | 事件 | 在更新内容时执行。 |
| Content Versioned| 事件 | 在版本化内容时执行。 |
| Create Content | 任务 | 创建内容项。 |
| Delete Content | 任务 | 删除内容项。 |
| Publish Content | 任务 | 发布内容项。 |
**User** | * | * | * |
| ValidateUser | 任务 | 用于检查用户是否已登录并具有指定的角色。 |

## 开发自定义活动

Orchard旨在扩展，`Workflows`模块也不例外。创建自己的模块时，可以开发自定义工作流活动。  
开发自定义活动涉及以下步骤：

1. 创建一个新类，该类直接或间接实现`IActivity`。在大多数情况下，您要么从`TaskActivity`或`EventActivity`派生，具体取决于您的活动是否表示事件。虽然不是必需的，但建议将此类保存在名为`Activities`的文件夹中。
2. 创建一个新的**显示驱动程序**类，该类直接或间接实现`IDisplayDriver`。活动显示驱动程序控制活动在**工作流编辑器画布**，**活动选择器**和**活动编辑器**上的显示。虽然不是必需的，但建议将此类保存在名为`Drivers`的文件夹中。
3. 如果您的活动具有用户应该能够配置的属性，则可以选择实现**视图模型**。
4. 实现驱动程序提供的各种Razor视图的各种形状。虽然不是必需的，但建议将这些文件存储在`Views / Items`文件夹中。请注意，您的视图必须可被显示引擎发现。  

您可以通过在`IWorkflowManager`上调用`TriggerEventAsync`方法来触发自定义事件活动。以下是如何触发名为`CustomTaskActivity`的自定义事件的工作流的示例

```csharp
var customData = new CustomDto();

var input = new Dictionary<string, object>()
{
    // Here we are passing custom data to the workflow's input.
    { "data", customData}
};

await workflowManager.TriggerEventAsync("CustomTaskActivity", input);
```

您可以通过将自定义对象的实例添加到输入集合中来将其传递给工作流的输入。如果要使用Liquid访问自定义对象的成员，则必须注册成员访问策略。以下示例用于定义自定义类型。

```csharp
services.Configure<TemplateOptions>(o =>
{
    o.MemberAccessStrategy.Register<CustomDto>();
});
```

### 活动显示类型

活动具有以下显示类型：

- 缩略图
- 设计

**缩略图**
当活动作为活动选择器的一部分呈现时使用。

**设计**
当活动作为工作流编辑器设计表面的一部分呈现时使用。

### IActivity

`IActivity`具有以下成员：

- `Name`
- `Category`
- `DisplayText`
- `Properties`
- `HasEditor`
- `GetPossibleOutcomes`
- `CanExecuteAsync`
- `ExecuteAsync`
- `ResumeAsync`
- `OnInputReceivedAsync`
- `OnWorkflowStartingAsync`
- `OnWorkflowStartedAsync`
- `OnWorkflowResumingAsync`
- `OnWorkflowResumedAsync`
- `OnActivityExecutingAsync`
- `OnActivityExecutedAsync`

以下是一个简单的任务活动实现示例，用于显示通知：

```csharp
public class NotifyTask : TaskActivity
{
    private readonly INotifier _notifier;
    private readonly IStringLocalizer S;
    private readonly IHtmlLocalizer H;

    public NotifyTask(INotifier notifier, IStringLocalizer<NotifyTask> s, IHtmlLocalizer<NotifyTask> h)
    {
        _notifier = notifier;

        S = s;
        H = h;
    }

    // 活动的技术名称。工作流定义上的活动引用此名称。
    public override string Name => nameof(NotifyTask);

    // 活动的显示名称，因此它可以使用本地化。
    public override LocalizedString DisplayText => S["Notify Task"];

    // 此活动所属的类别。活动选择器按此类别对活动进行分组。
    public override LocalizedString Category => S["UI"];

    // 此活动目的的描述。
    public override LocalizedString Description => S["Display a message."];

    // 要显示的通知类型。
    public NotifyType NotificationType
    {
        get => GetProperty<NotifyType>();
        set => SetProperty(value);
    }

    // 要显示的消息。
    public WorkflowExpression<string> Message
    {
        get => GetProperty(() => new WorkflowExpression<string>());
        set => SetProperty(value);
    }

    // 返回此活动的可能结果。
    public override IEnumerable<Outcome> GetPossibleOutcomes(WorkflowExecutionContext workflowContext, ActivityContext activityContext)
    {
        return Outcomes(S["Done"]);
    }

    // 这是活动的核心，实际上执行要执行的工作。
    public override async Task<ActivityExecutionResult> ExecuteAsync(WorkflowExecutionContext workflowContext, ActivityContext activityContext)
    {
        var message = await workflowContext.EvaluateExpressionAsync(Message);
        _notifier.Add(NotificationType, H[message]);
        return Outcomes("Done");
    }
}
```

以下是一个简单的活动显示驱动程序示例：


public class NotifyTaskDisplayDriver : ActivityDisplayDriver<NotifyTask, NotifyTaskViewModel>
{
    protected override void EditActivity(NotifyTask activity, NotifyTaskViewModel model)
    {
        model.NotificationType = activity.NotificationType;
        model.Message = activity.Message.Expression;
    }

    protected override void UpdateActivity(NotifyTaskViewModel model, NotifyTask activity)
    {
        activity.NotificationType = model.NotificationType;
        activity.Message = new WorkflowExpression<string>(model.Message);
    }
}
```
上面的代码对`NotifyTask`和`NotifyTaskViewModel`进行了简单的映射。这个简单的实现是可能的，因为所需的编辑器和显示形状的实际创建是由`ActivityDisplayDriver<TActivity, TEditViewModel>`处理的，它看起来像这样（修改以便关注重要部分）：

```csharp
public abstract class ActivityDisplayDriver<TActivity, TEditViewModel> : ActivityDisplayDriver<TActivity> where TActivity : class, IActivity where TEditViewModel : class, new()
{
    private static string ThumbnailshapeType = $"{typeof(TActivity).Name}_Fields_Thumbnail";
    private static string DesignShapeType = $"{typeof(TActivity).Name}_Fields_Design";
    private static string EditShapeType = $"{typeof(TActivity).Name}_Fields_Edit";

    public override IDisplayResult Display(TActivity activity)
    {
        return Combine(
            Shape(ThumbnailshapeType, new ActivityViewModel<TActivity>(activity)).Location("Thumbnail", "Content"),
            Shape(DesignShapeType, new ActivityViewModel<TActivity>(activity)).Location("Design", "Content")
        );
    }

    public override IDisplayResult Edit(TActivity activity)
    {
        return Initialize<TEditViewModel>(EditShapeType, model =>
        {
            return EditActivityAsync(activity, model);
        }).Location("Content");
    }

    public async override Task<IDisplayResult> UpdateAsync(TActivity activity, IUpdateModel updater)
    {
        var viewModel = new TEditViewModel();
        if (await updater.TryUpdateModelAsync(viewModel, Prefix))
        {
            await UpdateActivityAsync(viewModel, activity);
        }

        return Edit(activity);
    }
}
```

请注意，形状名称是从活动类型派生的，有效地实现了用于使用形状模板名称的命名约定。继续使用`NotifyTask`示例，我们现在需要创建以下Razor文件：<|im_end|>

- `NotifyTask.Fields.Design.cshtml`
- `NotifyTask.Fields.Thumbnail.cshtml`
- `NotifyTask.Fields.Edit.cshtml`

## Videos

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/n-O4WO6dVJk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/IcR-YpxKlGQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

















