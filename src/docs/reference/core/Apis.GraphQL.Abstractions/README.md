# GraphQL

## 查询

查询由三个部分组成，即“类型”、“参数”和“返回值”，例如：

```json
{
  blog {
    displayText
  }
}
```

在此示例中，“blog”是类型，“displayText”是返回值。您可以扩展此内容以添加参数。参数用于过滤查询，例如：

```json
{
  blog(where: {contentItemId: "4k5df0kadp9asy1n2ejzs1rz4r"}) {
    displayText
  }
}
```

在这里，我们可以看到查询正在使用参数“contentItemId”进行过滤。

### 定义查询类型

让我们看看如何将类型连接到GraphQL模式中；

首先：让我们从一个简单的C#部分开始

```csharp
public class AutoroutePart : ContentPart
{
    public string Path { get; set; }
    public bool SetHomepage { get; set; }
}
```

这是附加到您的内容项的部分。GraphQL不知道这是什么，因此我们现在需要创建此类的GraphQL表示形式；

```csharp
public class AutorouteQueryObjectType : ObjectGraphType<AutoroutePart>
{
    public AutorouteQueryObjectType()
    {
        Name = "AutoroutePart";

        // 映射要公开的字段
        Field(x => x.Path);
    }
}
```

这里有两件事情要做；

1. 继承自“ObjectGraphType”。GraphQL理解此类型。
2. Field(x => x.Path);。这告诉＃1类您要公开的字段。

最后一部分是告诉Orchard子系统有关您的新类型的信息，一旦完成，GraphQL子系统将从其依赖树中获取您的新对象。要执行此操作，请在Startup类中简单注册它；

```csharp
[RequireFeatures("OrchardCore.Apis.GraphQL")]
public class Startup : StartupBase
{
    public override void ConfigureServices(IServiceCollection services)
    {
        // 我省略了AutoroutePart的注册，因为我们希望它已经注册
        services.AddObjectGraphType<AutoroutePart, AutorouteQueryObjectType>();
    }
}
```

就这样，您的部分现在将在GraphQL中公开...只需转到查询资源管理器并查看即可。神奇。

### 定义查询过滤器类型

现在您有了大量的数据返回，下一步要做的是能够过滤所述数据。

我们从步骤＃1开始执行类似的过程，因此此时我将假设您已经实现了步骤＃1。

我们要在这里介绍的是；

1. 实现输入类型。
2. 在Startup类中注册它。
3. 实现过滤器。

因此，让我们开始。输入类型类似于查询类型，在这里我们要告诉GraphQL模式我们接受此输入。

```csharp
public class AutorouteInputObjectType : InputObjectGraphType<AutoroutePart>
{
    public AutorouteInputObjectType()
    {
        Name = "AutoroutePartInput";

        Field(x => x.Path, nullable: true).Description("要过滤的内容项的路径");
    }
}
```


像下面这样更新Startup类。

```csharp
[RequireFeatures("OrchardCore.Apis.GraphQL")]
public class Startup : StartupBase
{
    public override void ConfigureServices(IServiceCollection services)
    {
        // 我省略了AutoroutePart的注册，因为我们希望它已经注册
        services.AddObjectGraphType<AutoroutePart, AutorouteQueryObjectType>();
        services.AddInputObjectGraphType<AutoroutePart, AutorouteInputObjectType>();
    }
}
```

从此类中获取的主要内容是所有输入类型都必须继承自InputObjectGraphType。

当注册输入部分时，它将该部分添加为父查询，例如此实例中的autoroutePart，如下所示；

```json
{
  blog(autoroutePart: { path: "somewhere" }) {
    displayText
  }
}
```

接下来，我们要实现一个过滤器。过滤器从我们刚刚构建的类和上面的示例中获取输入，并对传递给它的对象执行实际过滤器。

```csharp
public class AutoroutePartGraphQLFilter : GraphQLFilter<ContentItem>
{
    public override Task<IQuery<ContentItem>> PreQueryAsync(IQuery<ContentItem> query, ResolveFieldContext context)
    {
        if (!context.HasArgument("autoroutePart"))
        {
            return Task.FromResult(query);
        }

        var part = context.GetArgument<AutoroutePart>("autoroutePart");

        if (part == null)
        {
            return Task.FromResult(query);
        }

        var autorouteQuery = query.With<AutoroutePartIndex>();

        if (!string.IsNullOrWhiteSpace(part.Path))
        {
            return Task.FromResult(autorouteQuery.Where(index => index.Path == part.Path).All());
        }

        return Task.FromResult(query);
    }
}
```

我们注意到的第一件事是

> context.GetArgument<AutoroutePart>("autoroutePart");

如上所示，我们有一个autoroutePart参数，这是在注册输入类型时注册的。从那里，我们可以反序列化并执行查询；

```json
{
  blog(autoroutePart: { path: "somewhere" }) {
    displayText
  }
}
```

完成。

## 查询相关内容项

内容项的一个特点是它们可以与其他内容项相关联。

假设我们有以下内容类型：电影（具有名称和发布年份作为文本字段）和人员，其中包含FavoriteMovies字段（Movie的内容选择器字段）。

### 获取相关内容项GraphQL查询

现在，如果我们想要获取Person项目的Favorite Movies，查询将抛出错误：

```json
{
  person {
    name
    favoriteMovies {
      contentItems {
        name
        releaseYear
      }
    }
  }
}
```

错误将抱怨“name”和“releaseYear”不是内容项的字段。

错误提示的“inline fragment”是一种构造，用于告诉查询解析器它应该如何处理这些通用项，并将它们视为“Movie”类型而不是通用的“Content Item”。

**注意**“... on Movie”片段，它告诉GraphQL解析器将发现的对象视为“Movie”。

以下查询为我们提供了所需的结果：

```json
{
  person {
    name
    favoriteMovies {
      contentItems {
        ... on Movie {
          name
          releaseYear
        }
      }
    }
  }
}
```

## 更多信息

有关GraphQL的更多信息，您可以访问以下链接：

- <https://graphql.org/learn/>
- <https://graphql-dotnet.github.io/docs/getting-started/introduction>
