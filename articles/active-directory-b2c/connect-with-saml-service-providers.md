---
title: 将 Azure AD B2C 作为 SAML IdP 配置到应用程序
title-suffix: Azure AD B2C
description: 如何配置 Azure AD B2C 以向应用程序（服务提供程序）提供 SAML 协议断言。 Azure AD B2C 将充当 SAML 应用程序的单个标识提供者 (IdP)。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 9e67f24cf670024432f64487df20b9fca515c006
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91740371"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 SAML 应用程序

本文介绍如何配置 Azure Active Directory B2C (Azure AD B2C)，以将其用作应用程序的安全断言标记语言 (SAML) 标识提供者 (IdP)。

## <a name="scenario-overview"></a>方案概述

使用 Azure AD B2C 作为其客户标识和访问管理解决方案的组织可能需要与配置为使用 SAML 协议进行身份验证的标识提供者或应用程序交互。

Azure AD B2C 通过以下两种方式之一实现 SAML 互操作性：

* 充当标识提供者 (IdP) 并使用基于 SAML 的服务提供程序（应用程序）实现单一登录 (SSO)
* 充当服务提供程序 (SP) 并与基于 SAML 的标识提供者（如 Salesforce 和 ADFS）进行交互

![关系图，其中左侧 B2C 为标识提供者，右侧为服务提供程序](media/saml-identity-provider/saml-idp-diagram-01.jpg)

用 SAML 汇总两个非独占核心方案：

