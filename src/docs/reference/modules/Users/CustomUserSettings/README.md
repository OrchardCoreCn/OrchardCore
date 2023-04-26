# 自定义用户设置 (`OrchardCore.User.CustomUserSettings`)

自定义用户设置允许站点管理员为给定用户创建自定义属性集。

## 创建自定义用户设置

自定义用户设置按部分组织。每个部分由具有`CustomUserSettings`构造的内容类型表示。
创建此类部分时，请记住禁用`Creatable`，`Listable`，`Draftable`和`Securable`元数据，因为它们不适用。

!!! 警告
    不要将任何现有内容类型标记为此`CustomUserSettings`构造，因为这将破坏此类型的现有内容项。

然后，自定义用户设置由部分和字段组成，就像任何其他内容类型一样。
创建后，打开设置菜单项，每个部分都应出现在模块提供的部分旁边。

## 用法

### Liquid

在从数据库加载用户时，可以使用自定义用户设置。
```liquid 
{% assign user = User | user_id | users_by_id %}
{{user.Properties}}
```
每个部分都可以使用其名称。

例如，对于名为`UserProfile`的自定义设置部分，具有名为`FirstName`的`TextField`将使用`{{ user.Properties.UserProfile.UserProfile.FirstName.Text }}`访问。

### 放置

默认情况下，每个自定义用户设置内容类型都放置在选项卡中。

要调整放置位置，例如将设置移出选项卡，请使用`CustomUserSettings-PartDefinitionName`的`Differentiator`。

``` json
{
  "CustomUserSettings": [
    {
      "place": "Content:10#Content",
      "differentiator": "CustomUserSettings-UserProfile" 
    }
  ]
}
```

## 用户部分显示驱动程序

您还可以通过实现`SectionDisplayDriver<User，UserProfile>`来扩展`User`属性，其中`User`是要编辑的实体类型，
而`UserProfile`是要将`User`实体扩展的属性。

## 视频

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/_ff79hm5PAc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

> 该文档由ChatGPT 4 翻译
