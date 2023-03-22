# 角色 (`OrchardCore.Roles`)

启用 `OrchardCore.Roles` 模块将允许您管理用户角色。

## 预定义角色

Orchard Core 提供了以下预定义的权限原型：

| 名称 | 描述 |
| --- | --- |
| `Administrator` | 包含所有管理员用户。 |
| `Anonymous` | 包含所有未经身份验证的用户。 |
| `Authenticated` | 包含所有经过身份验证的用户。 |
| `Author` | 包含所有具有编写内容能力的用户。 |
| `Contributor` | 包含所有具有对内容进行贡献的能力的用户。 |
| `Editor` | 包含所有具有编辑内容能力的用户。 |
| `Moderator` | 包含所有具有审核内容能力的用户。 |

## 角色配置

角色可以通过管理仪表板中的角色菜单创建和配置，也可以通过配方步骤创建。请注意，功能的角色未预定义。它们是从预定义的权限原型创建的。

角色配置步骤示例：

```json
{
    "name": "roles",
    "Roles": [
        {
            "Name": "记者",
            "Permissions": [ "PublishContent", "EditContent" ]
        },
        {
            "Name": "订阅者",
            "Permissions": [ ]
        },
    ]
}
```

> 该文档由ChatGPT 4 翻译