| 场景 | Azure AD B2C 角色 | 操作说明 |
| -------- | ----------------- | ------- |
| 我的应用程序需要 SAML 断言才能完成身份验证。 | **Azure AD B2C 充当标识提供者 (IdP)**<br />Azure AD B2C 充当应用程序的 SAML IdP。 | 本文。 |
| 我的用户需要使用与 SAML 兼容的标识提供程序（如 ADFS、Salesforce 或 Shibboleth）进行单一登录。  | **Azure AD B2C 充当服务提供程序 (SP)**<br />当连接到 SAML 标识提供者时，Azure AD B2C 充当服务提供商。 它是应用程序与 SAML 标识提供者之间的联合代理。  | <ul><li>[通过自定义策略将使用 ADFS 登录设置为 SAML IdP](identity-provider-adfs2016-custom.md)</li><li>[通过自定义策略设置使用 Salesforce SAML 提供程序进行的登录](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>先决条件

* 完成 [Azure AD B2C 中的自定义策略](custom-policy-get-started.md)中的步骤。 你需要本文讨论的自定义策略入门包中的 SocialAndLocalAccounts 自定义策略。
* 对安全断言标记语言 (SAML) 协议的基本了解。
* 一个配置为 SAML 服务提供程序 (SP) 的 Web 应用。 对于本教程，你可以使用我们提供的 [SAML 测试应用程序][samltest]。

## <a name="components-of-the-solution"></a>解决方案组件

此方案需要三个主要组件：

* SAML 服务提供程序能够发送 SAML 请求，并且能够接收、解码和响应来自 Azure AD B2C 的 SAML 声明。 这也称为信赖方。
* 服务提供程序可公共获取的 SAML 元数据终结点。
* [Azure AD B2C 租户](tutorial-create-tenant.md)

如果你还没有 SAML 服务提供程序和关联的元数据终结点，则可以使用我们已提供的此示例 SAML 应用程序用于测试：

[SAML 测试应用程序][samltest]

## <a name="1-set-up-certificates"></a>1.设置证书

若要在服务提供程序与 Azure AD B2C 之间建立信任关系，需要提供 Web 应用 X509 证书。

* **服务提供程序证书**
  * 该证书具有存储在 Web 应用中的私钥。 服务提供程序使用此证书对发送到 Azure AD B2C 的 SAML 请求进行签名。 Azure AD B2C 读取服务提供程序元数据中的公钥来验证签名。
  * （可选）该证书具有存储在 Web 应用中的私钥。 Azure AD B2C 读取服务提供程序元数据中的公钥来加密 SAML 断言。 然后，服务提供程序使用私钥来解密断言。
* **Azure AD B2C 证书**
  * 具有 Azure AD B2C 中私钥的证书。 Azure AD B2C 使用此证书对发送到服务提供程序的 SAML 响应进行签名。 服务提供程序读取 Azure AD B2C 的元数据公钥以验证 SAML 响应的签名。

你可以使用由公共证书颁发机构颁发的证书，或者对于本教程，可以使用自签名证书。

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 准备自签名证书

如果你还没有证书，则可以在本教程中使用自签名证书。 在 Windows 上，可使用 PowerShell 的 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet 来生成证书。

1. 执行此 PowerShell 命令来生成自签名证书。 根据应用程序和 Azure AD B2C 租户名称修改 `-Subject` 参数。 还可调整 `-NotAfter` 日期，为证书指定不同的过期日期。

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

1. 打开“管理用户证书” > “当前用户” > “个人” > “证书” > “yourappname.yourtenant.onmicrosoft.com”   
1. 选择该证书 >“操作” > “所有任务” > “导出”  
1. 选择“是” > “下一步” > “是，导出私钥” > “下一步”   
1. 接受“导出文件格式”的默认值
1. 提供证书的密码

### <a name="12-upload-the-certificate"></a>1.2 上传证书

接下来，将 SAML 断言和响应签名证书上传到 Azure AD B2C。

1. 登录到 [Azure 门户](https://portal.azure.com)浏览到 Azure AD B2C 租户。
1. 在“策略”下，选择“Identity Experience Framework”，然后选择“策略私钥”  。
1. 选择“添加”，然后选择“选项” > “上传”  
1. 输入“名称”（例如 SamlIdpCert） 前缀 B2C_1A_ 会自动添加到密钥名称。
1. 使用上传文件控件上传证书。
1. 输入证书的密码。
1. 选择“创建”。
1. 验证密钥是否按预期方式显示。 例如，B2C_1A_SamlIdpCert。

## <a name="2-prepare-your-policy"></a>2.准备策略

### <a name="21-create-the-saml-token-issuer"></a>2.1 创建 SAML 令牌颁发者

现在，使用 [SAML令牌颁发者](saml-issuer-technical-profile.md)和 [SAML会话提供者](custom-policy-reference-sso.md#samlssosessionprovider)技术配置文件，为租户添加颁发 SAML 令牌的功能。

打开自定义策略新手包中的 `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** 。

找到 `<ClaimsProviders>` 部分并添加以下 XML 片段。

可以更改 `IssuerUri` 元数据的值。 这是 Azure AD B2C 在 SAML 响应中返回的颁发者 URI。 应将信赖方应用程序配置为：在 SAML 断言验证期间接受颁发者 URI。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3.添加 SAML 信赖方策略

现在，你的租户可以传送 SAML 断言，你需要创建 SAML 信赖方策略，并修改用户旅程，使其传送 SAML 断言而不是 JWT。

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 创建注册或登录策略

1. 在新手包工作目录中创建 SignUpOrSignin.xml 文件的副本，并使用新名称保存该副本。 例如，SignUpOrSigninSAML.xml。 这是信赖方策略文件。

1. 在首选编辑器中打开“SignUpOrSigninSAML.xml”文件。

1. 如下所示，将策略的 `PolicyId` 和 `PublicPolicyUri` 更改为 B2C_1A_signup_signin_saml 和 `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`。

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 在 `<RelyingParty>` 元素之前添加以下 XML 代码段。 此 XML 将覆盖 SignUpOrSignIn 用户旅程的业务流程步骤编号 7。 如果从新手包中的其他文件夹开始，或者通过添加或删除业务流程来自定义用户旅程，请确保该数字（在 `order` 元素中）与令牌颁发者的用户旅程步骤中指定的数字一致（例如，在其他新手包文件夹中，`LocalAccounts` 的步骤编号为 4，`SocialAccounts` 的步骤编号为 6，`SocialAndLocalAccountsWithMfa` 的步骤编号为 9）。

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. 将 `<RelyingParty>` 元素中的整个 `<TechnicalProfile>` 元素替换为以下技术配置文件 XML。

    ```xml
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

1. 将 `tenant-name` 更新为 Azure AD B2C 租户的名称。

最终的信赖方策略文件应如下所示：

```xml
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

> [!NOTE]
> 当实现其他类型的用户流 (例如登录、密码重置或配置文件编辑) 时，该过程实质上与本部分中所述的过程相同。 在上面的步骤4中，将用户旅程的最后一个步骤从更改 `JWTIssuer` 为 `Saml2AssertionIssuer` 。 在上面的步骤6中，在 "信赖方" 部分中，将 **协议** 从更改 `OpenIdConnect` 为 `SAML2` 。

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 上传和测试策略元数据

保存更改并上传新的策略文件。 上传两个策略（扩展和信赖方文件）后，打开 Web 浏览器并导航到策略元数据。

Azure AD B2C 策略 IDP 元数据是 SAML 协议中用于公开 SAML 标识提供者配置的信息。 元数据定义服务的位置，例如登录和注销、证书、登录方法和其他信息。 以下 URL 提供了 Azure AD B2C 策略元数据。 将 `tenant-name` 替换为 Azure AD B2C 租户的名称，并将 `policy-name` 替换为策略的名称 (ID)：

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

你的自定义策略和 Azure AD B2C 租户现已准备就绪。 接下来，在 Azure AD B2C 中创建应用程序注册。

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4.在 Azure AD B2C 目录中安装应用程序

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4.1 在 Azure AD B2C 中注册应用程序

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，SAMLApp1。
1. 在“支持的帐户类型”下，选择“仅此组织目录中的帐户” 
1. 在“重定向 URI”下，选择“Web”，然后输入 `https://localhost`。 稍后会在应用程序注册的清单中修改此值。
1. 选择“注册”。

### <a name="42-update-the-app-manifest"></a>4.2 更新应用清单

对于 SAML 应用，需要在应用程序注册的清单中配置几个属性。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到在上一节中创建的应用程序注册。
1. 在“管理”下，选择“清单”以打开清单编辑器。 你需要在以下部分中修改几个属性。

#### <a name="identifieruris"></a>identifierUris

`identifierUris` 是一个字符串集合，其中包含用户定义的 URI，这些 URI 唯一标识其 Azure AD B2C 租户中的 Web 应用。 服务提供程序必须在 SAML 请求的 `Issuer` 元素中设置此值。

#### <a name="samlmetadataurl"></a>samlMetadataUrl

此属性表示服务提供程序的公开可用的元数据 URL。 元数据 URL 可以指向已上传到任何可匿名访问的终结点（例如 blob 存储）的元数据文件。

元数据是 SAML 协议中用于公开 SAML 方（例如服务提供程序）的配置的信息。 元数据定义服务（如登录和注销、证书、登录方法和其他信息）的位置。 Azure AD B2C 读取服务提供程序元数据并进行相应的操作。 元数据不是必需的。 你还可以直接在应用清单中指定某些属性，如回复 URI 和注销 URI。

如果同时在 SAML 元数据 URL 和应用程序注册的清单中指定了一些属性，则这些属性将合并。 会优先处理元数据 URL 中指定的属性，其优先级更高。

对于使用 SAML 测试应用程序的本教程，请对 `samlMetadataUrl` 使用以下值：

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType（可选）

如果不提供元数据 URI，则可以显式指定回复 URL。 此可选属性表示 `AssertionConsumerServiceUrl`（服务提供程序元数据中的 `SingleSignOnService` URL），并且 `BindingType` 默认为 `HTTP POST`。

如果在应用程序清单中配置回复 URL 和注销 URL 时选择不使用服务提供程序元数据，则 Azure AD B2C 将不会验证 SAML 请求签名，也不会加密 SAML 响应。

对于本教程中使用 SAML 测试应用程序，请将 `replyUrlsWithType` 的 `url` 属性设置为以下 JSON 代码段中显示的值。

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl（可选）

此可选属性表示 `Logout` URL（依赖方元数据中的 `SingleLogoutService` URL），该属性的 `BindingType` 默认为 `Http-Redirect`。

对于本教程中使用 SAML 测试应用程序，请将 `logoutUrl` 设置为 `https://samltestapp2.azurewebsites.net/logout`：

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5.更新应用程序代码

最后一步是在 SAML 信赖方应用中将 Azure AD B2C 启用为 SAML IdP。 对于不同的应用程序，执行此操作的步骤也会有所不同。 有关详细信息，请参阅应用的文档。

通常需要以下部分或全部内容：

* **元数据**：`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **颁发者**： 在元数据文件中使用 entityID
* **登录 URL/SAML 终结点/SAML URL**：检查元数据文件中的值
* **证书：** 为 B2C_1A_SamlIdpCert ，但没有私钥。 若要获取证书的公钥：

    1. 请转到上面指定的元数据 URL。
    1. 复制 `<X509Certificate>` 元素中的值。
    1. 将其粘贴到文本文件中。
    1. 将该文本文件另存为 .cer 文件。

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 使用 SAML 测试应用进行测试（可选）

使用 [SAML 测试应用程序][samltest]完成本教程：

* 更新租户名称
* 更新策略名称，例如 B2C_1A_signup_signin_saml
* 指定此颁发者 URI：`https://contoso.onmicrosoft.com/app-name`

选择“登录”，然后会显示用户登录屏幕。 登录后，SAML 断言会发回到示例应用程序。

## <a name="enable-encrypted-assertions-optional"></a>启用加密断言 (可选) 

若要加密发送回服务提供程序的 SAML 断言，Azure AD B2C 将使用服务提供程序公钥证书。 公钥必须存在于上述 ["samlMetadataUrl"](#samlmetadataurl) 中所述的 SAML 元数据中，作为 KeyDescriptor，使用 "Encryption"。

下面是 SAML 元数据 KeyDescriptor 的一个示例，其中使用设置为 "加密"：

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

若要启用 Azure AD B2C 发送加密的断言，请将 **WantsEncryptedAssertion** metadata 项设置为 `true` [信赖方技术配置文件](relyingparty.md#technicalprofile)中的。 你还可以配置用于加密 SAML 断言的算法。 有关详细信息，请参阅 [信赖方技术配置文件元数据](relyingparty.md#metadata)。 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>启用标识提供者启动的流 (可选) 

在标识提供程序启动的流中，由标识提供程序启动的登录进程 (Azure AD B2C) ，后者将未经请求的 SAML 响应发送到的信赖方应用) 程序的服务提供 (商。 若要启用标识提供者启动的流，请将 " **IdpInitiatedProfileEnabled** metadata" 项设置为 `true` " [信赖方技术配置文件](relyingparty.md#technicalprofile)"。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

若要登录或通过标识提供者启动的流注册用户，请使用以下 URL：

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login
```

请替换以下值：

* **租户-名称** 与租户名称
* **策略-** 包含 SAML 信赖方策略名称的名称

## <a name="sample-policy"></a>示例策略

我们提供了一个完整示例策略，可用于通过 SAML 测试应用进行测试。

1. 下载 [SAML-SP 启动的登录示例策略](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. 更新 `TenantId` 以匹配租户名称，例如 contoso.b2clogin.com
1. 保留策略名称 B2C_1A_SAML2_signup_signin

## <a name="supported-and-unsupported-saml-modalities"></a>受支持的和不支持的 SAML 形式

通过你自己的元数据终结点支持以下 SAML 信赖方 (RP) 方案：

* 在应用程序/服务主体对象中的多个注销 URL 或注销 URL 的 POST 绑定。
* 在应用程序/服务主体对象中指定用于验证 RP 请求的签名密钥。
* 在应用程序/服务主体对象中指定令牌加密密钥。
* 标识提供者发起的登录，Azure AD B2C 标识提供者。

当前不支持以下 SAML 信赖方 (RP) 方案：
* 标识提供者发起的登录，其中标识提供者是外部标识提供者，例如 ADFS。

## <a name="next-steps"></a>后续步骤

- 可以[在 OASIS 网站上找到有关 SAML 协议](https://www.oasis-open.org/)的详细信息。
- 从 [Azure AD B2C GitHub 社区存储库](https://github.com/azure-ad-b2c/saml-sp-tester)获取 SAML 测试 Web 应用。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
