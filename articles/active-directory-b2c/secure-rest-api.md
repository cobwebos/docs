---
title: 在 Azure AD B2C 中保护 Restful 服务
titleSuffix: Azure AD B2C
description: 保护 Azure AD B2C 中的自定义 REST API 声明交换。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34ed6d043f713aa55bfe464c48d4332364df805d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680375"
---
# <a name="secure-your-restful-services"></a>保护你的 RESTful 服务 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

将 REST API 集成到 Azure AD B2C 用户旅程中时，必须使用身份验证来保护 REST API 终结点。 这可确保只有具有正确凭据的服务（如 Azure AD B2C）才能调用 REST API 终结点。

了解如何在 "[验证用户输入](custom-policy-rest-api-claims-validation.md)" 中将 REST API 集成到 Azure AD B2C 用户旅程中，以及如何[将 REST API 声明交换添加到自定义策略](custom-policy-rest-api-claims-exchange.md)文章。

本文将探讨如何通过 HTTP 基本、客户端证书或 OAuth2 authentication 保护 REST API。 

## <a name="prerequisites"></a>必备条件

完成以下 "操作方法" 指南之一中的步骤：

- [集成 Azure AD B2C 用户旅程中的 REST API 声明交换来验证用户输入](custom-policy-rest-api-claims-validation.md)。
- [将 REST API 声明交换添加到自定义策略](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP 基本身份验证

在[RFC 2617](https://tools.ietf.org/html/rfc2617)中定义了 HTTP 基本身份验证。 基本身份验证的工作原理如下： Azure AD B2C 使用授权标头中的客户端凭据发送 HTTP 请求。 凭据的格式为 base64 编码的字符串 "name： password"。  

### <a name="add-rest-api-username-and-password-policy-keys"></a>添加 REST API 用户名和密码策略密钥

若要使用 HTTP 基本身份验证配置 REST API 技术配置文件，请创建以下用于存储用户名和密码的加密密钥：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择 Azure AD B2C 的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“概述”页上选择“标识体验框架”****。
1. 选择 "**策略密钥**"，然后选择 "**添加**"。
1. 对于“选项”，请选择“手动”********。
1. 对于 "**名称**"，请键入**RestApiUsername**。
    可能会自动添加前缀 *B2C_1A_*。
1. 在 "**密钥**" 框中，输入 REST API 的用户名。
1. 对于“密钥用法”，请选择“加密”********。
1. 选择“创建”。 
1. 再次选择 "**策略密钥**"。
1. 选择 **添加** 。
1. 对于“选项”，请选择“手动”********。
1. 对于 "**名称**"，请键入**RestApiPassword**。
    可能会自动添加前缀 *B2C_1A_*。
1. 在 "**密钥**" 框中，输入 REST API 密码。
1. 对于“密钥用法”，请选择“加密”********。
1. 选择“创建”。 

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>将 REST API 技术配置文件配置为使用 HTTP 基本身份验证

创建所需的密钥后，请配置 REST API 技术配置文件元数据以引用凭据。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 查找 `<Metadata>` 元素。
1. 将*AuthenticationType*更改为`Basic`。
1. 将*AllowInsecureAuthInProduction*更改为`false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

下面是使用 HTTP 基本身份验证配置的 RESTful 技术配置文件的示例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS 客户端证书身份验证

客户端证书身份验证是一种基于证书的相互身份验证，其中，客户端 Azure AD B2C 向服务器提供其客户端证书以证明其身份。 这会作为 SSL 握手的一部分出现。 只有具有适当证书的服务（如 Azure AD B2C）才能访问你的 REST API 服务。 客户端证书是 x.509 数字证书。 在生产环境中，它必须由证书颁发机构签名。

### <a name="prepare-a-self-signed-certificate-optional"></a>准备自签名证书（可选）

对于非生产环境，如果还没有证书，则可以使用自签名证书。 在 Windows 上，可以使用 PowerShell 的[new-selfsignedcertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet 来生成证书。

1. 执行此 PowerShell 命令以生成自签名证书。 根据应用`-Subject`程序的需要修改参数，并 Azure AD B2C 租户名称。 你还可以调整`-NotAfter`日期，为证书指定不同的过期时间。
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
1. 打开 "**管理用户证书** > " "**当前用户** > **个人** > **证书** > *yourappname.yourtenant.onmicrosoft.com*"。
1. 选择证书 >**操作** > "**所有任务** > " "**导出**"。
1. 选择 **"是** > "**，** > **然后** > 选择 "**下一步**"。
1. 接受**导出文件格式**的默认值。
1. 提供证书的密码。

### <a name="add-a-client-certificate-policy-key"></a>添加客户端证书策略密钥

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择 Azure AD B2C 的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“概述”页上选择“标识体验框架”****。
1. 选择 "**策略密钥**"，然后选择 "**添加**"。
1. 在“选项”框中，选择“上传”。********
1. 在 "**名称**" 框中，键入**RestApiClientCertificate**。
    前缀 *B2C_1A_* 会自动添加。
1. 在“文件上传”框中，选择包含私钥的证书 .pfx 文件。****
1. 在“密码”框中，键入证书的密码****。
1. 选择“创建”。 

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>将 REST API 技术配置文件配置为使用客户端证书身份验证

创建所需的密钥后，请配置 REST API 技术配置文件元数据以引用客户端证书。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 查找 `<Metadata>` 元素。
1. 将*AuthenticationType*更改为`ClientCertificate`。
1. 将*AllowInsecureAuthInProduction*更改为`false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

下面是使用 HTTP 客户端证书配置的 RESTful 技术配置文件的示例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 持有者身份验证 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

持有者令牌身份验证在[oauth 2.0 授权框架中定义：持有者令牌用法（RFC 6750）](https://www.rfc-editor.org/rfc/rfc6750.txt)。 在持有者令牌身份验证中，Azure AD B2C 使用 authorization 标头中的令牌发送 HTTP 请求。

```http
Authorization: Bearer <token>
```

持有者令牌是不透明的字符串。 它可以是 JWT 访问令牌，也可以是 REST API 要求 Azure AD B2C 在授权标头中发送的任何字符串。 Azure AD B2C 支持以下类型：

- **持有者令牌**。 为了能够在 Restful 技术配置文件中发送持有者令牌，策略需要首先获取持有者令牌，然后在 RESTful 技术配置文件中使用该令牌。  
- **静态持有者令牌**。 当 REST API 颁发长期访问令牌时使用此方法。 若要使用静态持有者令牌，请创建策略密钥并从 RESTful 技术配置文件到策略密钥进行引用。 


## <a name="using-oauth2-bearer"></a>使用 OAuth2 持有者  

以下步骤演示了如何使用客户端凭据获取持有者令牌，并将其传递到 REST API 调用的 Authorization 标头。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>定义声明以存储持有者令牌

声明在 Azure AD B2C 策略执行过程中提供数据的临时存储。 声明[架构](claimsschema.md)是声明声明的位置。 必须将访问令牌存储在声明中，以便稍后使用。 

1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 搜索 BuildingBlocks[](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 " [ClaimsSchema](claimsschema.md) " 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到**ClaimsSchema**元素中。  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>获取访问令牌 

可以通过以下几种方式之一获取访问令牌：[从联合标识提供者](idp-pass-through-custom.md)获取访问令牌，通过调用返回访问令牌的 REST API，使用[ROPC 流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)，或使用[客户端凭据流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。  

下面的示例使用 REST API 技术配置文件，使用作为 HTTP 基本身份验证传递的客户端凭据向 Azure AD 令牌终结点发出请求。 若要在 Azure AD 中配置此设置，请参阅[Microsoft 标识平台和 OAuth 2.0 客户端凭据流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。 可能需要将此修改为与标识提供者交互。 

对于 ServiceUrl，请将-租户名称替换为 Azure AD 租户的名称。 有关所有可用选项，请参阅[RESTful 技术配置文件](restful-technical-profile.md)参考。

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>将 REST 技术配置文件更改为使用持有者令牌身份验证

若要在自定义策略中支持持有者令牌身份验证，请通过以下方式修改 REST API 技术配置文件：

1. 在工作目录中，打开 *TrustFrameworkExtensions.xml* 扩展策略文件。
1. 搜索包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 节点。
1. 查找 `<Metadata>` 元素。
1. 将*AuthenticationType*更改为*持有*者，如下所示：
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 将*UseClaimAsBearerToken*更改或添加到*bearerToken*，如下所示。 *BearerToken*是要从中检索持有者令牌（来自`SecureREST-AccessToken`的输出声明）的声明的名称。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 确保将上述声明作为输入声明添加：

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

添加上述代码段后，技术配置文件应类似于以下 XML 代码：

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>使用静态 OAuth2 持有者 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>添加 OAuth2 持有者令牌策略密钥

创建策略密钥以存储持有者令牌值。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择 Azure AD B2C 的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“概述”页上选择“标识体验框架”****。
1. 选择 "**策略密钥**"，然后选择 "**添加**"。
1. 对于“选项”****，请选择 `Manual`。
1. 输入策略密钥的**名称**。 例如，`RestApiBearerToken` 。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密****。
1. 在“密钥用法”处选择 `Encryption`。****
1. 选择“创建”。 

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>将 REST API 技术配置文件配置为使用持有者令牌策略密钥

创建所需的密钥后，请配置 REST API 技术配置文件元数据以引用持有者令牌。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 查找 `<Metadata>` 元素。
1. 将*AuthenticationType*更改为`Bearer`。
1. 将*AllowInsecureAuthInProduction*更改为`false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

下面是使用持有者令牌身份验证配置的 RESTful 技术配置文件的示例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅 IEF 参考中的[Restful 技术配置文件](restful-technical-profile.md)元素。 
