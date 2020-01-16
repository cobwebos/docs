---
title: 将 Azure AD B2C 配置为应用程序的 SAML IdP
title-suffix: Azure AD B2C
description: 如何配置 Azure AD B2C 以向应用程序提供 SAML 协议断言（服务提供程序）。 Azure AD B2C 将充当 SAML 应用程序的单个标识提供者（IdP）。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 16651441919ecd5167e518f68addd8964e767001
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043527"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 SAML 应用程序

本文介绍如何配置 Azure Active Directory B2C （Azure AD B2C），以将其用作应用程序的安全断言标记语言（SAML）标识提供者（IdP）。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>方案概述

使用 Azure AD B2C 作为其客户标识和访问管理解决方案的组织可能需要与配置为使用 SAML 协议进行身份验证的标识提供者或应用程序交互。

Azure AD B2C 通过以下两种方式之一实现 SAML 互操作性：

* 通过充当*标识提供者*（IdP）并使用基于 SAML 的服务提供程序（应用程序）实现单一登录（SSO）
* 作为*服务提供商*（SP），并与基于 SAML 的标识提供程序（如 SALESFORCE 和 ADFS）交互

![在左侧将 B2C 作为标识提供者的关系图作为右侧的服务提供商](media/saml-identity-provider/saml-idp-diagram-01.jpg)

用 SAML 汇总两个非独占核心方案：

| 方案 | Azure AD B2C 角色 | 操作说明 |
| -------- | ----------------- | ------- |
| 我的应用程序需要 SAML 断言才能完成身份验证。 | **Azure AD B2C 充当标识提供者（IdP）**<br />Azure AD B2C 充当应用程序的 SAML IdP。 | 本文。 |
| 我的用户需要使用与 SAML 兼容的标识提供程序（如 ADFS、Salesforce 或 Shibboleth）进行单一登录。  | **Azure AD B2C 充当服务提供商（SP）**<br />当连接到 SAML 标识提供者时，Azure AD B2C 充当服务提供商。 它是应用程序与 SAML 标识提供者之间的联合代理。  | <ul><li>[使用自定义策略将 ADFS 设置为 SAML IdP](active-directory-b2c-custom-setup-adfs2016-idp.md)</li><li>[使用自定义策略设置 Salesforce SAML 提供程序登录](active-directory-b2c-setup-sf-app-custom.md)</li></ul> |

## <a name="prerequisites"></a>必备组件

