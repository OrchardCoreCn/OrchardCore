# SQL索引

## 内容项索引

以下是您可以查询的SQL表及其列。

### **ContentItemIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| `ModifiedUtc` | `datetime` | `false` | `false` |
| `PublishedUtc` | `datetime` | `false` | `false` |
| `CreatedUtc` | `datetime` | `false` | `false` |
| `Owner` | `nvarchar(255)` | `false` | `false` |
| `Author` | `nvarchar(255)` | `false` | `false` |
| `DisplayText` | `nvarchar(255)` | `false` | `false` |

### **LocalizedContentItemIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **LocalizationSet** | **nvarchar** | **false** | **false** |
| **Culture** | **nvarchar** | **false** | **false** |

## 内容字段索引

`OrchardCore.ContentFields.Indexing.SQL` 模块提供了内容字段的数据库索引。

* 请注意，所列出的类型是 SQL Server 数据类型。

    *SQLite 在文本字段上没有长度限制。*

## 可用的内容字段表

### **BooleanFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Boolean** | **bit** | **false** | **false** |

### **ContentPickerFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **SelectedContentItemId** | **nvarchar(26)** | **false** | **false** |

### **DateFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Date** | **datetime** | **false** | **false** |

### **DateTimeFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **DateTime** | **datetime** | **false** | **false** |

### **HtmlFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Html** | **nvarchar(max)** | **false** | **false** |

### **LinkFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Url** | **nvarchar(766)** | **false** | **false** |
| **BigUrl** | **nvarchar(max)** | **false** | **false** |
| **Text** | **nvarchar(766)** | **false** | **false** |
| **BigText** | **nvarchar(max)** | **false** | **false** |

### **MultiTextFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `Int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Value** | **nvarchar(766)** | **false** | **false** |
| **BigValue** | **nvarchar(max)** | **false** | **false** |

### **NumericFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Numeric** | **decimal(19,5)** | **false** | **false** |

### **TextFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `Int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Text** | **nvarchar(766)** | **false** | **false** |
| **BigText** | **nvarchar(max)** | **false** | **false** |

### **TimeFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `Int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **Time** | **datetime** | **false** | **false** |

### **UserPickerFieldIndex**

| 名称 | 类型 | 非空 | 主键 |
| --- | --- | --- | --- |
| `Id` | `Int` | `true` | `true` |
| `DocumentId` | `int` | `false` | `false` |
| `ContentItemId` | `nvarchar(26)` | `false` | `false` |
| `ContentItemVersionId` | `nvarchar(26)` | `false` | `false` |
| `ContentType` | `nvarchar(255)` | `false` | `false` |
| `ContentPart` | `nvarchar(255)` | `false` | `false` |
| `ContentField` | `nvarchar(255)` | `false` | `false` |
| `Published` | `bit` | `false` | `false` |
| `Latest` | `bit` | `false` | `false` |
| **SelectedUserId** | **string** | **false** | **false** |

## 用法

请查看每个索引表，以查看可以查询哪些字段。以下示例仅适用于 TextFieldIndex。

从类中。

```csharp
using OrchardCore.ContentManagement;
using OrchardCore.ContentFields.Indexing

public class MyClass(){
    private readonly ISession _session;

        public MyClass(ISession session)
        {
            _session = session;
        }

        public async Task<IEnumerable<ContentItem>> GetTextFieldIndexRecords(string contentType, string contentField){
            return await _session.Query<ContentItem, TextFieldIndex>(x => x.ContentType == contentType && x.ContentField == contentField).ListAsync();
        }
}
```

从 Razor 模板：

```html
@using OrchardCore.ContentManagement
@using OrchardCore.ContentFields.Indexing
@inject ISession Session

@{
    var contentItems = await Session.Query<ContentItem, TextFieldIndex>(x => x.ContentType == "Acme" && x.ContentField == "Test").ListAsync();
}
```

从 Liquid，您需要先在 Orchard Core 中创建一个 SQL 查询以检索这些记录。将其命名为“所有国家”以进行当前示例，并且**不要选择**查询选项“返回文档”：

```SQL
SELECT * FROM TextFieldIndex
WHERE ContentType = 'Acme' AND ContentField = 'Country'
```

在我们的 Liquid 模板中，我们现在将检索这些记录。

```liquid
{% assign allCountries = Queries.AllCountries | query %}
{% for country in allCountries %}
{{ country.Text }}
{% endfor %}
```

请注意，日期时间存储为 UTC，因此需要使用当前请求区域设置进行转换。
> 该文档由ChatGPT 4 翻译
