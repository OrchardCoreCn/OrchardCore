# 内容类型 (`OrchardCore.ContentTypes`)

## 视图组件

### `SelectContentTypes`

渲染一个编辑器以选择内容类型列表。  
它可以选择性地过滤特定原型的内容类型。  
编辑器将选择作为模型上的 `string[]` 返回。

#### 参数

| 参数 | 类型 | 描述 |
| --------- | ---- | ----------- |
| `selectedContentTypes` | `string[]` | 渲染编辑器时应标记为选定的内容类型列表。 |
| `htmlName` | `string` | 将结果绑定到的模型属性的名称。
| `stereotype` (可选) | `string` | 用于过滤可选择的内容类型列表的原型名称。 |

#### 示例

```csharp
@await Component.InvokeAsync("SelectContentTypes", new { selectedContentTypes = Model.ContainedContentTypes, htmlName = Html.NameFor(m => m.ContainedContentTypes) })
```

## 迁移

迁移类可用于更改内容类型定义，例如添加新的 __类型__，或配置其 __部分__ 和 __字段__。

### `IContentDefinitionManager`

此服务提供了一种修改内容类型定义的方法。从迁移类中，我们可以注入此接口的实例。

```csharp
public class Migrations : DataMigration
{
    IContentDefinitionManager _contentDefinitionManager;

    public Migrations(IContentDefinitionManager contentDefinitionManager)
    {
        _contentDefinitionManager = contentDefinitionManager;
    }

    public int Create()
    {
        // 此代码将在启用功能时运行

        return 1;
    }
}
```

### 创建新的内容类型

以下示例创建名为 `Product` 的新内容类型。

```csharp
_contentDefinitionManager.AlterTypeDefinition("Product");
```

### 更改内容类型的元数据

要更改内容类型的特定属性，可以使用参数进行配置：

```csharp
_contentDefinitionManager.AlterTypeDefinition("Product", type => type
    // 此类型的内容项可以有草稿
    .Draftable()
    // 此类型的内容项版本已保存
    .Versionable()
    // 此内容类型出现在新菜单部分中
    .Creatable()
    // 可以将权限专门应用于此类型的实例
    .Securable()
);
```

### 将内容部分添加到类型

以下示例将 `TitlePart` 内容部分添加到 `Product` 类型。

```csharp
_contentDefinitionManager.AlterTypeDefinition("Product", type => type
    .WithPart("TitlePart")
);
```

每个部分也可以在类型的上下文中进行配置。例如，`AutoroutePart` 需要一个 __Liquid__ 模板作为其模式以生成自定义路由。它在此部分的自定义设置中定义。

```csharp
_contentDefinitionManager.AlterTypeDefinition("Product", type => type
    .WithPart("AutoroutePart", part => part
        // 在其他部分中设置位置
        .WithPosition("2")
        // 设置 AutoroutePart 上的所有设置
        .WithSettings(new AutoroutePartSettings { Pattern = "{{ ContentItem | display_text | slugify }}" })
    )
);
```

有关每种类型可以使用的所有设置的列表，请参阅它们各自的文档页面。

### 将内容字段添加到部分

字段不能直接附加到内容类型。要将字段添加到内容类型，请创建与类型名称相同的部分，并将字段添加到此部分中。

```csharp
_contentDefinitionManager.AlterTypeDefinition("Product", type => type
    .WithPart("Product")
);

_contentDefinitionManager.AlterPartDefinition("Product", part => part
    .WithField("Image", field => field
        .OfType("MediaField")
        .WithDisplayName("主图像")
    )
    .WithField("Price", field => field
        .OfType("NumericField")
        .WithDisplayName("价格")
    )
);
```

将字段添加到部分时，字段还可以具有自定义设置，例如定义编辑器的行为方式或验证规则。有关可能设置的列表，请参阅它们各自的文档页面。

### 从 CSharp 使用内容部分和字段

可以从上述类型定义中获取 Content Parts 和 Fields 的强类型版本。

!!! 警告
    这些类型可能会在 CMS 中进行修改。在代码中使用它们时，确保在开发周期之外不会修改这些类型非常重要。

首先，创建与类型定义匹配的部分：

```csharp
public class Product : ContentPart
{
    public MediaField Image { get; set; }
    public NumericField Price { get; set; }
}
```

然后，使用依赖项注入注册 ContentPart：

```csharp
using OrchardCore.ContentManagement;

...

public class Startup : StartupBase
{
    public override void ConfigureServices(IServiceCollection services)
    {
        services.AddContentPart<Product>();
    }
}
```

最后，以下是在控制器中将 Content Item 作为 Content Part 使用的示例。

```csharp
public class ProductController : Controller
{
    private readonly IOrchardHelper _orchardHelper;
    private readonly IContentManager _contentManager;

    public ProductController(IOrchardHelper orchardHelper, IContentManager contentManager)
    {
        _orchardHelper = orchardHelper;
        _contentManager = contentManager;
    }

    [HttpGet("/api/product/{productId}")]
    public async Task<ObjectResult> GetProductAsync(string productId)
    {
        var product = _orchardHelper.GetContentItemByIdAsync(productId);

        if (product == null) 
        {
            return NotFoundObjectResult();
        }

        var productPart = product.As<Product>();

        // 如果这些字段中的任何一个为 null，则会引发异常
        // 对于不需要的任何字段，应使用 null-conditional 运算符 (?)
        return new ObjectResult(new {
             Image = productPart.Image.Paths.FirstOrDefault(),
             Price = productPart.Price.Value,
        });
    }
    
    [HttpPost("/api/product/{productId}/price/{price}")]
    public async Task<ContentValidateResult> UpdateProductPriceAsync(string productId, int price)
    {
        //此调用仅获取已发布的内容项，这使得更新后的发布变得多余
        var product = _orchardHelper.GetContentItemByIdAsync(productId);

        if (product == null) 
        {
            return NotFoundObjectResult();
        }

        var productPart = product.As<Product>();
        productPart.Price.Value = price;
        
        product.Apply(productPart) //将修改后的部分应用于内容项
        
        await _contentManager.UpdateAsync(product); //更新将触发处理程序，这些处理程序可能会更改内容项。

        //验证将取消更改，如果产品无效。它在更新后触发，因为处理程序可能会更改对象。
        return await _contentManager.ValidateAsync(product);
    }
}
```

> 该文档由ChatGPT 4 翻译
