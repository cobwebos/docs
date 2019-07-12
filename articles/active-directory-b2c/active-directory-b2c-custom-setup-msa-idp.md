---
title: 将 Microsoft 帐户 (MSA) 添加为标识提供程序在 Azure Active Directory B2C 中使用自定义策略
description: 使用 OpenID Connect (OIDC) 协议将 Microsoft 用作标识提供者的示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654149"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略设置使用 Microsoft 帐户的登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文展示了如何在 Azure Active Directory (Azure AD) B2C 中使用[自定义策略](active-directory-b2c-overview-custom.md)为来自 Microsoft 帐户的用户实现登录。

## <a name="prerequisites"></a>先决条件

- 完成 [Azure Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。
- 如果还没有 Microsoft 帐户，请在 [https://www.live.com/](https://www.live.com/) 上创建一个。

## <a name="add-an-application"></a>添加应用程序

若要启用使用 Microsoft 帐户的用户进行登录，需要 Azure AD 租户中注册应用程序。 Azure AD 租户与 Azure AD B2C 租户不同。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请务必使用包含 Azure AD 租户的目录，具体方法是：单击顶部菜单中的“目录和订阅筛选器”  ，再选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”   。
1. 选择“新注册”。 
1. 输入应用程序的**名称**。 例如，MSAapp1  。
1. 下**支持的帐户类型**，选择**中的任何组织的目录和个人 Microsoft 帐户 (例如 Skype，Xbox、 Outlook.com) 帐户**。
1. 下 **（可选） 重定向 URI**，选择**Web**并输入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`在文本框中。 替换为`your-tenant-name`与 Azure AD B2C 租户的名称。
1. 选择**注册**
1. 记录**应用程序 （客户端） ID**的应用程序概述页上显示。 在后面的部分配置的声明提供程序时，您需要它。
1. 选择**证书和机密**
1. 单击**新客户端机密**
1. 输入**描述**机密，例如*MSA 应用程序客户端机密*，然后单击**添加**。
1. 记录应用程序密码中所示**值**列。 下一节中使用此值。

## <a name="create-a-policy-key"></a>创建策略密钥

现在，已在 Azure AD 租户中创建应用程序，需要将该应用程序的客户端机密存储在 Azure AD B2C 租户。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
1. 在“概述”页上选择“标识体验框架”  。
1. 选择“策略密钥”  ，然后选择“添加”  。
1. 对于“选项”  ，请选择 `Manual`。
1. 输入策略密钥的**名称**。 例如， `MSASecret` 。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在中**机密**，输入在上一部分中所记录的客户端机密。
1. 在“密钥用法”处选择 `Signature`。 
1. 单击“创建”。 

## <a name="add-a-claims-provider"></a>添加声明提供程序

若要让用户使用 Microsoft 帐户登录，需要帐户定义为声明提供程序与进行通信的 Azure AD B2C 可以通过终结点。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中添加 **ClaimsProvider** 元素。

1. 打开*TrustFrameworkExtensions.xml*策略文件。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 值替换**client_id**与 Azure AD 应用程序的*应用程序 （客户端） ID*之前记录。
1. 保存该文件。

现在已配置策略，因此 Azure AD B2C 知道如何与 Azure AD 中的 Microsoft 帐户应用程序进行通信。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

在继续之前，将上传修改后的策略，以确认它没有任何问题为止。

1. 导航到在 Azure 门户并选择 Azure AD B2C 租户**标识体验框架**。
1. 上**自定义策略**页上，选择**上传自定义策略**。
1. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件  。
1. 单击“上传” 。 

如果没有错误显示在门户中，转到下一节。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，你已设置标识提供者，但尚不可用在任何注册或登录屏幕。 若要使其可用，创建的现有模板用户旅程，然后对其进行修改，以便它还具有 Microsoft 帐户标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 重命名用户旅程的 ID。 例如， `SignUpSignInMSA` 。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册或登录屏幕上的标识提供者按钮。 如果您将添加**ClaimsProviderSelection**用户进入页面时，将显示一个 Microsoft 帐户，一个新按钮的元素。

1. 在 *TrustFrameworkExtensions.xml* 文件中，在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
1. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `MicrosoftAccountExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 操作，在这种情况下，为 Azure AD b2c 以接收令牌的 Microsoft 帐户与进行通信。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
1. 添加以下 **ClaimsExchange** 元素，确保在 ID 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    更新的值**TechnicalProfileReferenceId**的相匹配`Id`中的值**技术配置文件**元素前面添加的声明提供程序。 例如， `MSA-OIDC` 。

1. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

与 Azure AD B2C 的通信是通过在 Azure AD B2C 租户中创建的应用程序。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
1. 选择“应用程序”，然后选择“添加”   。
1. 输入应用程序的名称，例如 *testapp1*。
1. 对于“Web 应用/Web API”，请选择 `Yes`，然后为“回复 URL”输入 `https://jwt.ms`   。
1. 单击“创建”。 

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInMSA.xml*。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如， `SignUpSignInMSA` 。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_msa`
1. 更新的值**ReferenceId**属性中**DefaultUserJourney**创建早期 (SignUpSignInMSA) 的用户旅程的 ID 匹配。
1. 保存更改并上传文件，然后选择列表中的新策略。
1. 请确保你在上一节中创建的 Azure AD B2C 应用程序 (或通过完成必备组件，例如*webapp1*或*testapp1*) 中选择**选择应用程序**字段，然后再对其进行测试通过单击**立即运行**。
1. 选择**的 Microsoft 帐户**按钮，然后登录。

    如果在登录操作成功，将重定向到`jwt.ms`显示解码的令牌，类似于：

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
