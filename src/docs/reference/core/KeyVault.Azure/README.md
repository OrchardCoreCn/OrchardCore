# Azure Key Vault (`OrchardCore.Azure.KeyVault`)

Azure Key Vault 配置提供程序从 Azure Key Vault 添加应用程序配置值，以保护应用程序使用的加密密钥和机密。它还包含 DefaultKeyVaultManager 类的自定义覆盖，该类从 Azure Key Vault 检索机密并将 --- 翻译为下划线 (_)，将 -- 翻译为冒号 (:)。在 Azure KeyVault 中，下划线和冒号都是非法字符。

示例：
Key Vault 输入："OrchardCore--OrchardCore---Shells---Database--ConnectionString"。
输出："OrchardCore:OrchardCore_Shells_Database:ConnectionString"。
请参阅 https://github.com/OrchardCMS/OrchardCore/issues/6359。

## 使用 Azure Key Vault 进行身份验证
默认情况下，Azure Key Vault 配置提供程序使用 [Azure Identity 库](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/identity/Azure.Identity/README.md) 用于 Azure SDK 中的 Azure Active Directory 令牌身份验证支持。此时，OrchardCore.Azure.KeyVault 仅支持 DefaultAzureCredential 设置，适用于大多数应用程序在 Azure 中运行的情况。

在调试或本地执行时，开发人员有多种选项可用于使用 Azure Key Vault 进行身份验证。要在 Visual Studio 中进行身份验证，请选择“工具”>“选项”菜单以启动“选项”对话框。然后导航到 Azure 服务身份验证选项以使用 Azure Active Directory 帐户进行登录。使用 Visual Studio Code 的开发人员可以使用 [Azure Account Extension]，通过 IDE 进行身份验证。

## 配置
此外，您需要指定 Azure Key Vault 的名称和可选的重新加载间隔（以秒为单位）。
```json
"OrchardCore_KeyVault_Azure": {
    "KeyVaultName": "", // 设置 Azure Key Vault 的名称。
    "ReloadInterval": "" // 可选，设置在轮询 Azure KeyVault 更改时等待的时间间隔。留空以禁用重新加载。
}
```

在 `Program.cs` 中，在 `CreateHostBuilder()` 的通用主机中添加 `AddOrchardCoreAzureKeyVault()`。

```csharp
using OrchardCore.Configuration.KeyVault.Extensions;

public class Program
{
    public static Task Main(string[] args)
        => BuildHost(args).RunAsync();

    public static IHost BuildHost(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .AddOrchardCoreAzureKeyVault()
            .ConfigureWebHostDefaults(webBuilder => webBuilder.UseStartup<Startup>())
            .Build();
}
