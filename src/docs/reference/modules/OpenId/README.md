# OpenID (`OrchardCore.OpenId`)

## OpenID Connect 模块

`OrchardCore.OpenId` 提供以下功能：

- 核心组件
- 授权服务器
- 管理界面
- 令牌验证
- OIDC 客户端

## 核心组件

注册 OpenID 模块使用的核心组件。

## 管理界面

允许添加、编辑和删除已注册的应用程序。

## 授权服务器

使用 OpenID Connect/OAuth 2.0 标准启用外部应用程序的身份验证。  
它基于 [`OpenIddict`](https://github.com/openiddict/openiddict-core) 库，允许 Orchard Core 充当身份提供者，支持令牌身份验证而无需外部身份提供者。  

- Orchard Core 也可以用作集中用户访问权限的身份提供者，用于外部应用程序。
- Orchard Core 服务。
  - 授权服务器功能维护其自己的私有 JWT/验证处理程序实例，用于 userinfo API 端点。这样，您就不必为当前租户启用令牌验证功能。

支持的流程：[code/implicit/hybrid flows](http://openid.net/specs/openid-connect-core-1_0.html) 和 [client credentials/resource owner password grants](https://tools.ietf.org/html/rfc6749)。

### 配置

可以通过管理仪表板中的 _OpenID Connect_ 设置菜单和配方步骤来设置配置。

可用设置如下：

- 令牌格式：
  - 数据保护：默认情况下启用此格式 - 使用非标准不透明令牌，由 ASP.NET Core 数据保护堆栈加密。
  - Json Web Token：此格式使用签名的 JWT 标准令牌。默认情况下，令牌已加密，但可以关闭访问令牌加密，以允许第三方资源服务器使用 Orchard OpenID 服务器生成的 JWT 令牌。
- 授权机构：Orchard 用于充当身份服务器的 URL。
- 签名证书存储位置：CurrentUser/LocalMachine <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storelocation(v=vs.110).aspx>
- 签名证书存储名称：AddressBook/AuthRootCertificateAuthority/Disallowed/My/Root/TrustedPeople/TrustedPublisher <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storename(v=vs.110).aspx>
- 加密证书指纹：签名证书的指纹（建议不要使用用于 SSL 的相同证书）。
- 加密证书存储位置：CurrentUser/LocalMachine <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storelocation(v=vs.110).aspx>
- 加密证书存储名称：AddressBook/AuthRootCertificateAuthority/Disallowed/My/Root/TrustedPeople/TrustedPublisher <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storename(v=vs.110).aspx>
- 加密证书指纹：加密证书的指纹（建议不要使用用于 SSL 的相同证书）。
- 启用令牌端点。
- 启用授权端点。
- 启用注销端点。
- 启用用户信息端点。
- 允许密码流：需要启用令牌端点。更多信息请参见 <https://tools.ietf.org/html/rfc6749#section-1.3.3>
- 允许客户端凭据流：需要启用令牌端点。更多信息请参见 <https://tools.ietf.org/html/rfc6749#section-1.3.4>
- 允许授权代码流：需要启用授权和令牌端点。更多信息请参见 <http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>
- 允许隐式流：需要启用授权端点。更多信息请参见 <http://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth>
- 允许刷新令牌流：允许使用刷新令牌刷新访问令牌。它可以与密码流、授权代码流和混合流组合使用。更多信息请参见 <http://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens>
- 要求代码交换的证明密钥：全局设置，将 PKCE 应用于所有已注册的客户端，无论应用程序设置页面中是否设置了“要求 PKCE”标志。

OpenID Connect 设置配方步骤示例：

```json
{
      "name": "OpenIdServerSettings",
      "TestingModeEnabled": false,
      "AccessTokenFormat": "JsonWebToken", // JsonWebToken 或 DataProtection
      "Authority": "https://www.orchardproject.net",
      "SigningCertificateStoreLocation": "LocalMachine", //更多信息：https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storelocation(v=vs.110).aspx
      "SigningCertificateStoreName": "My", //更多信息：https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storename(v=vs.110).aspx
      "SigningCertificateThumbprint": "27CCA66EF38EF46CD9022431FB1FF0F2DF5CA1D7",
      "EncryptionCertificateStoreLocation": "LocalMachine",
      "EncryptionCertificateStoreName": "My",
      "EncryptionCertificateThumbprint": "BC34460ABEA2D576EA68E8FFCFEEB3F45C94FB0F",
      "EnableTokenEndpoint": true,
      "EnableAuthorizationEndpoint": false,
      "EnableLogoutEndpoint": true,
      "EnableUserInfoEndpoint": true,
      "AllowPasswordFlow": true,
      "AllowClientCredentialsFlow": false,
      "AllowAuthorizationCodeFlow": false,
      "AllowRefreshTokenFlow": false,
      "AllowImplicitFlow": false,
      "RequireProofKeyForCodeExchange" : false
}
```

### 客户端 OpenID Connect 应用程序配置

OpenID Connect 应用程序可以通过管理仪表板中的 OpenID Connect 应用程序菜单（通过管理界面功能）和配方步骤来设置。

OpenID Connect 应用程序需要以下配置。

- Id：唯一标识符。
- Client Id：应用程序的客户端标识符。客户端在请求有效令牌时必须提供它。
- 显示名称：与当前应用程序关联的显示名称。
- 类型：有两个选项：
  - 机密：机密应用程序在与令牌和撤销端点通信时必须发送其客户端密钥。这保证只有合法客户端才能交换授权代码或获取刷新令牌。
  - 公共：公共应用程序在其通信中不使用客户端密钥。
- 客户端密钥：客户端密钥是与应用程序关联的密码。当应用程序配置为机密时，将需要它。
- 流程：如果通用 OpenID Connect 设置允许此流程，则应用程序也可以启用此流程。
  - 允许密码流：需要启用令牌端点。更多信息请参见 <https://tools.ietf.org/html/rfc6749#section-1.3.3>
  - 允许客户端凭据流：需要启用令牌端点。更多信息请参见 <https://tools.ietf.org/html/rfc6749#section-1.3.4>
  - 允许授权代码流：需要启用授权和令牌端点。更多信息请参见 <http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>
  - 允许隐式流：需要启用授权端点。更多信息请参见 <http://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth>
  - 允许刷新令牌流：允许使用刷新令牌刷新访问令牌。它可以与密码流、授权代码流和混合流结合使用。更多信息请参见 <http://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens>
- 角色名称规范化：仅在启用客户端凭据流时需要此配置。它确定在使用该流进行身份验证时分配给应用程序的角色。
- 重定向选项：仅在需要隐式流、授权代码流或允许混合流时才需要这些选项。
- 注销重定向 URI：注销回调 URL。
- 重定向 URI：回调 URL。
- 跳过同意：设置用户在登录后是否必须完成同意表单。
- 高级参数：允许设置可以与授权请求一起发送的其他参数。注意：默认参数从上面的选项设置。
- 要求 PKCE：为注册应用程序应用 PKCE。确保使用的客户端库支持 PKCE。

OpenID Connect 应用程序配方步骤示例：

```json
{
      "name": "openidapplication",
      "ClientId": "openidtest",
      "DisplayName": "Open Id Test",
      "Type": "Confidential",
      "ClientSecret": "MyPassword",
      "EnableTokenEndpoint": true,
      "EnableAuthorizationEndpoint": false,
      "EnableLogoutEndpoint": true,
      "EnableUserInfoEndpoint": true,
      "AllowPasswordFlow": true,
      "AllowClientCredentialsFlow": false,
      "AllowAuthorizationCodeFlow": false,
      "AllowRefreshTokenFlow": false,
      "AllowImplicitFlow": false,
      "RequireProofKeyForCodeExchange": false
}
```

### OpenID Connect 范围配置

可以通过管理仪表板中的 OpenID Connect 范围菜单（通过管理界面功能）和配方步骤来设置范围。

OpenID Connect 范围需要以下配置。

| 属性 | 描述 |
| -------- | ----------- |
| 名称 | 范围的唯一名称。 |
| 显示名称 | 与当前范围关联的显示名称。 |
| 描述 | 描述此范围在系统中的使用方式。 |
| 租户 | 基于租户名称构建受众。 |
| 其他资源 | 基于提供的空格分隔字符串构建受众。 |

OpenID Connect 范围配方步骤示例：

```json
    {
      "name": "OpenIdScope",
      "Description": "A scope to provide audience for remote clients",
      "DisplayName": "External Audience Scope",
      "ScopeName": "custom_scope",
      "Resources": "my_recipient"
    }
```

### 配置证书

#### Windows / IIS

Windows 和/或 IIS 上有多个工具可用于生成签名证书，例如：

- IIS 服务器管理器（提供有限控制权）
    1. 服务器证书
    2. 创建自签名证书
- PowerShell（提供完全控制权）
    1. `New-SelfSignedCertificate`，例如：

```powershell
# 参见 https://technet.microsoft.com/en-us/itpro/powershell/windows/pkiclient/new-selfsignedcertificate

New-SelfSignedCertificate `
    -Subject "connect.example.com" `
    -FriendlyName "Example.com Signing Certificate" `
    -CertStoreLocation "cert:\LocalMachine\My" `
    -KeySpec Signature `
    -KeyUsage DigitalSignature `
    -KeyUsageProperty Sign `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") `
    -KeyExportPolicy NonExportable `
    -KeyAlgorithm RSA `
    -KeyLength 4096 `
    -HashAlgorithm SHA256 `
    -NotAfter (Get-Date).AddDays(825) `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
```

**必须以管理员身份运行此片段。**它生成一个 4096 位签名证书，将其存储在机器存储中，并返回证书的指纹，您需要在 OpenID Connect 设置配方或通过 PowerShell 导出证书时使用。_您应根据自己的要求更新此示例！_

在多节点环境中，考虑使用 `-KeyExportPolicy Exportable` 创建证书，然后将证书（PFX）导出到安全位置，使用 MMC 证书快照或 PowerShell `Export-PfxCertificate`，然后在每个节点上导入证书作为不可导出的，这是使用 `Import-PfxCertificate` 时的默认设置。例如：

```powershell
# 参见 https://technet.microsoft.com/en-us/itpro/powershell/windows/pkiclient/export-pfxcertificate
# 在生成证书的计算机上运行此命令：

$mypwd = ConvertTo-SecureString -String "MySecretPassword123" -Force -AsPlainText

Export-PfxCertificate -FilePath C:\securelocation\connect.example.com.pfx cert:\localMachine\my\thumbprintfromnewselfsignedcertificate -Password $mypwd

# 参见 https://technet.microsoft.com/en-us/itpro/powershell/windows/pkiclient/import-pfxcertificate
# 在目标节点上运行此命令：

$mypwd = ConvertTo-SecureString -String "MySecretPassword123" -Force -AsPlainText

Import-PfxCertificate -FilePath C:\securelocation\connect.example.com.pfx cert:\localMachine\my -Password $mypwd
```

**重要提示：**为了使 `OrchardCore.OpenId` 模块使用证书的密钥进行签名，它需要对存储中的证书具有 `Read` 访问权限。这可以通过各种方式授予，例如：

- `MMC.exe`
    1. 为计算机帐户添加“证书”快照
    2. 右键单击相关证书，选择“所有任务”、“管理私钥”
    3. 添加相关标识（例如 IIS AppPool\PoolName）
        - 添加
        - 高级
        - 位置：选择 iis 服务器机器名称
        - 查找
        - 搜索结果：选择您的 iisServerMachineName\IIS_IUSRS（只是一个示例）
        - 确定
    4. 在权限下检查允许读取
- `WinHttpCertCfg.exe`（授予完全控制权）
    1. 例如：`winhttpcertcfg -g -c LOCAL_MACHINE\My -s connect.example.com -a AppPoolIdentityName` <https://msdn.microsoft.com/en-us/library/windows/desktop/aa384088(v=vs.85).aspx>

### 在 Azure 中使用证书

要在 Azure 托管的站点上使用证书。

1. 将证书上传到站点 Azure 门户页面的“TLS/SSL 设置”页面。
2. 在 Azure 站点设置页面中添加一个新条目，其中包含以下内容：
    - Key：WEBSITE_LOAD_CERTIFICATES
    - Value：[证书的 Thumbprint]
3. 在“CurrentUser”>“My”证书存储中选择证书。

## 令牌验证

- 验证 Orchard OpenID 服务器颁发的令牌
  - 配置验证功能以透明地使用启用授权服务器功能的另一个租户的服务器配置。
- 通过支持 JWT 和 OpenID Connect 发现的远程服务器验证令牌。

令牌验证需要以下配置。

| 属性 | 描述 |
| -------- | ----------- |
| 授权服务器租户 | 运行 OpenID Connect 服务器的租户。如果未选择任何租户，则必须提供以下属性。 |
| Authority | 颁发令牌的远程 OpenID Connect 服务器的地址。 |
| Audience | 定义必须检查的令牌的预期接收者。 |

令牌验证设置示例：

```json
    {
      "name": "OpenIdValidationSettings",
      "Audience": "my_recipient",
      "Authority": "https://idp.domain.com"
    }
```

## OIDC 客户端

从外部 OpenID Connect 身份提供程序验证用户。
如果站点允许注册新用户，则会链接本地用户并链接外部登录。
如果收到“电子邮件”声明并找到本地用户，则在身份验证后将外部登录链接到该帐户。

### OpenId 配置

可以通过管理仪表板中的 OpenID Connect 设置菜单（通过管理界面功能）和配方步骤来设置配置。

可用设置为：

- 显示名称：IdP 的显示名称。它显示在登录表单中。
- Authority：进行 OpenIdConnect 调用时要使用的 Authority。
- ClientId：查询的 `client_id` 部分。
- CallbackPath：用户代理在从标识提供程序注销后将返回的应用程序基本路径内的请求路径。请参见 <http://openid.net/specs/openid-connect-session-1_0.html#RedirectionAfterLogout> 中的 `post_logout_redirect_uri`
- SignedOut CallbackPath：注销回调端点。默认为 `/signout-callback-oidc`。
- SignedOut Redirect Uri：应用程序从标识提供程序注销后，用户代理将重定向到的 URI。在调用 `SignedOutCallbackPath` 后将发生重定向。
- Scopes：除 openid 和 profile 之外的额外范围。
- Response Mode：配置响应模式，请参见：<http://openid.net/specs/openid-connect-core-1_0.html#ImplicitAuthResponse>。如果仅允许片段或查询，则只允许代码身份验证流。
- 支持的流：选择 OIDC 流之一：
  - 代码身份验证流（请参见：<http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>）
  - 混合身份验证流（请参见：<http://openid.net/specs/openid-connect-core-1_0.html#HybridAuthRequest>）
    - 使用 `code id_token` 响应类型（示例：<http://openid.net/specs/openid-connect-core-1_0.html#code-id_token-tokenExample>）
    - 使用 `code id_token token` 响应类型（示例：<http://openid.net/specs/openid-connect-core-1_0.html#code-id_token-tokenExample>）
    - 使用 `code token` 响应类型（示例：<http://openid.net/specs/openid-connect-core-1_0.html#code-tokenExample>）
  - 隐式身份验证流（请参见：<http://openid.net/specs/openid-connect-core-1_0.html#ImplicitAuthRequest>）
    - 使用 `id_token` 响应类型（示例：<http://openid.net/specs/openid-connect-core-1_0.html#id_tokenExample>）
    - 使用 `id_token token` 响应类型（示例：<http://openid.net/specs/openid-connect-core-1_0.html#id_token-tokenExample>）
- 客户端密钥：它与“机密”流中的一个一起使用，代码或混合。
- 跳过同意：设置用户在登录后是否必须完成同意表单。
- 高级参数：允许设置可以与授权请求一起发送的其他参数。注意：默认参数从上面的选项设置。

OpenID Connect 客户端设置示例：

```json
{
      "name": "OpenIdClientSettings",
      "Authority": "http://localhost:44300/t1",
      "DisplayName": "Orchard (t1) IdP",
      "ClientId": "orchard_t2", 
      "CallbackPath": "/signin-oidc",
      "SignedOutCallbackPath": "/signout-callback-oidc",
      "Scopes": "email phone",
      "ResponseMode": "form_post",
      "ResponseType": "code id_token",
      "ClientSecret": "secret"
}
```