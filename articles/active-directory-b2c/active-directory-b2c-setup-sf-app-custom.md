---
title: 在 Azure Active Directory B2C 中使用自定义策略添加 Salesforce SAML 提供程序 | Microsoft Docs
description: 了解如何创建和管理 Azure Active Directory B2C 自定义策略。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f4399730755c15fe5e171bf7fd5826c2b7ffea0a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709642"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C：使用 Salesforce 帐户通过 SAML 登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用[自定义策略](active-directory-b2c-overview-custom.md)从特定 Salesforce 组织为用户设置登录。

## <a name="prerequisites"></a>先决条件

### <a name="azure-ad-b2c-setup"></a>Azure AD B2C 设置

确保已完成演示如何在 Azure Active Directory B2C (Azure AD B2C) 中[开始使用自定义策略](active-directory-b2c-get-started-custom.md)的所有步骤。

其中包括：

* 创建 Azure AD B2C 租户。
* 创建 Azure AD B2C 应用程序。
* 注册两个策略引擎应用程序。
* 设置密钥。
* 设置初学者包。

### <a name="salesforce-setup"></a>Salesforce 设置

本文假设已执行了以下操作：

* 已注册一个 Salesforce 帐户。 可以注册[免费的 Developer Edition 帐户](https://developer.salesforce.com/signup)。
* 为 Salesforce 组织[设置“我的域”](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

## <a name="set-up-salesforce-so-users-can-federate"></a>设置 Salesforce，以便用户可以进行联合

若要帮助 Azure AD B2C 与 Salesforce 进行通信，需要获取 Salesforce 元数据 URL。

### <a name="set-up-salesforce-as-an-identity-provider"></a>将 Salesforce 设置为标识提供者

> [!NOTE]
> 在本文中，假设使用 [Salesforce Lightning 体验](https://developer.salesforce.com/page/Lightning_Experience_FAQ)。

1. [登录到 Salesforce](https://login.salesforce.com/)。 
2. 在左侧菜单中的“设置”下面，展开“标识”，然后单击“标识提供者”。
3. 单击“启用标识提供者”。
4. 在“选择证书”下，选择希望 Salesforce 用于与 Azure AD B2C 进行通信的证书。 （可以使用默认证书。）单击“ **保存**”。 

### <a name="create-a-connected-app-in-salesforce"></a>在 Salesforce 中创建连接的应用

1. 在“标识提供者”页上，转到“服务提供程序”。
2. 单击“现在通过连接的应用创建服务提供程序”**。** 请单击此处”。
3. 在“基本信息”下，为连接的应用输入所需值。
4. 在“Web 应用设置”下，选中“启用 SAML”复选框。
5. 在“实体 ID”字段中，输入以下 URL。 确保替换 `tenantName` 的值。
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. 在“ACS URL”字段中，输入以下 URL。 确保替换 `tenantName` 的值。
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. 对所有其他设置保留默认值。
8. 滚动到列表底部，然后单击“保存”。

### <a name="get-the-metadata-url"></a>获取元数据 URL

1. 在连接的应用的概述页上，单击“管理”。
2. 复制“元数据发现终结点”的值，然后保存它。 在本文后面部分将使用它。

### <a name="set-up-salesforce-users-to-federate"></a>设置 Salesforce 用户以进行联合

1. 在连接的应用的“管理”页上，转到“配置文件”。
2. 单击“管理配置文件”。
3. 选择要与 Azure AD B2C 联合的配置文件（或用户组）。 以系统管理员身份，选中“系统管理员”复选框，以便可以使用 Salesforce 帐户进行联合。

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>为 Azure AD B2C 生成签名证书

发送到 Salesforce 的请求需要由 Azure AD B2C 进行签名。 若要生成签名证书，请打开 Azure PowerShell，然后运行以下命令。

> [!NOTE]
> 确保在前两行中更新租户名称和密码。

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>将 SAML 签名证书添加到 Azure AD B2C

将签名证书上传到 Azure AD B2C 租户： 

1. 转到 Azure AD B2C 租户。 单击“设置” > “标识体验框架” > “策略密钥”。
2. 单击“+添加”，然后：
    1. 单击“选项” > “上传”。
    2. 输入“名称”（例如 SAMLSigningCert）。 前缀 B2C_1A_ 会自动添加到密钥名称。
    3. 若要选择你的证书，请选择“上传文件控件”。 
    4. 输入在 PowerShell 脚本中设置的证书密码。
3. 单击“创建”。
4. 验证是否创建了密钥（例如 B2C_1A_SAMLSigningCert）。 记下完整名称（包括 B2C_1A_）。 后面会在策略中引用此密钥。

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>在基本策略中创建 Salesforce SAML 声明提供程序

需要将 Salesforce 定义为声明提供程序，以便用户可以使用 Salesforce 登录。 换而言之，需指定要与 Azure AD B2C 通信的终结点。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定用户是否已完成身份验证。 为此，请在策略的扩展文件中添加 Salesforce 的 `<ClaimsProvider>`：

1. 在工作目录中，打开扩展文件 (TrustFrameworkExtensions.xml)。
2. 找到 `<ClaimsProviders>` 节。 如果该节不存在，请在根节点的下面创建它。
3. 添加新 `<ClaimsProvider>`：

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

在 `<ClaimsProvider>` 节点下：

1. 将 `<Domain>` 的值更新为可用于区分 `<ClaimsProvider>` 与其他标识提供者的唯一值。
2. 将 `<DisplayName>` 的值更新为声明提供程序的显示名称。 目前不会使用此值。

### <a name="update-the-technical-profile"></a>更新技术配置文件

若要从 Salesforce 获取 SAML 令牌，请定义 Azure AD B2C 与 Azure Active Directory (Azure AD) 通信时使用的协议。 在 `<ClaimsProvider>` 的 `<TechnicalProfile>` 元素中执行此操作：

1. 更新 `<TechnicalProfile>` 节点的 ID。 此 ID 用于从策略的其他部分引用此技术配置文件。
2. 更新 `<DisplayName>` 的值。 此值会显示在登录页中的登录按钮上。
3. 更新 `<Description>` 的值。
4. Salesforce 使用 SAML 2.0 协议。 确保 `<Protocol>` 的值是 **SAML2**。

更新前面 XML 中的 `<Metadata>` 节以反映特定 Salesforce 帐户的设置。 在 XML 中，更新元数据值：

1. 将 `<Item Key="PartnerEntity">` 的值更新为前面复制的 Salesforce 元数据 URL。 它具有以下格式： 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. 在 `<CryptographicKeys>` 节中，将 `StorageReferenceId` 的两个实例的值更新为签名证书（例如，B2C_1A_SAMLSigningCert）的密钥的名称。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

策略现在已配置，从而 Azure AD B2C 知道如何与 Salesforce 进行通信。 请尝试上传策略的扩展文件，以验证到目前为止是否未出现任何问题。 上传策略的扩展文件：

1. 在 Azure AD B2C 租户中，转到“所有策略”边栏选项卡。
2. 选中“覆盖策略(如果存在)”复选框。
3. 上传扩展文件 (TrustFrameworkExtensions.xml)。 确保它可通过验证。

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>将 Salesforce SAML 声明提供程序注册到用户旅程

现在，将 Salesforce SAML 标识提供者添加到用户旅程之一。 此时，标识提供者已设置，但不会出现在任何用户注册或登录页中。 若要将标识提供者添加到登录页，首先请创建现有模板用户之旅的副本。 然后修改模板，以便它还具有 Azure AD 标识提供者。

1. 打开策略的基文件（例如 TrustFrameworkBase.xml）。
2. 找到 `<UserJourneys>` 元素并复制整个 `<UserJourney>` 值（包括 Id=”SignUpOrSignIn”）。
3. 打开扩展文件（例如 TrustFrameworkExtensions.xml）。 找到 `<UserJourneys>` 元素。 如果该元素不存在，请创建一个。
4. 将复制的整个 `<UserJourney>` 粘贴为 `<UserJourneys>` 元素的子级。
5. 重命名新 `<UserJourney>` 的 ID（例如 SignUpOrSignUsingContoso）。

### <a name="display-the-identity-provider-button"></a>显示标识提供者按钮

`<ClaimsProviderSelection>` 元素类似于注册或登录页上的标识提供者按钮。 为 Salesforce 添加 `<ClaimsProviderSelection>` 元素后，当用户进入此页时，会显示一个新按钮。 显示标识提供者按钮：

1. 在创建的 `<UserJourney>` 中，找到采用 `Order="1"` 设置的 `<OrchestrationStep>`。
2. 添加以下 XML：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. 将 `TargetClaimsExchangeId` 设置为逻辑值。 我们建议遵循其他元素使用的相同约定（例如 \[ClaimProviderName\]Exchange）。

### <a name="link-the-identity-provider-button-to-an-action"></a>将标识提供者按钮链接到操作

准备好标识提供者按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作与 Salesforce 通信以接收 SAML 令牌。 为此，可以链接 Salesforce SAML 声明提供程序的技术配置文件：

1. 在 `<UserJourney>` 节点中，找到采用 `Order="2"` 设置的 `<OrchestrationStep>`。
2. 添加以下 XML：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. 将 `Id` 更新为前面用于 `TargetClaimsExchangeId` 的相同值。
4. 将 `TechnicalProfileReferenceId` 更新为前面创建的技术配置文件的 `Id`（例如 ContosoProfile）。

### <a name="upload-the-updated-extension-file"></a>上传更新的扩展文件

现已完成修改扩展文件。 保存并上传次文件。 确保所有验证都成功。

### <a name="update-the-relying-party-file"></a>更新信赖方文件

接下来，更新用于启动创建的用户旅程的信赖方 (RP) 文件：

1. 在工作目录中创建 SignUpOrSignIn.xml 的副本。 然后，重命名它（例如 SignUpOrSignInWithAAD.xml）。
2. 打开新文件，并使用唯一值更新 `<TrustFrameworkPolicy>` 的 `PolicyId` 属性。 这会是策略的名称（例如 SignUpOrSignInWithAAD）。
3. 修改 `<DefaultUserJourney>` 中的 `ReferenceId` 属性，使其与创建的新用户旅程的 `Id` 匹配（例如 SignUpOrSignUsingContoso）。
4. 保存更改，然后上传文件。

## <a name="test-and-troubleshoot"></a>测试和故障排除

若要测试刚刚上传的自定义策略，请在 Azure 门户中，转到策略边栏选项卡，然后单击“立即运行”。 如果失败，请参阅[自定义策略故障排除](active-directory-b2c-troubleshoot-custom.md)。

## <a name="next-steps"></a>后续步骤

向 [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) 提供反馈。