* 完成[Azure AD B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。 你需要从本文中讨论的自定义策略初学者包中*SocialAndLocalAccounts*自定义策略。
* 基本了解安全断言标记语言（SAML）协议。
* 配置为 SAML 服务提供程序（SP）的 web 应用程序。 对于本教程，可以使用我们提供的[SAML 测试应用程序][samltest]。

## <a name="components-of-the-solution"></a>解决方案的构成

此方案需要三个主要组件：

* SAML**服务提供程序**，能够发送 saml 请求，以及接收、解码和响应 AZURE AD B2C 的 saml 断言。 这也称为依赖方。
* 服务提供商的公开可用 SAML**元数据终结点**。
* [Azure AD B2C 租户](tutorial-create-tenant.md)

如果还没有 SAML 服务提供程序和关联的元数据终结点，则可以使用此示例 SAML 应用程序，该应用程序可用于测试：

[SAML 测试应用程序][samltest]

## <a name="1-set-up-certificates"></a>1. 设置证书

若要在服务提供商与 Azure AD B2C 之间建立信任关系，则需要提供 X509 证书及其私钥。

* **服务提供商证书**
  * 具有存储在 Web 应用中的私钥的证书。 服务提供商使用此证书对发送到 Azure AD B2C 的 SAML 请求进行签名。 Azure AD B2C 从服务提供程序元数据读取公钥来验证签名。
  * 可有可无具有存储在 Web 应用中的私钥的证书。 Azure AD B2C 从服务提供程序元数据读取公钥以加密 SAML 断言。 然后，服务提供商使用私钥来解密断言。
* **Azure AD B2C 证书**
  * 具有 Azure AD B2C 的私钥的证书。 Azure AD B2C 使用此证书对发送到服务提供商的 SAML 响应进行签名。 服务提供程序读取 Azure AD B2C 的元数据公钥以验证 SAML 响应的签名。

你可以使用由公共证书颁发机构颁发的证书，也可以在本教程中使用自签名证书。

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 准备自签名证书

如果还没有证书，可在本教程中使用自签名证书。 在 Windows 上，可以使用 PowerShell 的[new-selfsignedcertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet 来生成证书。

1. 执行此 PowerShell 命令以生成自签名证书。 根据应用程序的需要修改 `-Subject` 参数，并 Azure AD B2C 租户名称。 你还可以调整 `-NotAfter` 日期，为证书指定不同的过期时间。

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

1. 打开 "**管理用户证书**" > **当前用户** > **个人** > **证书** > *yourappname.yourtenant.onmicrosoft.com*
1. 选择 > **所有任务** > **导出**的证书 >**操作**
1. 选择 **"是**" > **下一步** > **是，导出私钥** > **下一步**
1. 接受**导出文件格式**的默认值
1. 提供证书的密码

### <a name="12-upload-the-certificate"></a>1.2 上传证书

接下来，将 SAML 断言和响应签名证书上传到 Azure AD B2C。

1. 登录到[Azure 门户](https://portal.azure.com)并浏览到 Azure AD B2C 租户。
1. 在 "**策略**" 下，选择 "**标识体验框架**"，然后选择 "**策略密钥**"。
1. 选择 "**添加**"，然后选择 "**选项**" > **上传**"。
1. 输入一个**名称**，例如*SamlIdpCert*。 前缀 B2C_1A_ 会自动添加到密钥名称。
1. 使用 "上传文件" 控件上载证书。
1. 输入证书的密码。
1. 选择“创建”。
1. 验证键是否按预期方式显示。 例如， *B2C_1A_SamlIdpCert*。

## <a name="2-prepare-your-policy"></a>2. 准备策略

### <a name="21-create-the-saml-token-issuer"></a>2.1 创建 SAML 令牌颁发者

现在，请为你的租户添加功能，以颁发 SAML 令牌。

打开自定义策略初学者包中 `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** 。

找到 "`<ClaimsProviders>`" 部分并添加以下 XML 代码片段。

可以更改 `IssuerUri` 元数据的值。 这是 Azure AD B2C 的 SAML 响应中返回的颁发者 URI。 应将信赖方应用程序配置为在 SAML 断言验证期间接受颁发者 URI。

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. 添加 SAML 信赖方策略

现在，你的租户可以发出 SAML 断言，你需要创建 SAML 信赖方策略，并修改用户旅程，使其发出 SAML 断言而不是 JWT。

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 创建注册或登录策略

1. 在初学者 pack 工作目录中创建*signuporsignin.xml*文件的副本，并使用新名称保存。 例如， *SignUpOrSigninSAML*。 这是信赖方策略文件。

1. 在偏好的编辑器中打开*SignUpOrSigninSAML*文件。

1. 将策略的 `PolicyId` 和 `PublicPolicyUri` 更改为_B2C_1A_signup_signin_saml_和 `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`，如下所示。

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 在 `<RelyingParty>` 元素之前添加以下 XML 代码段。 此 XML 将覆盖_signuporsignin.xml_用户旅程的业务流程步骤7。 如果是从初学者包中的不同文件夹启动的，或通过添加或删除业务流程步骤自定义用户旅程，请确保数字（在 `order` 元素中）与 "令牌颁发者" 步骤的 "用户旅程" 中指定的值相匹配（例如，在其他 starter pack 文件夹中，它的步骤号为4，用于 `LocalAccounts`，6 `SocialAccounts` 用于 `SocialAndLocalAccountsWithMfa`）。

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. 将 `<RelyingParty>` 元素中的整个 `<TechnicalProfile>` 元素替换为以下技术配置文件 XML。

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. 用 Azure AD B2C 租户的名称更新 `tenant-name`。

最终的信赖方策略文件应如下所示：

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 上传和测试策略元数据

保存更改并上传新的策略文件。 上传两个策略（扩展和信赖方文件）后，打开 web 浏览器并导航到策略元数据。

以下 URL 提供了 Azure AD B2C 策略元数据。 将 `tenant-name` 替换为 Azure AD B2C 租户的名称，并将 `policy-name` 替换为策略的名称（ID）：

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

你的自定义策略和 Azure AD B2C 租户现已准备就绪。 接下来，在 Azure AD B2C 中创建应用程序注册。

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. 在 Azure AD B2C 目录中安装应用程序

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 在 Azure Active Directory 中注册应用程序

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册(预览版)”，然后选择“新建注册”。
1. 输入应用程序的“名称”。 例如， *SAMLApp1*。
1. 在 "**支持的帐户类型**" 下，选择 "**仅限此组织目录中的帐户**"
1. 在 "**重定向 URI**" 下，选择 " **Web**"，然后输入 `https://localhost`。 稍后会在应用程序注册的清单中修改此值。
1. 选择 "**将管理员同意授予 openid 并 offline_access 权限**"。
1. 选择“注册”。

### <a name="42-update-the-app-manifest"></a>4.2 更新应用程序清单

对于 SAML 应用，需要在应用程序注册的清单中配置几个属性。

1. 在[Azure 门户](https://portal.azure.com)中，导航到在上一部分中创建的应用程序注册。
1. 在 "**管理**" 下，选择 "**清单**" 打开清单编辑器。 在以下部分中修改多个属性。

#### <a name="identifieruris"></a>identifierUris

`identifierUris` 是一个字符串集合，其中包含唯一标识其 Azure AD B2C 租户内的 Web 应用的用户定义 URI。 服务提供商必须在 SAML 请求的 `Issuer` 元素中设置此值。

#### <a name="samlmetadataurl"></a>samlMetadataUrl

此属性表示服务提供程序的公开可用元数据 URL。 元数据 URL 可以指向已上传到任何可匿名访问的终结点（例如 blob 存储）的元数据文件。

元数据是 SAML 协议中用于公开 SAML 参与方（如服务提供程序）的配置的信息。 元数据定义服务的位置，例如登录和注销、证书、登录方法，等等。 Azure AD B2C 读取服务提供程序元数据并进行相应的操作。 不需要元数据。 你还可以在应用程序清单中直接指定某些属性，如回复 URI 和注销 URI。

如果在 SAML 元数据 URL 和应用程序注册的清单中*同时*指定了属性，则会**合并**它们。 首先处理元数据 URL 中指定的属性并优先处理。

对于使用 SAML 测试应用程序的本教程，请使用以下 `samlMetadataUrl`值：

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType （可选）

如果未提供元数据 URI，则可以显式指定回复 URL。 此可选属性表示服务提供程序元数据中的 `AssertionConsumerServiceUrl` （`SingleSignOnService` URL），并且假定 `BindingType` 为 `HTTP POST`。

如果在不使用服务提供程序元数据的情况下，选择在应用程序清单中配置回复 URL 和注销 URL，Azure AD B2C 将不会验证 SAML 请求签名，也不会加密 SAML 响应。

对于本教程，使用 SAML 测试应用程序时，请将 `replyUrlsWithType` 的 `url` 属性设置为以下 JSON 代码段中所示的值。

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>LogoutUrl （可选）

此可选属性表示 `Logout` URL （`SingleLogoutService` 信赖方元数据中的 URL），这的 `BindingType` 假定为 `Http-Redirect`。

对于使用 SAML 测试应用程序的本教程，将 `logoutUrl` 设置为 `https://samltestapp2.azurewebsites.net/logout`：

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. 更新应用程序代码

最后一步是在 SAML 信赖方应用程序中启用作为 SAML IdP 的 Azure AD B2C。 每个应用程序是不同的，执行此操作的步骤会有所不同。 有关详细信息，请参阅应用的文档。

通常需要以下部分或全部内容：

* **元数据**： `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **颁发者**： `https://tenant-name.onmicrosoft.com/policy-name`
* **登录 url/saml 终结点/Saml url**：检查元数据文件中的值
* **证书**：此项*B2C_1A_SamlIdpCert*，但没有私钥。 获取证书的公钥：

    1. 请参阅上面指定的元数据 URL。
    1. 复制 `<X509Certificate>` 元素中的值。
    1. 将其粘贴到文本文件中。
    1. 将该文本文件另存为 *.cer*文件。

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 SAML 测试应用的测试（可选）

若要完成本教程，请使用我们的[SAML 测试应用程序][samltest]：

* 更新租户名称
* 更新策略名称，例如*B2C_1A_signup_signin_saml*
* 指定此颁发者 URI： `https://contoso.onmicrosoft.com/app-name`

选择 "**登录**"，你应该会看到一个最终用户登录屏幕。 登录时，SAML 断言会退回到示例应用程序。

## <a name="sample-policy"></a>示例策略

我们提供了一个可用于测试 SAML 测试应用的完整示例策略。

1. 下载[SAML SP 启动的登录示例策略](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. 更新 `TenantId` 以匹配租户名称，例如*contoso.b2clogin.com*
1. 保留策略名称*B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>支持和不支持的 SAML 情态

通过你自己的元数据终结点支持以下 SAML 信赖方（RP）方案：

* 在应用程序/服务主体对象中注销 URL 的多个注销 Url 或公告。
* 指定用于验证应用程序/服务主体对象中 RP 请求的签名密钥。
* 在应用程序/服务主体对象中指定令牌加密密钥。
* 预览版本当前不支持标识提供者发起的登录。

## <a name="next-steps"></a>后续步骤

你可以[在 OASIS 网站上找到有关 SAML 协议](https://www.oasis-open.org/)的详细信息。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
