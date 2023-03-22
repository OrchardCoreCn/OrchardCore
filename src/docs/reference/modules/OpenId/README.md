# OpenID (`OrchardCore.OpenId`)

## OpenID Connect Module

`OrchardCore.OpenId` 提供以下功能：

- 核心组件
- 授权服务器
- 管理界面
- 令牌验证
- OpenId 客户端 （OIDC）

## 核心组件Core Components

注册 OpenID 模块使用的核心组件。

## 管理界面

允许添加、编辑和删除已注册的应用程序。

## 授权服务器

使用 OpenID Connect/OAuth 2.0 标准支持外部应用程序的身份验证。 
它基于[`OpenIddict`](https://github.com/openiddict/openiddict-core) 库允许。 
Orchard Core 充当身份提供程序，以支持令牌身份验证，而无需外部标识提供程序。

- Orchard Core 还可用作标识提供程序，用于将用户访问权限集中到外部应用程序。
- Orchard Core 服务。   
- 授权服务器功能可以维护其自己的专用 JWT/验证处理程序实例的用户信息 API 终结点。这样，您就不必为当前租户启用令牌验证功能。


支持的流：[代码/隐式/混合流](http://openid.net/specs/openid-connect-core-1_0.html) 和 [客户凭证/资源所有者密码授予 
](https://tools.ietf.org/html/rfc6749)。

### 配置 Configuration

可以通过管理仪表板中的OpenID Connect设置菜单以及配方步骤设置配置。

- Token Format:
  - Data Protection: this format - enabled by default - uses non-standard opaque tokens encrypted by the ASP.NET Core Data Protection stack.
  - Json Web Token: this format uses signed JWT standard tokens. The tokens are encrypted by default but access token encryption can be turned off
to allow third-party resource servers to use the JWT tokens produced by the Orchard OpenID server.
- Authority: Orchard URL used by Orchard to act as an identity server.
- Signing Certificate Store Location: CurrentUser/LocalMachine <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storelocation(v=vs.110).aspx>
- Signing Certificate Store Name: AddressBook/AuthRootCertificateAuthority/Disallowed/My/Root/TrustedPeople/TrustedPublisher <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storename(v=vs.110).aspx>
- Encryption Certificate Thumbprint: the thumbprint of the signing certificate (it is recommended to not use same certificate that is being used for SSL).
- Encryption Certificate Store Location: CurrentUser/LocalMachine <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storelocation(v=vs.110).aspx>
- Encryption Certificate Store Name: AddressBook/AuthRootCertificateAuthority/Disallowed/My/Root/TrustedPeople/TrustedPublisher <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storename(v=vs.110).aspx>
- Encryption Certificate Thumbprint: the thumbprint of the encryption certificate (it is recommended to not use same certificate that is being used for SSL).
- Enable Token Endpoint.
- Enable Authorization Endpoint.
- Enable Logout Endpoint.
- Enable User Info Endpoint.
- Allow Password Flow: It requires that the Token Endpoint is enabled. More info at <https://tools.ietf.org/html/rfc6749#section-1.3.3>
- Allow Client Credentials Flow: It requires that the Token Endpoint is enabled. More info at <https://tools.ietf.org/html/rfc6749#section-1.3.4>
- Allow Authorization Code Flow: It requires that the Authorization and Token Endpoints are enabled. More info at <http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>
- Allow Implicit Flow: It requires that the Authorization Endpoint is enabled. More info at <http://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth>
- Allow Refresh Token Flow: It allows to refresh access token using a refresh token. It can be used in combination with Password Flow, Authorization Code Flow and Hybrid Flow. More info at <http://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens>
- Require Proof Key for Code Exchange: Global setting that applies PKCE to all registered clients whether or not the 'Require PKCE' flag was set in the Application settings page.

- 测试模式：启用测试模式，无需提供证书来签署提供临时密钥的令牌。同时也消除了使用HTTPS发行令牌的要求。 
- 令牌格式：有两个选项:
  - JWT：此格式使用签名的JWT标准令牌（未加密）。它要求客户端接受正在使用的SSL证书作为可信证书。
  - Encrypted: 此格式使用由ASP.NET数据保护块。它不要求客户端接受SSL证书作为可信证书。
- Authority: 权威，Orchard用作身份服务器的Orchard URL。
- Audiences: 观众 identity server为其颁发有效JWT令牌的资源服务器的URL.
- Certificate Store Location: 证书存储位置， CurrentUser/LocalMachine <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storelocation(v=vs.110).aspx>
- Certificate Store Name: 证书存储名称，通讯录(AddressBook)/验证根证书颁发机构（Auth Root Certificate Authority）/不允许(Disallowed)/我的(My)/根(Root)/可信人(TrustedPeople)/可信发布者(TrustedPublisher) <https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storename(v=vs.110).aspx>
- Certificate Thumbprint: 证书的指纹（建议不要使用用于SSL的同一证书）。
- Enable Token Endpoint： 启用令牌终结点.
- Enable Authorization Endpoint：  启用授权终结点.
- Enable Logout Endpoint ： 启用注销终结点 .
- Enable User Info Endpoint： 启用用户信息终结点.
- Allow Password Flow : 允许密码流， 要求启用令牌终结点。更多信息请访问 <https://tools.ietf.org/html/rfc6749#section-1.3.3>
- Allow Client Credentials Flow: 允许客户端凭据流， 要求启用令牌终结点。更多信息请访问<https://tools.ietf.org/html/rfc6749#section-1.3.4>
- Allow Authorization Code Flow: 允许授权代码流，要求启用授权和令牌终结点。更多信息请访问 <http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>
- Allow Implicit Flow:  允许隐式流， 要求启用授权终结点。更多信息请访问 <http://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth>
- Allow Refresh Token Flow:  允许刷新令牌流， 它允许使用刷新令牌刷新访问令牌。它可以与密码流、授权码流和混合流结合使用。更多信息请访问 <http://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens>

OpenID连接设置配方步骤示例:

```json 
{
      "name": "OpenIdServerSettings",
      "TestingModeEnabled": false,
      "AccessTokenFormat": "JsonWebToken", // JsonWebToken or DataProtection
      "Authority": "https://www.orchardproject.net",
      "SigningCertificateStoreLocation": "LocalMachine", //More info: https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storelocation(v=vs.110).aspx
      "SigningCertificateStoreName": "My", //More info: https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.storename(v=vs.110).aspx
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

### 客户端验证
服务器端配置好后，在你的客户端进行授权验证 :
```Javascript
// environment.serverURL 是你的Orchard Core 的站点根路径
    const url = environment.serverURL + '/connect/token/';
    this.blogPosts = this.allBlogPostGQL.watch().valueChanges.pipe(map(blogs => blogs.data));

    const body = new HttpParams()
      .set("client_id", "e0f660a2cf2a47babac40a4a8c24e7e0")
      .set("client_secret", "76945d3917a4456db5a41fc2949d6439")
      .set("grant_type", "client_credentials");
    const headers = new HttpHeaders({
      "Content-Type": "application/x-www-form-urlencoded"
    });
     
    this.http.post(url, body, {headers: headers}).subscribe( res => {
      console.log(res);
      const jsonToken = res['access_token'];
      localStorage.setItem('access_token', jsonToken)
    });
```

参考这里：<https://github.com/OrchardSkills/OrchardSkills.OrchardCore.AuthenticatedGraphQL/blob/f4aca695c922ef733294c1cf8b0bd28e82dc954b/ClientApp/src/app/app.component.ts#L38>

- Id: Unique identifier.
- Client Id: Client identifier of the application. It has to be provided by a client when requesting a valid token.
- Display Name: Display name associated with the current application.
- Type: There are two options:
  - Confidential: Confidential applications MUST send their client secret when communicating with the token and revocation endpoints. This guarantees that only the legit client can exchange an authorization code or get a refresh token.
  - Public: Public applications don't use client secret on their communications.
- Client Secret: Client secret is a password associated with the application. It will be required when the application is configured as Confidential.
- Flows: If general OpenID Connect settings allow this flow, an app can also enable this flow.
  - Allow Password Flow: It requires that the Token Endpoint is enabled. More info at <https://tools.ietf.org/html/rfc6749#section-1.3.3>
  - Allow Client Credentials Flow: It requires that the Token Endpoint is enabled. More info at <https://tools.ietf.org/html/rfc6749#section-1.3.4>
  - Allow Authorization Code Flow: It requires that the Authorization and Token Endpoints are enabled. More info at <http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>
  - Allow Implicit Flow: It requires that the Authorization Endpoint is enabled. More info at <http://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth>
  - Allow Refresh Token Flow: It allows to refresh access token using a refresh token. It can be used in combination with Password Flow, Authorization Code Flow and Hybrid Flow. More info at <http://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens>
- Normalized RoleNames: This configuration is only required if Client Credentials Flow is enabled. It determines the roles assigned to the app when it is authenticated using that flow.
- Redirect Options: Those options are only required when Implicit Flow, Authorization Code Flow or Allow Hybrid Flow is required.
- Logout Redirect Uri: logout callback URL.
- Redirect Uri: callback URL.
- Skip Consent: sets whether a consent form has to be completed by the user after log in.
- Advanced Parameters: Allows setting additional parameters that can be sent with the authorize request. Note: The default parameters are set from the options above.
- Require PKCE: Applies PKCE for the registered application.  Ensure that the client library being used suppports PKCE.  


### OpenID 客户端连接应用程序配置

可以通过管理菜单中的“安全/OpenID 连接/管理/应用程序” 打开
也可以通过配方步骤配置。

OpenID Connect客户端应用程序需要以下配置.

- Id: 唯一标识符，配置界面不可见.
- 客户端 Id: 应用程序的客户端标识符。请求有效令牌时，必须由客户端提供。
- 显示名称：用户自定义的显示名称。
- 类型: 有两种:
  - Confidential:机密客户端，机密应用程序在与令牌和吊销终结点通信时必须发送其客户端机密。这保证了只有合法的客户端可以交换授权代码或获取刷新令牌。
  - Public:公共客户端， 公共应用程序在通信中不使用客户端机密。
- 客户机密（客户端密钥）: 客户端密钥是与应用程序关联的密码。当应用程序配置为机密时，它将是必需的。
- Flows: 如果常规OpenID连接设置允许此流，则应用程序也可以启用此流。
  - 允许授权密码流（Allow Password Flow）： 它要求启用令牌终结点。更多信息请访问 <https://tools.ietf.org/html/rfc6749#section-1.3.3> \
   请求示例:  ：
    ``` JSON 
    //EndPoint="/connect/token" ,Method=POST,Content-Type=application/x-www-form-urlencoded 
    {
      "grant_type":"password",
      "client_id": "client_id",
      "client_secret": "client_secret",
      "username":"username",
      "password":"password",
      "scope": "openid profile roles"
    }
    ```
  - 允许客户端凭据流(Allow Client Credentials Flow): 它要求启用令牌终结点。更多信息请访问 <https://tools.ietf.org/html/rfc6749#section-1.3.4>
  - 允许授权代码流(Allow Authorization Code Flow): 它要求启用授权和令牌终结点。更多信息请访问<http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth> \
   请求示例：
    ``` JSON    
    //EndPoint="/connect/token" ,Method=POST,Content-Type=application/x-www-form-urlencoded 
    {
      "grant_type": "code",
      "client_id": "client_id",
      "client_secret": "client_secret",
      "scope": "openid profile roles"
    }
    ```
  - 允许隐式流(Allow Implicit Flow): 它要求启用授权终结点。更多信息请访问 <http://openid.net/specs/openid-connect-core-1_0.html#ImplicitFlowAuth>
  - 允许刷新令牌流(Allow Refresh Token Flow): 它允许使用刷新令牌刷新访问令牌。它可以与密码流、授权码流和混合流结合使用。更多信息请访问 <http://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens>
- 标准化角色名(Normalized RoleNames): 只有启用客户端凭据流时，才需要此配置。当应用程序使用该流进行身份验证时，它确定分配给该应用程序的角色。
- 重定向选项(Redirect Options): 仅当需要隐式流、授权代码流或允许混合流时，才需要这些选项。
- 注销重定向Uri(Logout Redirect Uri): 注销回调URL.
- 重定向Uri(Redirect Uri): 回调URL.
- 同意类型: 设置用户登录后是否必须填写同意书。

OpenID Connect应用程序配方步骤示例:

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

### OpenID连接作用域配置

可以通过管理菜单中的“安全/OpenID 连接/管理/作用域” 打开设置或使用配方步骤设置作用域。

OpenID连接作用域需要以下配置.

| Property | Description |
| -------- | ----------- |
|名称|作用域的唯一名称.|
|显示名称|与当前作用域关联的显示名称。|
|说明|描述如何在系统中使用此作用域。|
|租户|根据租户的名字建立访问群体。|
|其他资源 (API 访问群体)|根据提供的空格分隔字符串构建访问群体。|

OpenID Connect Scope配方步骤示例：

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

有几种工具可用于在Windows和/或IIS上生成签名证书，例如：

- IIS Server Manager _(offers limited control)_
    1. 服务器证书
    2. 创建自签名证书
- PowerShell _(提供完全控制)_
    1. `New-SelfSignedCertificate`, 例如:

```powershell
# See https://technet.microsoft.com/en-us/itpro/powershell/windows/pkiclient/new-selfsignedcertificate

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

**此代码段必须以管理员身份运行。** 它生成一个4096位签名证书，将其存储在计算机存储中，并返回证书的指纹，这是您在OpenID Connect Settings配方中或通过PowerShell导出证书时所需的指纹。_你应该根据你的要求修改这个例子！_

在多节点环境中，请考虑使用 `-KeyExportPolicy Exportable`, 然后使用MMC证书管理单元或PowerShell将证书（PFX）导出到安全位置`Export-PfxCertificate`, 然后将每个节点上的证书导入为不可导出，这是使用`Import-PfxCertificate`时的默认值。例如：

```powershell
# See https://technet.microsoft.com/en-us/itpro/powershell/windows/pkiclient/export-pfxcertificate
# 在生成证书的计算机上运行此命令：

$mypwd = ConvertTo-SecureString -String "MySecretPassword123" -Force -AsPlainText

Export-PfxCertificate -FilePath C:\securelocation\connect.example.com.pfx cert:\localMachine\my\thumbprintfromnewselfsignedcertificate -Password $mypwd

# See https://technet.microsoft.com/en-us/itpro/powershell/windows/pkiclient/import-pfxcertificate
# 在目标节点上运行此命令：

$mypwd = ConvertTo-SecureString -String "MySecretPassword123" -Force -AsPlainText

Import-PfxCertificate -FilePath C:\securelocation\connect.example.com.pfx cert:\localMachine\my -Password $mypwd
```

**重要提示:** `OrchardCore.OpenId`模块若要使用证书的密钥进行签名，它要求对存储区中的证书具有`Read`访问权限。这可以通过多种方式授予，例如：


- `MMC.exe`
    1. 为计算机帐户添加管理单元证书
    2. 右键单击相关证书并选择所有任务，管理私钥
    3. 添加相关标识(如： IIS AppPool\PoolName)
        - 添加
        - 高级
        - 位置: 选择iis服务器计算机名
        - 立即查找
        - 搜索结果：选择你的 IIS服务器名称\IIS_IUSRS（通常仅一个）
        - 确认
    4. 选中“允许在权限下读取”
- `WinHttpCertCfg.exe` (授予完全控制权)
    1. 例如： `winhttpcertcfg -g -c LOCAL_MACHINE\My -s connect.example.com -a AppPoolIdentityName` <https://msdn.microsoft.com/en-us/library/windows/desktop/aa384088(v=vs.85).aspx>

### 在Azure中使用证书

在Azure托管站点上使用证书。

1.将证书上载到网站Azure门户页面的“TLS/SSL设置”页面。
2. 向Azure网站设置页面添加一个新条目，内容如下：
    - Key: WEBSITE_LOAD_CERTIFICATES
    - Value: [证书的指纹]
3. 选择下面的证书 `CurrentUser` > `My` 证书存储。

## 令牌验证 Token Validation

- 验证Orchard OpenID服务器发出的令牌
  - 将验证功能配置为透明地使用另一个已启用授权服务器功能的租户的服务器配置。
- 通过支持JWT和OpenID连接发现的远程服务器验证令牌。

令牌验证需要以下配置。

| Property | Description |
| -------- | ----------- |
|授权服务器租户|运行OpenID Connect Server的租户。如果未选择“无”，则必须提供以下属性。|
|权威（Authority）|颁发令牌的远程OpenID Connect服务器的地址。|
|观众（Audience）|定义必须检查的令牌的预期收件人.|

令牌验证设置配方步骤示例：

```json
    {
      "name": "OpenIdValidationSettings",
      "Audience": "my_recipient",
      "Authority": "https://idp.domain.com"
    }
```

## OpenId 客户端 (OIDC)

从外部OpenID Connect身份提供程序对用户进行身份验证。
如果站点允许注册新用户，则链接本地用户和外部登录。
如果收到“电子邮件”声明，并且找到本地用户，则在验证后，外部登录将链接到该帐户。

### OpenId配置

配置可以通过管理仪表板中的“OpenID Connect”设置菜单进行设置，也可以通过配方步骤进行设置。

可用设置为：

- 显示名称: 凭据提供程序(IdP:Identity Provider)的显示名称。它显示在登录表单中。
- 权威: 进行OpenIdConnect调用时使用的权限。
- 客户端Id: `client_id` 部分.
- 回调地址(CallbackPath): 应用程序基本路径中的请求路径，从身份提供程序注销后，将在该路径中返回用户代理。见 `post_logout_redirect_uri` from <http://openid.net/specs/openid-connect-session-1_0.html#RedirectionAfterLogout>
- 注销回调地址(SignedOutCallbackPath): 注销的回调终结点。默认为`/signout-callback-oidc`.
- 注销重定向地址: 从身份提供程序注销应用程序后，用户代理将重定向到的URI。重定向地址将被调用 `SignedOutCallbackPath`.
- 范围(Scopes): 除openid和profile之外的其他作用域。
- 响应模式(ResponseMode): 配置响应模式请参阅： <http://openid.net/specs/openid-connect-core-1_0.html#ImplicitAuthResponse>.如果只允许片段或查询代码验证流。
- 支持的流(ResponseType)：选择一个OIDC（OpenId 客户端）流：
  - 代码验证流(see: <http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>)
  - 混合认证流(see: <http://openid.net/specs/openid-connect-core-1_0.html#HybridAuthRequest>)
    - 使用 `code id_token` 响应类型（例如： <http://openid.net/specs/openid-connect-core-1_0.html#code-id_token-tokenExample>)
    - 使用 `code id_token token`响应类型（例如： <http://openid.net/specs/openid-connect-core-1_0.html#code-id_token-tokenExample>)
    - 使用 `code token` 响应类型（例如：<http://openid.net/specs/openid-connect-core-1_0.html#code-tokenExample>)
  - 隐式身份验证流（见：<http://openid.net/specs/openid-connect-core-1_0.html#ImplicitAuthRequest>)
    - 使用 `id_token` 响应类型（例如： <http://openid.net/specs/openid-connect-core-1_0.html#id_tokenExample>)
    - 使用 `id_token token` 响应类型（例如：<http://openid.net/specs/openid-connect-core-1_0.html#id_token-tokenExample>)
- 客户端机密（ClientSecret）: 它与一个“机密”流、代码或混合流一起使用。

OpenID Connect客户端设置配方步骤示例：

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
