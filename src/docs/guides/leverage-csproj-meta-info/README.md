# 利用您的CSPROJ元信息

## 动机

开箱即用，生成了一个`Manifest.cs`文件，包含了`ModuleAttribute`、`FeatureAttribute`、`ThemeAttribute`等，描述了关于您项目的_Orchard Core_元信息。然而，假设您想要连接可用的元属性到`CSPROJ`级别，比如诸如`$(MSBuildProjectName)`、`$(AssemblyVersion)`、`$(PackageTags)`等属性。

在本质上，我们想遵循类似于`InternalsVisibleTo`的模式，即。

```xml
<ItemGroup>
    <AssemblyAttribute Include="System.Runtime.CompilerServices.InternalsVisibleTo">
      <_Parameter1>Test.$(MSBuildProjectName)</_Parameter1>
    </AssemblyAttribute>
</ItemGroup>
```

原则相同，但在以前的版本中，属性构造函数对我们来说不可用。我们还决定提供有用的[`MSBuild`项目](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-items)作为更易于访问、自我说明的快捷输入。因此，虽然可以直接与构造函数交互，例如`InternalsVisibleTo`，但我们将其留给读者作为一项练习。相反，我们将重点放在[_项列表_](#msbuild-item-lists)上。

我们还有一个要求，即在构建目标之前增加我们的程序集版本，因此我们希望在传递到_Orchard Core_目标之前发生这种情况。有关可用的生成属性的更广泛细节，请参阅[Microsoft `MSBuild`文档](https://docs.microsoft.com/en-us/visualstudio/msbuild)。
## MSBuild项目列表

根据您的项目的不同，您可能会对几个项目感兴趣，比如_Module_、_Theme_ 和/或任何_Features_。我们在_Orchard Core_目标中还使用了一些属性。显然，一个项目不可能同时列举所有这些属性，但为了简洁起见，在此列举。需要提醒的是[一些注意事项](#一些注意事项)，但整体而言，这些是有用的属性。

```xml
<OrchardCoreFeatures Include="..."
                     Name="..."
                     Category="..."
                     Priority="..."
                     Description="..."
                     Dependencies="..."
                     DefaultTenant="..."
                     AlwaysEnabled="..."
                     EnabledByDependencyOnly="..." />
```

```xml
<OrchardCoreModules Include="..."
                    Name="..."
```xml
<OrchardCoreThemes Include="..."
                   Name="..."
                   Base="..."
                   Category="..."
                   Priority="..."
                   ModuleType="..."
                   Description="..."
                   Author="..."
                   Version="..."
                   Website="..."
                   Dependencies="..."
                   Tags="..."
                   DefaultTenant="..."
                   AlwaysEnabled="..."
                   EnabledByDependencyOnly="..." />
``` 

注意，这里的`ModuleType`，`Description`，`Author`，`Version`，`Website`，`Dependencies`，`Tags`，`DefaultTenant`，`AlwaysEnabled`，`EnabledByDependencyOnly`是添加在原有属性后的。这个XML元素是用于定义Orchard Core主题的，其中`Name`和`Base`属性是必须指定的。而其他属性是可选的，可以被用来提供主题的更多信息，比如主题的描述，作者，版本号，以及它所依赖的其他模块等等。该元素的属性与Orchard Core模块的属性类似。
XML 元素 `<DotNetNuke>` 定义了 DNN 模块、主题或功能的信息。其包含了以下各个属性：

|属性|类型|描述|
|-|-|-|
|`Include`|`string` <sup>1</sup>|必填，表示 `MSBuild` 支持将此项目列表作为一个项目。其中，`Identity` 用作属性 `Id`。|
|`Name`|`string`|可选，用于指定此模块或功能的名称。|
|`Base`|`string`|可选，指定此程序集所继承的基础主题。不适用于特性或模块。|
|`ModuleType`|`string`|可选，由开发人员提供的 `Type`；如果未提供，则默认值为 `"Module"` 或 `"Theme"`。这个属性不适用于特性，因为它是一个 _Module_ 基础类属性<sup>2</sup>。|
|`Category`|`string`|可选，为该特性定义一个类别。|

所有属性被描述如下表所示。

|属性|类型|描述|
|-|-|-|
|`Include`|`string` <sup>1</sup>|必填，表示 `MSBuild` 支持将此项目列表作为一个项目。其中，`Identity` 用作属性 `Id`。|
|`Name`|`string`|可选，用于指定此模块或功能的名称。|
|`Base`|`string`|可选，指定此程序集所继承的基础主题。不适用于特性或模块。|
|`ModuleType`|`string`|可选，由开发人员提供的 `Type`；如果未提供，则默认值为 `"Module"` 或 `"Theme"`。这个属性不适用于特性，因为它是一个 _Module_ 基础类属性<sup>2</sup>。|
|`Category`|`string`|可选，为该特性定义一个类别。|
|`Priority`|`int` <sup>1</sup>|可选的整数优先级，以 `string` 形式给出，默认为 `0 `；优先级较低优先。|
|`Description`|`string`|可选的描述性文本。|
|`Author`|`string`|可选的，由作者提供的标识。|
|`Version`|`string`|可选的、建议使用的、符合语义化版本的文本，默认为 `"0.0"`。|
|`Dependencies`|`list`|可选的，用分号分隔的 _Module Identifier_ 依赖列表。<sup>3</sup>|
|`Tags`|`list`|可选的，用分号分隔的标签列表。<sup>3</sup>|
|`DefaultTenant`|`bool` <sup>1</sup>|可选的，布尔值， `true|false` ，默认为 `false`。|
|`AlwaysEnabled`|`bool` <sup>1</sup>|可选的，布尔值， `true|false`，默认为 `false`。|
|`EnabledByDependencyOnly`|`bool` <sup>1</sup>|可选的，布尔值， `true|false`，默认为 `false`。|

<sup>[1] `MSBuild` 将所有元数据都传输为字符串， leaving authors to contend with either `string` or `object` type conversions i.e. either `int` or `bool`, which is fine for our purposes.</sup>
<br/><sup>[2] 依据上下文， `ModuleAttribute` 产生 `"Module"`, `ThemeAttribute` 默认产生 `"Theme"`。</sup>
<br/><sup>[3] 默认使用分号分隔，因为这也是 `CSPROJ` 的界定符。也支持空格或逗号分隔。</sup>

为了方便起见，您可以使用一个顶层项目列表指定所有您的组件 Orchard Core 属性。

```xml
<OrchardCoreAttributes Include="..."
                       Type="..."
                       ... />
```

|属性|类型|描述|
|-|-|-|
|`Type`|`string`|通常为`“feature”`、`“theme”`或`“module”`。除此以外都认为是_Module_。|

否则，`OrchardCoreAttributes`应包含所有所需`Type`相同的_属性_。

## 目标顺序依赖关系

提供了一些方便的[`MSBuild` _属性_](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-properties)，作者可以利用它们来适应其内部目标构建顺序。

|属性|默认值|描述|
|-|-|-|
|`OrchardCoreEmbeddingAfterTargets`|`AfterResolveReferences`|发生_Orchard Core_程序集嵌入后的这些目标。|
|`OrchardCoreEmbeddingBeforeTargets`|`GenerateAssemblyInfo`|_Orchard Core_程序集嵌入将在这些目标之前发生。|

我们不建议以任何方式更改默认值，而是根据需要追加自定义目标，例如。 

```xml
本文介绍了如何在Orchard Core项目中创建模块和主题的方法，并介绍了如何使用CSPROJ文件和属性组来定义项目和组件。此外，还介绍了一个有用的技巧，即可以通过注释或删除`Manifest.cs`文件来简化项目结构。在此过程中需要注意一些限制条件，如一个项目中只能有一个模块或主题，不能同时定义模块和主题等。
在本文中，我们描述了如何利用_Orcard Core_的`CSPROJ` _项目列表_和_属性_，以便丰富和充分发挥您的创作体验。

愉快的编程！


> 该文档由Chat-GPT 翻译
