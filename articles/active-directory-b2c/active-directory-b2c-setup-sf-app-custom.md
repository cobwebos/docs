---
title: 在 Azure Active Directory B2C 中使用自定义策略设置使用 Salesforce SAML 提供程序的登录 | Microsoft Docs
description: 在 Azure Active Directory B2C 中使用自定义策略设置使用 Salesforce SAML 提供程序的登录。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 920d5924271d31848b455ebece9e4832548e1b06
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846398"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略设置使用 Salesforce SAML 提供程序的登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文展示了如何在 Azure Active Directory (Azure AD) B2C 中使用[自定义策略](active-directory-b2c-overview-custom.md)为来自 Salesforce 组织的用户实现登录。 可以通过将 [SAML 技术配置文件](saml-technical-profile.md)添加到自定义策略来实现登录。

## <a name="prerequisites"></a>先决条件

- 完成 [Azure Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。
- 如果尚未注册，请注册一个[免费的 Developer Edition 帐户](https://developer.salesforce.com/signup)。 本文使用 [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ)。
- 为 Salesforce 组织[设置“我的域”](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

### <a name="set-up-salesforce-as-an-identity-provider"></a>将 Salesforce 设置为标识提供者

1. [登录到 Salesforce](https://login.salesforce.com/)。 
2. 在左侧菜单中的“设置”下面，展开“标识”，然后选择“标识提供者”。
3. 选择“启用标识提供者”。
4. 在“选择证书”下，选择希望 Salesforce 用于与 Azure AD B2C 进行通信的证书。 可以使用默认证书。 
5. 单击“ **保存**”。 

### <a name="create-a-connected-app-in-salesforce"></a>在 Salesforce 中创建连接的应用

1. 在“标识提供者”页面上，选择“现在通过已连接应用创建服务提供程序”。请单击此处”。
2. 在“基本信息”下，为连接的应用输入所需值。
3. 在“Web 应用设置”下，选中“启用 SAML”复选框。
4. 在“实体 ID”字段中，输入以下 URL。 确保将 `your-tenant` 的值替换为你的 Azure AD B2C 租户的名称。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. 在“ACS URL”字段中，输入以下 URL。 确保将 `your-tenant` 的值替换为你的 Azure AD B2C 租户的名称。
      
      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. 滚动到列表底部，然后单击“保存”。

### <a name="get-the-metadata-url"></a>获取元数据 URL

1. 在连接的应用的概述页上，单击“管理”。
2. 复制“元数据发现终结点”的值，然后保存它。 在本文后面部分将使用它。

### <a name="set-up-salesforce-users-to-federate"></a>设置 Salesforce 用户以进行联合

1. 在连接的应用的“管理”页上，单击“管理配置文件”。
2. 选择要与 Azure AD B2C 联合的配置文件（或用户组）。 以系统管理员身份，选中“系统管理员”复选框，以便可以使用 Salesforce 帐户进行联合。

## <a name="generate-a-signing-certificate"></a>生成签名证书

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

## <a name="create-a-policy-key"></a>创建策略密钥

你需要将已创建的证书存储在 Azure AD B2C 租户中。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 在“概述”页上，选择“标识体验框架 - 预览”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Upload`。
7. 输入策略的**名称**。 例如，SAMLSigningCert。 前缀 `B2C_1A_` 将自动添加到你的密钥的名称中。
8. 浏览找到并选择你创建的 B2CSigningCert.pfx 证书。 
9. 输入证书的密码。
3. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Salesforce 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。 

可以通过在策略的扩展文件中将 Salesforce 帐户添加到 **ClaimsProvider** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

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
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

4. 将 **PartnerEntity** 的值更新为前面复制的 Salesforce 元数据 URL。
5. 将 **StorageReferenceId** 的两个实例的值更新为你的签名证书的密钥。 例如，B2C_1A_SAMLSigningCert。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现在，你已配置了策略，因此 Azure AD B2C 知道如何与 Salesforce 帐户进行通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
3. 单击“上传” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册或登录屏幕中。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 Salesforce 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInSalesforce`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册或登录屏幕上的标识提供者按钮。 如果为 LinkedIn 帐户添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在刚才创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `SalesforceExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Salesforce 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，确保在 **Id** 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```
    
    将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 **Id**。 例如，`LinkedIn-OAUTH`。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过在租户中创建的应用程序与 Azure AD B2C 进行通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“应用程序”，然后选择“添加”。
5. 输入应用程序的名称，例如 *testapp1*。
6. 对于“Web 应用/Web API”，请选择 `Yes`，然后为“回复 URL”输入 `https://jwt.ms`。
7. 单击“创建”。

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动刚才创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInSalesforce.xml*。
2. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInSalesforce`。
3. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_salesforce`
4. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignInSalesforce)。
5. 保存更改并上传文件，然后选择列表中的新策略。
6. 确保在“选择应用程序”字段选择你创建的 Azure AD B2C 应用程序，然后单击“立即运行”对其进行测试。
