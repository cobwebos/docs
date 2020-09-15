---
title: 在自定义策略中定义 ID 令牌提示技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中的自定义策略中定义 ID 令牌提示技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eca75ac4fefcf7164c247c4da4b58ccf7c03334c
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564791"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 ID 令牌提示技术配置文件

Azure AD B2C 允许信赖方应用程序发送入站 JWT 作为 OAuth2 authorization 请求的一部分。 JWT 令牌可以由信赖方应用程序或标识提供程序颁发，并且可以传递有关用户或授权请求的提示。 Azure AD B2C 验证签名、颁发者名称和令牌受众，并从入站令牌中提取声明。

## <a name="use-cases"></a>用例

可以使用此解决方案将数据发送到在单个 JWT 令牌中封装的 Azure AD B2C。 [注册电子邮件邀请解决方案](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)，在该解决方案中，系统管理员可以向用户发送签名邀请，基于 id_token_hint。 只有有权访问邀请电子邮件的用户才能在目录中创建帐户。

## <a name="token-signing-approach"></a>令牌签名方法

使用 id_token_hint 时，令牌颁发者 (信赖方应用或标识提供者) 组合令牌，然后使用签名密钥对令牌进行签名，以证明令牌来自受信任的来源。 签名密钥可以是对称或非对称密钥。 对称加密或私钥加密使用共享机密来签署和验证签名。 非对称加密或公钥加密是使用私钥和公钥的加密系统。 私钥仅对令牌颁发者知道，并用于对令牌进行签名。 公钥与 Azure AD B2C 策略共享以验证令牌的签名。

## <a name="token-format"></a>令牌格式

Id_token_hint 必须是有效的 JWT 令牌。 下表列出了必需的声明。 其他声明是可选的。

