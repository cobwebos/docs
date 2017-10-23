---
title: "Azure Active Directory B2C：使用自定义策略添加 Twitter 作为 OAuth1 标识提供者"
description: "使用 OAuth1 协议将 Twitter 用作标识提供者的示例"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/02/2017
ms.author: yoelh
ms.openlocfilehash: 8d7bd2ed1cba62677a7eff19ba7018cd25a4345c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略添加 Twitter 作为 OAuth1 标识提供者
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示了如何通过使用[自定义策略](active-directory-b2c-overview-custom.md)，使用户能够从 Twitter 帐户进行登录。

## <a name="prerequisites"></a>先决条件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

这些步骤包括：

1.  创建 Twitter 帐户应用程序。
2.  将 Twitter 帐户应用程序密钥添加到 Azure AD B2C
3.  向策略添加声明提供程序
4.  向用户旅程注册 Twitter 帐户声明提供程序
5.  将策略上传到 Azure AD B2C 租户，并对其进行测试

## <a name="step-1-create-a-twitter-account-application"></a>步骤 1：创建 Twitter 帐户应用程序
要将 Twitter 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Twitter 应用程序并向其提供合适的参数。 可以通过以下地址注册一个 Twitter 应用程序：[https://twitter.com/signup](https://twitter.com/signup)

1.  导航到 [Twitter 开发人员](https://apps.twitter.com/)网站，使用 Twitter 帐户凭据登录，并单击“创建新应用”。

    ![Twitter 帐户 - 创建新应用](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2.  为新应用输入“名称”和“描述”。 粘贴 `https://login.microsoftonline.com` 作为“网站”值。 然后，对于“回调 URL”，粘贴 `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`。 将 {tenant} 替换为你的租户的名称（例如 contosob2c.onmicrosoft.com）。 请确保使用的是 HTTPS 方案。 在页面底部，请阅读并接受条款。 然后单击“创建 Twitter 应用程序”。

    ![Twitter 帐户 - 添加新应用](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3.  单击“设置”选项卡，选中“允许使用此应用程序进行 Twitter 登录”，并单击“更新设置”。
4.  选择“密钥和访问令牌”选项卡。记下“使用者密钥(API 密钥)”和“使用者机密(API 机密)”的值。

    ![Twitter 帐户 - 设置应用程序属性](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

> [!NOTE]
>
>使用者机密是一个重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。

## <a name="step-2-add-the-twitter-account-application-key-to-azure-ad-b2c"></a>步骤 2：将 Twitter 帐户应用程序密钥添加到 Azure AD B2C
使用 Twitter 帐户进行的联合身份验证需要 Twitter 帐户的客户端机密才能代表应用程序信任 Azure AD B2C。 需要将 Twitter 应用程序的使用者机密存储在 Azure AD B2C 租户中：  

1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“手动”。
5.  使用 `TwitterSecret` 作为“名称”。  
    可能会自动添加前缀 `B2C_1A_`。
6.  在“机密”框中，从 https://apps.dev.microsoft.com 输入你的 Microsoft 应用程序机密
7.  使用“加密”作为“密钥用法”。
8.  单击“创建” 
9.  确认已创建密钥 `B2C_1A_TwitterSecret`。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>步骤 3：在扩展策略中添加声明提供程序

如果希望用户使用 Twitter 帐户进行登录，则需要将 Twitter 定义为声明提供程序。 换言之，需指定要与 Azure AD B2C 通信的终结点。 终结点提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 `<ClaimsProvider>` 节点，将 Twitter 定义为声明提供程序：

1.  从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。 
2.  找到 `<ClaimsProviders>` 节
3.  将下面的 XML 代码片段添加到 `<ClaimsProviders>` 节点下：  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4.  将 `client_id` 值替换为你的 Twitter 帐户应用程序的使用者密钥

5.  保存文件。

## <a name="step-4-register-the-twitter-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>步骤 4：注册 Twitter 帐户声明提供程序以注册或登录用户旅程
至此，已设置标识提供者。 但是，它不能在任何注册/登录屏幕中使用。 现在，需要将 Twitter 帐户标识提供者添加到用户 `SignUpOrSignIn` 用户旅程。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>步骤 4.1 创建用户旅程的副本
为使其可用，需创建现有模板用户旅程的副本。 然后，添加 Twitter 标识提供者：

> [!NOTE]
>
>如果已将策略基本文件中的 `<UserJourneys>` 元素复制到扩展文件 (TrustFrameworkExtensions.xml)，则可以跳到下一部分。

1.  打开策略的基文件（例如 TrustFrameworkBase.xml）。
2.  找到 `<UserJourneys>` 元素并复制整个 `<UserJourneys>` 节点的内容。
3.  打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
4.  将复制的整个 `<UserJournesy>` 节点的内容粘贴为 `<UserJourneys>` 元素的子级。

### <a name="step-42-display-the-button"></a>步骤 4.2 显示“按钮”
`<ClaimsProviderSelections>` 元素定义声明提供程序选择选项的列表及其顺序。  `<ClaimsProviderSelection>` 元素类似于注册/登录页上的标识提供者按钮。 如果为 Twitter 帐户添加 `<ClaimsProviderSelection>` 元素，则当用户进入页面时，会显示一个新按钮。 添加此元素：

1.  在刚刚复制的用户旅程中找到包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 节点。
2.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
3.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>步骤 4.3 将按钮链接到操作
准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Twitter 帐户通信以接收令牌。 可通过链接 Twitter 帐户声明提供程序的技术配置文件来将按钮链接到操作：

1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    > [!NOTE]
    >
    > * 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值
    > * 确保将 `TechnicalProfileReferenceId` ID 设置为前面创建的技术配置文件 (Twitter-OAUTH1)。

## <a name="step-5-upload-the-policy-to-your-tenant"></a>步骤 5：将策略上传到租户
1.  在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，单击“Azure AD B2C”。
2.  选择“标识体验框架”。
3.  单击“所有策略”。
4.  选择“上传策略”
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>步骤 6：使用“立即运行”测试自定义策略

1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。

    > [!NOTE]
    >
    >“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3.  应当能够使用 Twitter 帐户进行登录。

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-profile-edit-user-journey"></a>步骤 7：[可选] 向配置文件编辑用户旅程注册 Twitter 帐户声明提供程序
此外，你可能需要将 Twitter 帐户标识提供者添加到用户 `ProfileEdit` 用户旅程。 若要使其可用，请重复步骤 #4。 这次，选择包括 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。 保存、上传并测试你的策略。


## <a name="download-the-complete-policy-files"></a>下载完整的策略文件
可选：建议在完成自定义策略入门演练后，使用你自己的自定义策略文件来构建方案，而不是使用这些示例文件。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)