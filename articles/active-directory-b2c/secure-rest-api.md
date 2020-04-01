---
title: 在 Azure AD B2C 中保护宁静的服务
titleSuffix: Azure AD B2C
description: 在 Azure AD B2C 中保护自定义 REST API 声明交换。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d1c181c18f69c040040da2be138eaad3a61693
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396863"
---
# <a name="secure-your-restful-services"></a>保护您的 RESTt 服务 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure AD B2C 用户旅程中集成 REST API 时，必须通过身份验证保护 REST API 终结点。 这可确保只有具有适当凭据的服务（如 Azure AD B2C）才能对 REST API 终结点进行调用。

了解如何在 Azure AD B2C 用户旅程中集成 REST API 以[验证用户输入](custom-policy-rest-api-claims-validation.md)并将[REST API 声明交换添加到自定义策略](custom-policy-rest-api-claims-exchange.md)文章。

本文将探讨如何使用 HTTP 基本、客户端证书或 OAuth2 身份验证来保护 REST API。 

## <a name="prerequisites"></a>先决条件

完成以下"操作"指南之一中的步骤：

- [在 Azure AD B2C 用户旅程中集成 REST API 声明交换，以验证用户输入](custom-policy-rest-api-claims-validation.md)。
- [将 REST API 声明交换添加到自定义策略](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP 基本身份验证

HTTP 基本身份验证在[RFC 2617](https://tools.ietf.org/html/rfc2617)中定义。 基本身份验证的工作原理如下：Azure AD B2C 发送具有授权标头中的客户端凭据的 HTTP 请求。 凭据格式化为基64编码字符串"name：password"。  

### <a name="add-rest-api-username-and-password-policy-keys"></a>添加 REST API 用户名和密码策略密钥

要使用 HTTP 基本身份验证配置 REST API 技术配置文件，请创建以下加密密钥以存储用户名和密码：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的**目录 + 订阅**筛选器，然后选择 Azure AD B2C 目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“概述”页上选择“标识体验框架”****。
1. 选择**策略键**，然后选择 **"添加**"。
1. 对于“选项”，请选择“手动”********。
1. 对于**名称**，键入**RestApi 用户名**。
    可能会自动添加前缀 *B2C_1A_*。
1. 在 **"机密"** 框中，输入 REST API 用户名。
1. 对于“密钥用法”，请选择“加密”********。
1. 选择“创建”  。
1. 再次选择**策略键**。
1. 选择 **添加** 。
1. 对于“选项”，请选择“手动”********。
1. 对于**名称**，键入**RestApi 密码**。
    可能会自动添加前缀 *B2C_1A_*。
1. 在 **"机密"** 框中，输入 REST API 密码。
1. 对于“密钥用法”，请选择“加密”********。
1. 选择“创建”  。

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>将 REST API 技术配置文件配置为使用 HTTP 基本身份验证

创建必要的密钥后，配置 REST API 技术配置文件元数据以引用凭据。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找到 `<Metadata>` 元素。
1. 将*身份验证类型*更改为`Basic`。
1. 将 *"允许不安全 Auth 生产"* 更改为`false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

下面是配置 HTTP 基本身份验证的 RESTful 技术配置文件的示例：

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

客户端证书身份验证是一种基于相互证书的身份验证，其中客户端 Azure AD B2C 向服务器提供其客户端证书以证明其身份。 这作为 SSL 握手的一部分发生。 只有具有适当证书的服务（如 Azure AD B2C）才能访问 REST API 服务。 客户端证书是 X.509 数字证书。 在生产环境中，必须由证书颁发机构签名。

### <a name="prepare-a-self-signed-certificate-optional"></a>准备自签名证书（可选）

对于非生产环境，如果您还没有证书，则可以使用自签名证书。 在 Windows 上，您可以使用 PowerShell 的["新自签名证书](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)"cmdlet 生成证书。

1. 执行此 PowerShell 命令以生成自签名证书。 根据需要修改`-Subject`参数，以适合您的应用程序和 Azure AD B2C 租户名称。 您还可以调整日期以`-NotAfter`指定证书的不同过期。
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
1. 打开**管理用户证书** > **当前用户** > **个人** > **证书** > *yourappname.yourtenant.onmicrosoft.com*。
1. 选择证书>**操作** > **所有任务** > **导出**。
1. 选择 **"是** > **下一个是** > **"，导出私钥** > **"下一个**"。
1. 接受**导出文件格式**的默认值。
1. 提供证书的密码。

### <a name="add-a-client-certificate-policy-key"></a>添加客户端证书策略密钥

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的**目录 + 订阅**筛选器，然后选择 Azure AD B2C 目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“概述”页上选择“标识体验框架”****。
1. 选择**策略键**，然后选择 **"添加**"。
1. 在“选项”框中，选择“上传”。********
1. 在 **"名称"** 框中，键入**RestApiClient 证书**。
    前缀 *B2C_1A_* 会自动添加。
1. 在“文件上传”框中，选择包含私钥的证书 .pfx 文件。****
1. 在“密码”框中，键入证书的密码****。
1. 选择“创建”  。

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>配置 REST API 技术配置文件以使用客户端证书身份验证

创建必要的密钥后，配置 REST API 技术配置文件元数据以引用客户端证书。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找到 `<Metadata>` 元素。
1. 将*身份验证类型*更改为`ClientCertificate`。
1. 将 *"允许不安全 Auth 生产"* 更改为`false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

下面是配置 HTTP 客户端证书的 RESTful 技术配置文件的示例：

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

## <a name="oauth2-bearer-authentication"></a>OAuth2 承载身份验证 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

承载令牌身份验证在[OAuth2.0 授权框架中定义：承载令牌使用 （RFC 6750）。](https://www.rfc-editor.org/rfc/rfc6750.txt) 在无记名令牌身份验证中，Azure AD B2C 发送具有授权标头中令牌的 HTTP 请求。

```http
Authorization: Bearer <token>
```

承载令牌是不透明字符串。 它可以是 JWT 访问令牌或 REST API 期望 Azure AD B2C 在授权标头中发送的任何字符串。 Azure AD B2C 支持以下类型：

- **承载令牌**. 为了能够在宁静的技术配置文件中发送无记名令牌，您的策略需要首先获取无记名令牌，然后在 RESTful 技术配置文件中使用它。  
- **静态承载令牌**。 当 REST API 发出长期访问令牌时，请使用此方法。 要使用静态无记名令牌，请创建策略密钥，并从 RESTful 技术配置文件向策略密钥进行引用。 


## <a name="using-oauth2-bearer"></a>使用 OAuth2 承载器  

以下步骤演示如何使用客户端凭据获取无记名令牌并将其传递到 REST API 调用的授权标头中。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>定义用于存储无记名令牌的声明

声明在 Azure AD B2C 策略执行期间提供数据的临时存储。 [声明架构](claimsschema.md)是声明声明的位置。 访问令牌必须存储在声明中，以便以后使用。 

1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 搜索 BuildingBlocks[](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到[声明架构](claimsschema.md)元素。 如果该元素不存在，请添加该元素。
1. 将城市承载令牌添加到**声明架构**元素。  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>获取访问令牌 

您可以通过以下几种方式之一获取访问令牌：[通过从联合标识提供程序](idp-pass-through-custom.md)获取访问令牌、通过调用返回访问令牌的 REST API、使用[ROPC 流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)或使用[客户端凭据流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。  

下面的示例使用 REST API 技术配置文件使用作为 HTTP 基本身份验证传递的客户端凭据向 Azure AD 令牌终结点发出请求。 要在 Azure AD 中配置此功能，请参阅[Microsoft 标识平台和 OAuth 2.0 客户端凭据流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。 您可能需要修改此内容以与您的身份提供商接口。 

对于 ServiceUrl，请将租户名称替换为 Azure AD 租户的名称。 有关所有可用的选项，请参阅[RESTful 技术配置文件](restful-technical-profile.md)参考。

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

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>将 REST 技术配置文件更改为使用无记名令牌身份验证

要在自定义策略中支持无记名令牌身份验证，请修改 REST API 技术配置文件，并参用以下内容：

1. 在工作目录中，打开 *TrustFrameworkExtensions.xml* 扩展策略文件。
1. 搜索包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 节点。
1. 找到 `<Metadata>` 元素。
1. 将*身份验证类型*更改为*承载类型*，如下所示：
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 更改或添加*使用声明标记*到*承载令牌*，如下所示。 *承载令牌*是从（来自`SecureREST-AccessToken`的输出声明）检索的持号标记的声明的名称。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 请确保将上述使用的声明添加为输入声明：

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

添加上述代码段后，您的技术配置文件应类似于以下 XML 代码：

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
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>使用静态 OAuth2 承载器 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>添加 OAuth2 承载令牌策略密钥

创建策略键以存储无记名令牌值。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的**目录 + 订阅**筛选器，然后选择 Azure AD B2C 目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“概述”页上选择“标识体验框架”****。
1. 选择**策略键**，然后选择 **"添加**"。
1. 对于“选项”****，请选择 `Manual`。
1. 输入策略密钥的**名称**。 例如，`RestApiBearerToken` 。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密****。
1. 在“密钥用法”处选择 `Encryption`。****
1. 选择“创建”  。

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>配置 REST API 技术配置文件以使用无记名令牌策略密钥

创建必要的密钥后，配置 REST API 技术配置文件元数据以引用无记名令牌。

1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技术配置文件。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找到 `<Metadata>` 元素。
1. 将*身份验证类型*更改为`Bearer`。
1. 将 *"允许不安全 Auth 生产"* 更改为`false`。
1. 紧靠在 `</Metadata>` 元素右括号的后面添加以下 XML 片段：
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

下面是配置承载令牌身份验证的 RESTful 技术配置文件的示例：

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

- 详细了解 IEF 参考中的[宁静技术配置文件](restful-technical-profile.md)元素。 
