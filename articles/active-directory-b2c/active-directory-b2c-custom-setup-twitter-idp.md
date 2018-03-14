---
title: "Azure Active Directory B2C：使用自定义策略添加 Twitter 作为 OAuth1 标识提供者"
description: "使用 OAuth1 协议将 Twitter 用作标识提供者"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: 629e0bbaa7c62ef5d381085588c6a99c203c41cb
ms.sourcegitcommit: 694e40a193980dea1e2f945471071f11030d5641
ms.translationtype: MT Enhanced
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略添加 Twitter 作为 OAuth1 标识提供者
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍了如何使用[自定义策略](active-directory-b2c-overview-custom.md)，使 Twitter 帐户用户能够登录。

## <a name="prerequisites"></a>必备组件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

## <a name="step-1-create-a-twitter-account-application"></a>步骤 1：创建 Twitter 帐户应用程序
要将 Twitter 用作 Azure Active Directory B2C (Azure AD B2C) 中的标识提供者，必须创建一个 Twitter 应用程序并向其提供合适的参数。 可以转到 [Twitter 注册页](https://twitter.com/signup)来注册 Twitter 应用程序。

1. 转到 [Twitter 开发人员](https://apps.twitter.com/)网站，使用 Twitter 帐户凭据进行登录，然后选择“创建新应用”。

    ![Twitter 帐户 - 创建新应用](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. 在“创建应用”窗口中，执行以下操作：
 
    a. 为新应用键入**名称**和**说明**。 

    b. 在“网站”框中，粘贴 **https://login.microsoftonline.com**。 

    c. 在“回调 URL”框中，粘贴 **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**。 将 {*tenant*} 替换为你自己的租户名称（例如 contosob2c.onmicrosoft.com）。 请务必使用 HTTPS 方案。 

    d.单击“下一步”。 在页面底部，阅读并接受条款，然后选择“创建 Twitter 应用程序”。

    ![Twitter 帐户 - 添加新应用](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. 在“B2C 演示”窗口中，选择“设置”，选中“允许使用此应用程序通过 Twitter 进行登录”复选框，然后选择“更新设置”。

4. 选择“密钥和访问令牌”，记下“使用者密钥(API 密钥)”、“使用者机密(API 机密)”值。

    ![Twitter 帐户 - 设置应用程序属性](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >使用者机密是一个非常重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>步骤 2：将 Twitter 帐户应用程序密钥添加到 Azure AD B2C
使用 Twitter 帐户进行的联合身份验证需要 Twitter 帐户的使用者机密才能代表应用程序信任 Azure AD B2C。 若要将 Twitter 应用程序的使用者机密存储在 Azure AD B2C 租户中，请执行以下操作： 

1. 在 Azure AD B2C 租户中，选择“B2C 设置” > “标识体验框架”。

2. 若要查看租户中的可用密钥，请选择“策略密钥”。

3. 选择“添加”。

4. 在“选项”框中，选择“手动”。

5. 在“名称”框中，选择“TwitterSecret”。  
    可能会自动添加前缀 *B2C_1A_*。

6. 在“机密”框中，输入从[应用程序注册门户](https://apps.dev.microsoft.com)获取的 Microsoft 应用程序机密。

7. 使用“加密”作为“密钥用法”。

8. 选择“创建”。

9. 确认已创建 `B2C_1A_TwitterSecret` 密钥。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>步骤 3：在扩展策略中添加声明提供程序

如果希望用户使用 Twitter 帐户进行登录，必须将 Twitter 定义为声明提供程序。 换言之，必须指定要与 Azure AD B2C 通信的终结点。 终结点提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 `<ClaimsProvider>` 节点，将 Twitter 定义为声明提供程序：

1. 在工作目录中，打开 *TrustFrameworkExtensions.xml* 扩展策略文件。 

2. 搜索 `<ClaimsProviders>` 部分。

3. 在 `<ClaimsProviders>` 节点中添加以下 XML 代码片段：  

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

4. 将 *client_id* 值替换为你的 Twitter 帐户应用程序使用者密钥。

5. 保存文件。

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>步骤 4：将 Twitter 帐户声明提供程序注册到注册或登录用户旅程
你已设置了标识提供者。 不过，它尚不可在任何注册或登录窗口中使用。 现在，必须将 Twitter 帐户标识提供者添加到用户 `SignUpOrSignIn` 用户旅程。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>步骤 4.1：创建用户旅程的副本
若要使用户旅程可用，需创建现有用户旅程模板的副本，然后添加 Twitter 标识提供者：

>[!NOTE]
>如果已将策略基本文件中的 `<UserJourneys>` 元素复制到 *TrustFrameworkExtensions.xml* 扩展文件，则可以跳到下一部分。

1. 打开策略的基文件（例如 TrustFrameworkBase.xml）。

2. 搜索 `<UserJourneys>` 元素，选择 `<UserJourney>` 节点的整个内容，再选择“剪切”将所选文本移动到剪贴板。

3. 打开扩展文件（例如 TrustFrameworkExtensions.xml），并搜索 `<UserJourneys>` 元素。 如果该元素不存在，请添加该元素。

4. 将 `<UserJourney>` 节点的整个内容（已在步骤 2 中移动到剪贴板）粘贴到 `<UserJourneys>` 元素。

### <a name="step-42-display-the-button"></a>步骤 4.2：显示“按钮”
`<ClaimsProviderSelections>` 元素定义声明提供程序选择选项的列表及其顺序。 `<ClaimsProviderSelection>` 节点类似于注册或登录页上的标识提供者按钮。 如果为 Twitter 帐户添加了 `<ClaimsProviderSelection>` 节点，则当用户进入页面时，会显示一个新按钮。 若要添加此元素，请执行以下操作：

1. 在复制的用户旅程中搜索包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 节点。

2. 定位到包含 `Order="1"` 的 `<OrchestrationStep>` 节点。

3. 在 `<ClaimsProviderSelections>` 元素中添加以下 XML 代码片段：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>步骤 4.3：将按钮链接到操作
准备好按钮后，必须将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Twitter 帐户通信以接收令牌。 可通过链接 Twitter 帐户声明提供程序的技术配置文件来将按钮链接到操作：

1. 在 `<UserJourney>` 节点中搜索包含 `Order="2"` 的 `<OrchestrationStep>` 节点。
2. 在 `<ClaimsExchanges>` 元素中添加以下 XML 代码片段：

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值。
    >* 确保 `TechnicalProfileReferenceId` ID 设置为前面创建的技术配置文件 (Twitter-OAUTH1)。

## <a name="step-5-upload-the-policy-to-your-tenant"></a>步骤 5：将策略上传到租户
1. 在 [Azure 门户](https://portal.azure.com)中，切换到[你的 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后选择“Azure AD B2C”。

2. 选择“标识体验框架”。

3. 选择“所有策略”。

4. 选择“上传策略”。

5. 选中“覆盖策略(如果存在)”复选框。

6. 上传 *TrustFrameworkBase.xml* 和 *TrustFrameworkExtensions.xml* 文件，并确保它们可通过验证。

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>步骤 6：使用“立即运行”测试自定义策略

1. 选择“Azure AD B2C 设置”，然后选择“标识体验框架”。

    >[!NOTE]
    >“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2. 打开已上传的信赖方 (RP) 自定义策略 **B2C_1A_signup_signin**，然后选择“立即运行”。  
    现在，应当能够使用 Twitter 帐户进行登录。

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>步骤 7：（可选）将 Twitter 帐户声明提供程序注册到配置文件编辑用户旅程
可能还需要将 Twitter 帐户标识提供者添加到 `ProfileEdit` 用户旅程。 若要使用户旅程可用，请重复“步骤 4”。 这次，请选择包含 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。 保存、上传并测试策略。


## <a name="optional-download-the-complete-policy-files"></a>（可选）下载完整的策略文件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)演练后，我们建议你使用自己的自定义策略文件来构建方案。 我们提供了[示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)供你参考。
