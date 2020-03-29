---
title: 将 Azure AD B2C 配置为对应用程序的 SAML IdP
title-suffix: Azure AD B2C
description: 如何配置 Azure AD B2C 以向应用程序（服务提供商）提供 SAML 协议断言。 Azure AD B2C 将作为 SAML 应用程序的单个标识提供程序 （IdP）。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051621"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 SAML 应用程序

在本文中，您将了解如何配置 Azure 活动目录 B2C（Azure AD B2C）以充当应用程序的安全断言标记语言 （SAML） 标识提供程序 （IdP）。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>方案概述

使用 Azure AD B2C 作为客户标识和访问管理解决方案的组织可能需要与配置为使用 SAML 协议进行身份验证的标识提供程序或应用程序进行交互。

Azure AD B2C 可通过两种方式之一实现 SAML 互操作性：

* 通过充当*标识提供程序*（IdP）并与基于 SAML 的服务提供商 （您的应用程序） 实现单一登录 （SSO）
* 通过充当*服务提供商*（SP） 并与基于 SAML 的标识提供商（如 Salesforce 和 ADFS）进行交互

![左侧为标识提供程序，右侧为 B2C 作为服务提供商](media/saml-identity-provider/saml-idp-diagram-01.jpg)

使用 SAML 总结两个非独占核心方案：

