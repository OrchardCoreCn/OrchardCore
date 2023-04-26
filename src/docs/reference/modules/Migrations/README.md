# Data Migrations (`OrchardCore.Data.Migration.DataMigration`)
# 数据迁移 (`OrchardCore.Data.Migration.DataMigration`)

Data Migration classes can be used to alter the content type definitions (like by adding new __types__, or configuring their __parts__ and __fields__), 
initializing recipes or creating indices.
数据迁移类可用于更改内容类型定义（例如添加新的__类型__或配置其__部分__和__字段__），初始化配方或创建索引。

There should be one Migrations file per module inheriting from __DataMigration__. However, if a module has more than one feature and multiple migrations per feature, each migration should be decorated with the Feature attribute i.e. `[Feature("OrchardCore.ContentFields.Indexing.SQL")]`
每个模块应该有一个继承自__DataMigration__的迁移文件。但是，如果一个模块有多个功能和多个迁移每个功能，每个迁移都应该用Feature属性装饰，即`[Feature("OrchardCore.ContentFields.Indexing.SQL")]`

Initial migration method should be named `public int Create` or `public Task<int> CreateAsync` and it should return a number (like  `return 1`).
Any subsequent migration should follow the convention `public int UpdateFromX` or `public Task<int> UpdateFromXAsync`, where __X__ is the number returned from the last migration method. Migrations are strictly chained:
eg. UpdateFrom4 will only be executed if the last executed migration returned the number 4. You should not revise these numbers after the fact because it could break migrations for other people.
初始迁移方法应命名为`public int Create`或`public Task<int> CreateAsync`，并且应返回一个数字（例如`return 1`）。任何后续迁移都应遵循约定`public int UpdateFromX`或`public Task<int> UpdateFromXAsync`，其中__X__是从上一个迁移方法返回的数字。迁移严格链接：
例如，如果上一个执行的迁移返回数字4，则只会执行UpdateFrom4。您不应在事后修改这些数字，因为它可能会破坏其他人的迁移。

Migrations are executed automatically on application start.
迁移会在应用程序启动时自动执行。

The following example showcases three different data migrations (recipe migration, creating a map index, creating content type and updating content type).
以下示例展示了三个不同的数据迁移（配方迁移，创建地图索引，创建内容类型和更新内容类型）。

```csharp
using System.Threading.Tasks;
using Members.Indexes;
using OrchardCore.ContentManagement.Metadata;
using OrchardCore.ContentManagement.Metadata.Settings;
using OrchardCore.Data.Migration;
using OrchardCore.Recipes.Services;
using YesSql.Sql;

namespace Members
{
    public class Migrations : DataMigration
    {
        private readonly IRecipeMigrator _recipeMigrator;
        private readonly IContentDefinitionManager _contentDefinitionManager;

        public Migrations(IRecipeMigrator recipeMigrator, IContentDefinitionManager contentDefinitionManager)
        {
            _recipeMigrator = recipeMigrator;
            _contentDefinitionManager = contentDefinitionManager;
        }

        public async Task<int> CreateAsync()
        {
            await _recipeMigrator.ExecuteAsync("init.recipe.json", this);

            return 1;
        }

        public int UpdateFrom1()
        {
            SchemaBuilder.CreateMapIndexTable<MemberIndex>(table =>
            {
                // 确保设置列长度，否则迁移将无法在所有数据库上工作
                table.Column<string>(nameof(MemberIndex.SocialSecurityNumber), column => column.WithLength(11))
                .Column<string>(nameof(MemberIndex.Name), column => column.WithLength(26))
                .Column<string>(nameof(MemberIndex.Surname), column => column.WithLength(26))
            });
            
            // 这将在sql表本身上创建索引，
            // 这将使按'SocialSecurityNumber'列获取更快
            SchemaBuilder.AlterIndexTable<MemberIndex>(table => table
                .CreateIndex("IDX_MemberIndex_SocialSecurityNumber",
                    "SocialSecurityNumber")
            );
            return 2;
        }

        public int UpdateFrom2()
        {
            _contentDefinitionManager.AlterTypeDefinition("Product", type => type
                // 此类型的内容项可以有草稿
                .Draftable()
                // 此类型的内容项版本可追踪
                .Versionable()
                // 此内容类型出现在“新建”菜单部分
                .Creatable()
                // 权限可以特别应用于此类型的实例
                .Securable()
            );
            return 3;
        }

        public int UpdateFrom3()
        {
            _contentDefinitionManager.AlterTypeDefinition("Product", type => type
                .WithPart("TitlePart")
            );
            return 4;
        }

    }
}
```

## Additional information
Please refer to separate sections for more details on data migrations:
## 附加信息
有关数据迁移的更多详细信息，请参见单独的部分：

- [Data Migration of Content Types](../ContentTypes/README.md#migrations)
- [Data Migration of Recipes](../Recipes/README.md#recipe-migrations)
- [内容类型的数据迁移](../ContentTypes/README.md#migrations)
- [配方的数据迁移](../Recipes/README.md#recipe-migrations)

> 该文档由ChatGPT 4 翻译
