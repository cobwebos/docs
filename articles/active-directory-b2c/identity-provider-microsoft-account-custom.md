---
title: 使用自定义策略使用 Microsoft 帐户帐户设置登录
titleSuffix: Azure AD B2C
description: 如何使用自定义策略启用 Microsoft 帐户 （MSA） 作为使用 OpenID 连接 （OIDC） 协议的身份提供商。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188111"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略设置使用 Microsoft 帐户的登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用 Azure 活动目录 B2C （Azure AD B2C） 中的[自定义策略](custom-policy-overview.md)为 Microsoft 帐户的用户启用登录。

## <a name="prerequisites"></a>先决条件

- 完成 [Azure Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的步骤。
- 如果您还没有 Microsoft 帐户，请在 创建[https://www.live.com/](https://www.live.com/)一个帐户。

## <a name="register-an-application"></a>注册应用程序

要为使用 Microsoft 帐户的用户启用登录，需要在 Azure AD 租户中注册应用程序。 Azure AD 租户与 Azure AD B2C 租户不同。

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 通过在顶部菜单中选择**目录 + 订阅**筛选器并选择包含 Azure AD 租户的目录，请确保使用的目录包含 Azure AD 租户。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择“新注册”。****
1. 输入应用程序的**名称**。 例如，MSAapp1**。
1. 在 **"支持帐户类型**"下，选择**任何组织目录中的帐户和个人 Microsoft 帐户（例如 Skype、Xbox、Outlook.com）。**
1. 在**重定向 URI（可选）** 下，选择 **"Web"** 并在文本框中输入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 替换为`your-tenant-name`Azure AD B2C 租户名称。
1. 选择 **"注册"**
1. 记录应用程序概述页上显示**的应用程序（客户端）ID。** 在后续部分中配置声明提供程序时，需要这样做。
1. 选择**证书&机密**
1. 单击 **"新客户端"机密**
1. 输入机密**的说明**，例如*MSA 应用程序客户端密钥*，然后单击"**添加**"。
1. 记录 **"值"** 列中显示的应用程序密码。 在下一节中使用此值。

## <a name="configuring-optional-claims"></a>配置可选声明

如果要从 Azure AD `family_name` `given_name`获取 和 声明，可以在 Azure 门户 UI 或应用程序清单中为应用程序配置可选声明。 有关详细信息，请参阅[如何向 Azure AD 应用提供可选声明](../active-directory/develop/active-directory-optional-claims.md)。

1. 登录到 Azure[门户](https://portal.azure.com)。 搜索并选择“Azure Active Directory”****。
1. 在“管理”部分，选择“应用注册”。********
1. 在列表中选择要为其配置可选声明的应用程序。
1. 在“管理”部分，选择“令牌配置(预览)”。********
1. 选择“添加可选声明”。****
1. 选择要配置的令牌类型。
1. 选择要添加的可选声明。
1. 单击 **“添加”**。

## <a name="create-a-policy-key"></a>创建策略密钥

现在，您已经在 Azure AD 租户中创建了应用程序，您需要在 Azure AD B2C 租户中存储该应用程序的客户端机密。

1. 登录到 Azure[门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录****。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“概述”页上选择“标识体验框架”****。
1. 选择“策略密钥”****，然后选择“添加”****。
1. 对于“选项”****，请选择 `Manual`。
1. 输入策略密钥的**名称**。 例如，`MSASecret` 。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在 **"机密**"中，输入您在上一节中记录的客户端机密。
1. 在“密钥用法”处选择 `Signature`。****
1. 单击 **“创建”**。

## <a name="add-a-claims-provider"></a>添加声明提供程序

要使用户能够使用 Microsoft 帐户登录，您需要将帐户定义为 Azure AD B2C 可通过终结点与其通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中添加 **ClaimsProvider** 元素。

1. 打开*信任框架扩展.xml*策略文件。
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
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. 将**client_id**的值替换为前面记录的 Azure AD*应用程序（客户端）ID。*
1. 保存文件。

现在，您已经配置了策略，以便 Azure AD B2C 知道如何与 Azure AD 中的 Microsoft 帐户应用程序进行通信。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

在继续之前，请上载修改后的策略，以确认到目前为止没有任何问题。

1. 导航到 Azure 门户中的 Azure AD B2C 租户，然后选择**标识体验框架**。
1. 在**自定义策略**页上，选择 **"上载自定义策略**"。
1. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件****。
1. 单击 **“上载”**。

如果门户中未显示任何错误，请继续执行下一节。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，您已设置标识提供程序，但它尚未在任何注册或登录屏幕中可用。 要使其可用，请创建现有模板用户旅程的重复项，然后对其进行修改，以便它还必须具有 Microsoft 帐户标识提供程序。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 重命名用户旅程的 ID。 例如，`SignUpSignInMSA` 。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册或登录屏幕上的标识提供者按钮。 如果为 Microsoft 帐户添加**声明提供程序选择**元素，则当用户落在页面上时将显示一个新按钮。

1. 在 *TrustFrameworkExtensions.xml* 文件中，在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
1. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `MicrosoftAccountExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在这种情况下，操作是 Azure AD B2C 与 Microsoft 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
1. 添加以下 **ClaimsExchange** 元素，确保在 ID 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    更新**技术配置文件基准Id**的值，以匹配前面添加的声明`Id`提供程序**的技术配置文件**元素中的值的值。 例如，`MSA-OIDC` 。

1. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过您在 B2C 租户中注册的应用程序与 Azure AD B2C 的通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInMSA.xml*。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInMSA` 。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_msa`
1. 更新**默认用户旅程**中的**参考 Id**属性的值，以匹配您之前创建的用户旅程的 ID（SignUpSignInMSA）。
1. 保存更改并上传文件，然后选择列表中的新策略。
1. 请确保在上一部分中创建的 Azure AD B2C 应用程序（或通过完成先决条件（例如*Webapp1*或*testapp1）* 在 **"选择应用程序**"字段中被选中，然后通过单击"**立即运行"** 来测试它。
1. 选择 **"微软帐户"** 按钮并登录。

    如果登录操作成功，您将被重定向到`jwt.ms`显示解码令牌的标记，类似于：

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