| 名称 | 声明 | 示例值 | 说明 |
| ---- | ----- | ------------- | ----------- |
| 目标受众 | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | 标识令牌的目标接收方。 这是令牌颁发者定义的任意字符串。 Azure AD B2C 将验证此值，如果该标记不匹配，则拒绝该令牌。  |
| 颁发者 | `iss` |`https://localhost` | 标识) security token service (令牌颁发者。 这是令牌颁发者定义的任意 URI。 Azure AD B2C 将验证此值，如果该标记不匹配，则拒绝该令牌。  |
| 过期时间 | `exp` | `1600087315` | 令牌失效的时间，以纪元时间表示。 Azure AD B2C 不会验证此声明。 |
| 生效时间 | `nbf` | `1599482515` | 令牌生效的时间，以纪元时间表示。 此时间通常与颁发令牌的时间相同。 Azure AD B2C 不会验证此声明。 |

 以下标记是有效 ID 令牌的示例：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `None`。 例如， **IdTokenHint_ExtractClaims** 技术配置文件的协议为 `None` ：

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

技术配置文件是从类型为的业务流程步骤中调用的 `GetClaims` 。

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>输出声明

**OutputClaims**元素包含要从 JWT 令牌中提取的声明的列表。 可能需要将策略中定义的声明的名称映射到 JWT 令牌中定义的名称。 如果设置了属性，则还可以包括 JWT 标记未返回的声明 `DefaultValue` 。

## <a name="metadata"></a>元数据

使用对称密钥时，以下元数据是相关的。 

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| 颁发者 | 是 | 标识) security token service (令牌颁发者。 此值必须与 `iss` JWT 令牌声明中的声明完全相同。 | 
| IdTokenAudience | 是 | 标识令牌的目标接收方。 必须与 `aud` JWT 令牌声明中的声明限完全相同。 | 

使用对称密钥时，以下元数据是相关的。 

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| METADATA| 是 | 指向令牌颁发者配置文档的 URL，也称为 OpenID 知名配置终结点。   |
| 颁发者 | 否 | 标识) security token service (令牌颁发者。 此值可用于覆盖元数据中配置的值，并且必须与 `iss` JWT 令牌声明中的声明完全相同。 |  
| IdTokenAudience | 否 | 标识令牌的目标接收方。 此值可用于覆盖元数据中配置的值，并且必须与 `aud` JWT 令牌声明中的声明完全相同。 |  

## <a name="cryptographic-keys"></a>加密密钥

使用对称密钥时， **CryptographicKeys** 元素包含以下属性：

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| client_secret | 是 | 用于验证 JWT 令牌签名的加密密钥。|


## <a name="how-to-guide"></a>操作指南

### <a name="issue-a-token-with-symmetric-keys"></a>使用对称密钥颁发令牌

#### <a name="step-1-create-a-shared-key"></a>步骤 1。 创建共享密钥 

创建可用于对令牌进行签名的密钥。 例如，使用下面的 PowerShell 代码生成一个密钥。

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

此代码会创建类似的机密字符串 `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` 。

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>步骤 2. 将签名密钥添加到 Azure AD B2C

令牌颁发者使用的同一密钥需要在 Azure AD B2C 策略密钥中创建。  

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在概述页面上的“策略”下，选择“Identity Experience Framework” 。
1. 选择 **策略密钥** 
1. 选择“手动”****。
1. 使用 `IdTokenHintKey` 作为“名称”****。  
   可能会自动添加前缀 `B2C_1A_`。
1. 在 " **密钥** " 框中，输入前面生成的登录密钥。
1. 使用“加密”**** 作为“密钥用法”****。
1. 选择“创建”。
1. 确认已创建密钥 `B2C_1A_IdTokenHintKey`。


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>步骤 3. 添加 ID 令牌提示技术配置文件

以下技术配置文件会验证令牌并提取声明。 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>步骤 4. 准备策略

完成 " [配置策略](#configure-your-policy) " 步骤。

#### <a name="step-5-prepare-the-code"></a>步骤 5。 准备代码  

[GitHub 示例](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key)是 ASP.NET web 应用程序和控制台应用程序，它生成使用对称密钥签名的 ID 令牌。 


### <a name="issue-a-token-with-asymmetric-keys"></a>使用非对称密钥颁发令牌

使用非对称密钥，将使用 RSA 证书对令牌进行签名。 此应用程序承载 (JWKs) 终结点的开放 ID 连接元数据终结点和 JSON Web 密钥，Azure AD B2C 用于验证 ID 令牌的签名。

令牌颁发者必须提供以下终结点：

* `/.well-known/openid-configuration` -一个众所周知的配置终结点，其中包含有关令牌的相关信息，例如令牌颁发者名称和 JWK 终结点的链接。 
* `/.well-known/keys` -JSON Web Key (JWK) 终结点，其中包含用于签署密钥 (与证书) 私钥部分一起使用的公钥。

请参阅 [TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .net MVC 控制器示例。

#### <a name="step-1-prepare-a-self-signed-certificate"></a>步骤 1。 准备自签名证书

如果还没有证书，可以将自签名证书用于本操作方法指南。 在 Windows 上，可使用 PowerShell [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet 来生成证书。

运行此 PowerShell 命令以生成自签名证书。 根据应用程序的需要修改 `-Subject` 参数，并修改 Azure AD B2C 租户名称。 还可调整 `-NotAfter` 日期，为证书指定不同的过期日期。

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>步骤 2. 添加 ID 令牌提示技术配置文件 

以下技术配置文件会验证令牌并提取声明。 更改令牌颁发者的已知配置终结点的元数据 URI。  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <!-- <Item Key="IdTokenAudience">your_optional_audience_override</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>步骤 3. 准备策略

完成 " [配置策略](#configure-your-policy) " 步骤。

#### <a name="step-4-prepare-the-code"></a>步骤 4. 准备代码 

此 [GitHub 示例](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET web 应用程序生成 ID 令牌，并托管在 Azure AD B2C 中使用 "id_token_hint" 参数所需的元数据终结点。


### <a name="configure-your-policy"></a>配置策略

对于对称和非对称方法， `id_token_hint` 技术配置文件是从类型为的业务流程步骤调用的， `GetClaims` 需要指定信赖方策略的输入声明。

1. 将 IdTokenHint_ExtractClaims 技术配置文件添加到扩展策略。
1. 将以下业务流程步骤添加到用户旅程，作为第一项。  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. 在信赖方策略中，重复在 IdTokenHint_ExtractClaims 技术配置文件中配置的相同输入声明。 例如：
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

根据你的业务需求，你可能需要添加令牌验证，例如，检查令牌是否过期、电子邮件地址的格式等。 为此，请添加调用 [声明转换技术配置文件](claims-transformation-technical-profile.md)的业务流程步骤。 另外添加一个 [自断言技术配置文件](self-asserted-technical-profile.md) 以显示错误消息。 

### <a name="create-and-sign-a-token"></a>创建令牌并为其签名

GitHub 示例说明了如何创建此类令牌，稍后以查询字符串参数形式发送的 JWT `id_token_hint` 。 下面是包含 id_token_hint 参数的授权请求的示例
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>后续步骤

- 查看 Azure AD B2C 社区 GitHub 存储库上的 " [注册邀请电子邮件](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) 解决方案"。
