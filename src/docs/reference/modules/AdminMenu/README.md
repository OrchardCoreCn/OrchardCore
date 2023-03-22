# 管理菜单 (`OrchardCore.AdminMenu`)

管理菜单模块提供了一种通过管理界面创建自定义管理菜单的方法。

## 基本概念

有两个基本概念：

1. **管理菜单**：一个管理节点树，其根位于管理菜单的第一级。可以有一个或多个。

2. **管理节点**：组成管理菜单的每个节点。管理节点可以包含其他管理节点。每个管理节点都会在TheAdmin菜单上呈现一个或多个菜单项。

这些菜单项的树与Orchard Core默认提供的标准管理菜单合并。在本文档中，当我们提到由Orchard Core默认提供的菜单时，我们使用术语**TheAdmin菜单**。

您可以禁用管理菜单，它将不会显示。

您可以禁用管理节点，它和它的后代都将不会显示。

## 如何创建管理菜单

1. 确保启用了管理菜单模块。

2. 转到“配置：管理菜单”。

3. 创建新的管理菜单，并开始添加管理节点。链接管理节点是最简单的类型，非常适合测试功能。

4. 随着您不断添加管理节点，您将看到它们自动呈现在TheAdmin菜单上。

## 提供的管理节点类型

在编写本文档时，Orchard Core默认提供了3种管理节点类型：

1. **链接管理节点**：它提供了一个简单的菜单项。用户可以添加文本、URL和Font Awesome图标类，以便当前管理主题在呈现菜单项时使用它们。目前，TheAdmin主题仅对一级菜单项使用该图标类。此链接是OrchardCore.AdminMenu模块本身提供的唯一链接。

2. **内容类型管理节点**：它提供了一个包含每个内容类型菜单项的菜单项列表。链接指向内容控制器中的索引操作。此节点类型由OrchardCore.Contents模块提供。

3. **列表管理节点**：它提供了指向包含列表部分的那些内容项的编辑页面的菜单项。例如，如果您有一个博客内容类型和几个博客内容项，它将为每个现有博客提供一个链接。此节点类型由OrchardCore.Lists模块提供。

请注意，这些节点中的每一个都可以有其他嵌套节点。嵌套是通过UI上的拖放完成的。

## 如何将管理菜单呈现为管理菜单项

### 在没有管理菜单模块的情况下如何工作

OrchardCore默认提供的管理菜单大体上是这样构建的：

1. NavigationManager检索实现INavigationProvider的所有类。有许多这样的类通过许多具有“AdminMenu.cs”文件名的模块。

2. 在每个AdminMenu上，NavigationManager调用BuildNavigationAsync方法，将一个构建器传递给它。构建器是每个AdminMenu都可以向其中添加自己的menuItems的对象。

3. 一旦所有AdminMenu类都完成了将自己的菜单项添加到构建器的操作，NavigationManager使用构建器上的信息来“呈现”完整的菜单。

### 启用管理菜单时发生了什么

1. 管理菜单模块声明了自己的INavigationProvider，因此NavigationManager也会调用它。该INavigationProvider的名称是AdminMenuNavigationProvidersCoordinator。

2. 协调器检索存储在数据库中的所有管理菜单，并为每个管理菜单调用BuildTreeAsync方法，在其中每个节点递归地向构建器添加自己的菜单项。

## 部署计划步骤和配方步骤

该模块提供了一个管理菜单部署步骤。因此，管理员用户可以花费一些时间配置自定义管理菜单，将其添加到部署计划中，导出json文件，并在设置配方中使用生成的json。这样，使用该配方构建的站点将具有用户准备的管理菜单。

## 权限

该模块有两种与之关联的权限：

1. 管理管理菜单。它是关于能否从管理中创建、编辑和删除管理菜单的。

2. 查看管理菜单。它使显示或隐藏每个角色的管理菜单成为可能。您可以从标准的编辑角色页面上执行此操作。