| 方案 | Azure AD B2C 角色 | 操作说明 |
| -------- | ----------------- | ------- |
| 我的应用程序希望 SAML 断言完成身份验证。 | **Azure AD B2C 充当标识提供程序 （IdP）**<br />Azure AD B2C 充当应用程序的 SAML IdP。 | 本文。 |
| 我的用户需要使用符合 SAML 的标识提供程序（如 ADFS、Salesforce 或 Shibboleth）单一登录。  | **Azure AD B2C 充当服务提供商 （SP）**<br />连接到 SAML 标识提供程序时，Azure AD B2C 充当服务提供商。 它是应用程序和 SAML 标识提供程序之间的联合代理。  | <ul><li>[使用自定义策略将 ADFS 作为 SAML IdP 进行注册](identity-provider-adfs2016-custom.md)</li><li>[使用自定义策略使用 Salesforce SAML 提供程序设置登录](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>先决条件

* 在 Azure [AD B2C 中完成"开始使用自定义策略"中](custom-policy-get-started.md)的步骤。 您需要本文中讨论的自定义策略初学者包中的*社交和本地帐户*自定义策略。
* 基本了解安全断言标记语言 （SAML） 协议。
* 配置为 SAML 服务提供商 （SP） 的 Web 应用程序。 在本教程中，您可以使用我们提供的[SAML 测试应用程序][samltest]。

## <a name="components-of-the-solution"></a>解决方案的构成

此方案需要三个主要组件：

* SAML**服务提供商**能够发送 SAML 请求，并从 Azure AD B2C 接收、解码和响应 SAML 断言。 这也被称为依赖方。
* 服务提供商的公开可用的 SAML**元数据终结点**。
* [Azure AD B2C 租户](tutorial-create-tenant.md)

如果您还没有 SAML 服务提供商和关联的元数据终结点，则可以使用我们提供的可用于测试的此示例 SAML 应用程序：

[SAML 测试应用程序][samltest]

## <a name="1-set-up-certificates"></a>1. 设置证书

要在服务提供商和 Azure AD B2C 之间建立信任关系，您需要提供 Web 应用 X509 证书。

* **服务提供商证书**
  * 在 Web 应用中存储私钥的证书。 此证书由服务提供商用于对发送到 Azure AD B2C 的 SAML 请求进行签名。 Azure AD B2C 从服务提供商元数据读取公钥以验证签名。
  * （可选）在 Web 应用中存储私钥的证书。 Azure AD B2C 从服务提供商元数据读取公钥以加密 SAML 断言。 然后，服务提供商使用私钥解密断言。
* **Azure AD B2C 证书**
  * 具有 Azure AD B2C 中私钥的证书。 此证书由 Azure AD B2C 用于对发送到服务提供商的 SAML 响应进行签名。 服务提供商读取 Azure AD B2C 元数据公钥以验证 SAML 响应的签名。

您可以使用由公共证书颁发机构颁发的证书，或者对于本教程而言，使用自签名证书。

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 准备自签名证书

如果您还没有证书，则可以使用自签名证书进行本教程。 在 Windows 上，您可以使用 PowerShell 的["新自签名证书](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)"cmdlet 生成证书。

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

1. 打开**管理 用户证书** > **当前用户** > **个人** > **证书** > *yourappname.yourtenant.onmicrosoft.com*
1. 选择证书>**操作** > **所有任务** > **导出**
1. 选择 **"是** > **下一个"，** > **导出私钥** > **下一个**
1. 接受**导出文件格式**的默认值
1. 提供证书的密码

### <a name="12-upload-the-certificate"></a>1.2 上传证书

接下来，将 SAML 断言和响应签名证书上载到 Azure AD B2C。

1. 登录到 Azure[门户](https://portal.azure.com)并浏览到 Azure AD B2C 租户。
1. 在 **"策略**"下，选择 **"身份体验框架****"，然后选择策略键**。
1. 选择 **"添加**"，然后选择 **"** > **上载**选项"。
1. 输入**名称**，例如*SamlidpCert*。 前缀 B2C_1A_** 会自动添加到密钥名称。
1. 使用上载文件控件上载证书。
1. 输入证书的密码。
1. 选择 **“创建”**。
1. 验证密钥是否按预期显示。 例如 *，B2C_1A_SamlIdpCert*。

## <a name="2-prepare-your-policy"></a>2. 准备您的保单

### <a name="21-create-the-saml-token-issuer"></a>2.1 创建 SAML 令牌颁发者

现在，使用[SAML 令牌颁发者和 SAML](saml-issuer-technical-profile.md) [会话提供程序](custom-policy-reference-sso.md#samlssosessionprovider)技术配置文件添加租户颁发 SAML 令牌的功能。

在`SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** 自定义策略初学者包中打开。

找到该`<ClaimsProviders>`部分并添加以下 XML 代码段。

您可以更改`IssuerUri`元数据的值。 这是从 Azure AD B2C 返回的 SAML 响应中的颁发者 URI。 应将依赖方应用程序配置为在 SAML 断言验证期间接受颁发者 URI。

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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. 添加 SAML 依赖方政策

现在，租户可以发出 SAML 断言，您需要创建 SAML 依赖方策略，并修改用户旅程，以便发布 SAML 断言而不是 JWT。

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 创建注册或登录策略

1. 在入门包工作目录中创建*SignUpOrSignin.xml*文件的副本，并将其保存为新名称。 例如，*注册或SigninSAML.xml*。 这是您的依赖方策略文件。

1. 在首选编辑器中打开 *"注册或签名"文件。"xml"*

1. 将`PolicyId`策略`PublicPolicyUri`和将更改为_B2C_1A_signup_signin_saml，_`http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`如下所示。

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

1. 在`<RelyingParty>`元素之前添加以下 XML 代码段。 此 XML 覆盖_SignUpOrSign 用户_旅程的业务流程步骤 7。 如果从初学者包中的其他文件夹开始，或者通过添加或删除业务流程步骤来自定义用户旅程，请确保该编号`order`（元素中）与令牌颁发者步骤的用户旅程中指定的号码一致（例如，在其他初学者包文件夹中，其步骤号为`LocalAccounts`4 for`SocialAccounts`和 9。 `SocialAndLocalAccountsWithMfa`

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. 将元素中的`<TechnicalProfile>``<RelyingParty>`整个元素替换为以下技术配置文件 XML。

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

1. 使用`tenant-name`Azure AD B2C 租户的名称进行更新。

您的最终依赖方策略文件应如下所示：

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 上传并测试策略元数据

保存更改并上载新的策略文件。 上载两个策略（扩展名和依赖方文件）后，打开 Web 浏览器并导航到策略元数据。

Azure AD B2C 策略 IDP 元数据是 SAML 协议中用于公开 SAML 标识提供程序配置的信息。 元数据定义服务的位置，例如登录和注销、证书、登录方法和其他信息。 Azure AD B2C 策略元数据可在以下 URL 上使用。 替换为`tenant-name`Azure AD B2C 租户的名称，以及`policy-name`策略的名称 （ID）：

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

自定义策略和 Azure AD B2C 租户现已准备就绪。 接下来，在 Azure AD B2C 中创建应用程序注册。

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Azure AD B2C 目录中的安装程序

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 在 Azure 活动目录中注册应用程序

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”**** 筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”****。 或者，选择“所有服务”**** 并搜索并选择“Azure AD B2C”****。
1. 选择“应用注册(预览版)”，然后选择“新建注册”********。
1. 输入应用程序的“名称”****。 例如 *，SAMLApp1*。
1. 在 **"支持帐户类型**"下，**仅选择此组织目录中的帐户**
1. 在**重定向 URI**下，选择**Web** `https://localhost`，然后输入 。 稍后在应用程序注册的清单中修改此值。
1. 选择“注册”****。

### <a name="42-update-the-app-manifest"></a>4.2 更新应用清单

对于 SAML 应用，需要在应用程序注册的清单中配置多个属性。

1. 在[Azure 门户](https://portal.azure.com)中，导航到您在上一节中创建的应用程序注册。
1. 在 **"管理**"下，选择 **"清单"** 以打开清单编辑器。 在以下部分中修改多个属性。

#### <a name="identifieruris"></a>identifierUris

是`identifierUris`包含用户定义的 URI 的字符串集合，用于唯一标识其 Azure AD B2C 租户中的 Web 应用。 您的服务提供商必须在 SAML 请求的元素`Issuer`中设置此值。

#### <a name="samlmetadataurl"></a>samlMetadataUrl

此属性表示服务提供商的公开可用元数据 URL。 元数据 URL 可以指向上载到任何匿名可访问的终结点（例如 Blob 存储）的元数据文件。

元数据是 SAML 协议中用于公开 SAML 参与方（如服务提供商）配置的信息。 元数据定义服务的位置，如登录和注销、证书、登录方法等。 Azure AD B2C 读取服务提供商元数据并相应地执行操作。 不需要元数据。 您还可以直接在应用清单中指定一些属性，如回复 URI 和注销 URI。

如果 SAML 元数据*both*URL 和应用程序注册的清单中都指定了属性 **，则合并**这些属性。 首先处理元数据 URL 中指定的属性并优先处理。

对于使用 SAML 测试应用程序的本教程，请使用 以下值进行`samlMetadataUrl`：

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>回复 Urls 与类型（可选）

如果不提供元数据 URI，则可以显式指定答复 URL。 此可选属性表示`AssertionConsumerServiceUrl`（`SingleSignOnService`服务提供者元数据中的 URL），`BindingType`假定 为`HTTP POST`。

如果选择在不使用服务提供商元数据的情况下在应用程序清单中配置回复 URL 和注销 URL，则 Azure AD B2C 将不会验证 SAML 请求签名，也不会加密 SAML 响应。

在本教程中，使用 SAML 测试应用程序，将`url`的属性`replyUrlsWithType`设置为以下 JSON 代码段中显示的值。

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>注销 Url（可选）

此可选属性表示`Logout`URL（`SingleLogoutService`依赖方元数据中的 URL），并且假定`BindingType``Http-Redirect`为 。

对于使用 SAML 测试应用程序的本教程，将`logoutUrl`设置为 ： `https://samltestapp2.azurewebsites.net/logout`

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. 更新应用程序代码

最后一步是在 SAML 依赖方应用程序中启用 Azure AD B2C 作为 SAML IdP。 每个应用程序都不同，这样做的步骤也各不相同。 有关详细信息，请参阅应用的文档。

通常需要以下部分或全部：

* **元数据**：`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **颁发者**：使用元数据文件中的实体 ID
* **登录 Url/SAML 终结点/SAML Url**：检查元数据文件中的值
* **证书**：这是*B2C_1A_SamlIdpCert，* 但没有私钥。 要获取证书的公钥，可以：

    1. 转到上面指定的元数据 URL。
    1. 复制元素中`<X509Certificate>`的值。
    1. 将其粘贴到文本文件中。
    1. 将文本文件另存为 *.cer*文件。

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 使用 SAML 测试应用程序进行测试（可选）

要使用我们的[SAML 测试应用程序][samltest]完成本教程：

* 更新租户名称
* 更新策略名称，例如*B2C_1A_signup_signin_saml*
* 指定此颁发者 URI：`https://contoso.onmicrosoft.com/app-name`

选择 **"登录"，** 您应该显示用户登录屏幕。 登录后，SAML 断言将发出回示例应用程序。

## <a name="sample-policy"></a>示例策略

我们提供完整的示例策略，您可以使用该策略与 SAML 测试应用进行测试。

1. 下载[SAML-SP 启动的登录示例策略](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. 更新`TenantId`以匹配租户名称，例如*contoso.b2clogin.com*
1. 保留*B2C_1A_SAML2_signup_signin*的策略名称

## <a name="supported-and-unsupported-saml-modalities"></a>支持和不支持的 SAML 模式

以下 SAML 依赖方 （RP） 方案通过您自己的元数据终结点提供支持：

* 多个注销 URL 或 POST 绑定，用于应用程序/服务主体对象中的注销 URL。
* 指定签名密钥以验证应用程序/服务主体对象中的 RP 请求。
* 在应用程序/服务主体对象中指定令牌加密密钥。
* 预览版本中当前不支持标识提供程序启动的登录。

## <a name="next-steps"></a>后续步骤

- 您可以在 OASIS 网站上找到有关[SAML 协议](https://www.oasis-open.org/)的更多信息。
- 从[Azure AD B2C GitHub 社区存储库](https://github.com/azure-ad-b2c/saml-sp-tester)获取 SAML 测试 Web 应用。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
