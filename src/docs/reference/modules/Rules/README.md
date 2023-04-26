# 规则 (`OrchardCore.Rules`)

启用 `OrchardCore.Rules` 模块可以让您实现基于条件的规则。

### 自定义条件

您可以为更复杂的场景创建自己的条件。

您需要实现 `OrchardCore.Rules.Abstractions` 包中找到的抽象。

- `Condition`
- `ConditionEvaluator`
- `ConditionDisplayDriver`
- 适当的视图以供您的条件显示驱动程序使用。

``` csharp
  services
    .AddScoped<IDisplayDriver<Condition>, BooleanConditionDisplayDriver>()
    .AddCondition<BooleanCondition, BooleanConditionEvaluator, ConditionFactory<BooleanCondition>>();
```

有关规则和条件的更多信息，请参见[层](../Layers/README.md)。

#### 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Iq6VbXZg0B0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