## 开发自定义管理节点类型

任何模块都可以添加自己的自定义管理节点类型，以便用户可以使用它们构建自定义管理菜单。

通常，您遵循的步骤是：

1. 添加一个从`AdminNode`继承的类。在此类中添加您想要的特定属性，以用于您的节点类型。这是将进入数据库的信息。

2. 添加一个驱动程序来处理在管理中显示和编辑您的管理节点。这不会处理管理菜单的实际呈现。驱动程序仅涉及创建和编辑管理菜单所需的视图。

3. 可选地，您可以实现ViewModel以在编辑视图和驱动程序之间移动信息。

4. 添加一个实现IAdminNodeNavigationBuilder的类。当渲染菜单时，AdminMenuNavigationProvidersCoordinator类将调用其BuildNavigationAsync()方法。

5. 根据您的节点类型创建创建和编辑管理节点所需的视图。

按照惯例，您应将所有这些非视图类存储在“AdminNodes”文件夹中。这是可选的。

按照惯例，您必须将视图存储在“Views”文件夹内的“Items”文件夹中。这是必需的。

不要忘记在Startup类中注册相应的类。

### 基于LinkAdminNode的代码片段

这是LinkAdminNode.cs

```csharp

    public class LinkAdminNode : AdminNode
    {
        [Required]
        public string LinkText { get; set; }

        public string LinkUrl { get; set; }

        public string IconClass { get; set; } = "far fa-circle";
    }
```

这是LinkAdminNodeBuilder如何构建链接。

该类负责：

*将数据库中的管理节点信息转换为menuItems，并将它们添加到全局构建器中。

*在每个管理节点的子项上调用相同的BuildNavigationAsync()方法。

此模式确保在处理完整个树之后，将处理完整个树。

```csharp
        public Task BuildNavigationAsync(MenuItem menuItem, 
                NavigationBuilder builder, 
                IEnumerable<IAdminNodeNavigationBuilder> treeNodeBuilders)
        {
            //将接收到的项转换为我们正在处理的具体管理节点类型。
            var ltn = menuItem as LinkAdminNode;

            if ((ltn == null) ||( !ltn.Enabled))
            {
                return Task.CompletedTask;
            }

            //这是将菜单项添加到构建器的标准Orchard Core方式
            builder.Add(new LocalizedString(ltn.LinkText, ltn.LinkText), async itemBuilder => {

                //添加实际链接
                itemBuilder.Url(ltn.LinkUrl);
                AddIconPickerClassToLink(ltn.IconClass, itemBuilder);

                //让每个子管理节点在此MenuItem内部构建自己
                foreach (var childTreeNode in menuItem.Items)
                {
                    try
                    {
                        var treeBuilder = treeNodeBuilders
                                .Where(x => x.Name == childTreeNode.GetType().Name)
                                .FirstOrDefault();

                        await treeBuilder.BuildNavigationAsync(childTreeNode,itemBuilder,treeNodeBuilders);
                    }
                    catch (Exception e)
                    {
                        _logger.LogError(e,
                            "在构建'{MenuItem}'子菜单项时发生异常。",
                            childTreeNode.GetType().Name);
                    }
                }
            });

            return Task.CompletedTask;
        }
```

## CREDITS

### Font Awesome Icon Picker

<https://farbelous.github.io/fontawesome-iconpicker/>

最初由(c)2016 Javi Aguilar编写

根据MIT许可证许可
<https://github.com/farbelous/fontawesome-iconpicker/blob/master/LICENSE>

### jQuery UI Nested Sortable

v 2.1a / 2016-02-04
<https://github.com/ilikenwf/nestedSortable>

依赖于：
jquery.ui.sortable.js 1.10+

版权所有(c)2010-2016 Manuele J Sarfatti和贡献者
根据MIT许可证许可
<http://www.opensource.org/licenses/mit-license.php>

> 该文档由ChatGPT 4 翻译
