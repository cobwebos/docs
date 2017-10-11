---
title: "Azure Active Directory B2C：使用自定义策略添加 Microsoft 帐户 (MSA) 作为标识提供者"
description: "使用 OpenID Connect (OIDC) 协议将 Microsoft 用作标识提供者的示例"
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
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 8c981046ff41d3927ff60d6dc4f40366ae25ba74
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略添加 Microsoft 帐户 (MSA) 作为标识提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示如何通过使用[自定义策略](active-directory-b2c-overview-custom.md)，使用户能够从 Microsoft 帐户 (MSA) 进行登录。

## <a name="prerequisites"></a>必备组件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

这些步骤包括：

1.  创建 Microsoft 帐户应用程序。
2.  将 Microsoft 帐户应用程序密钥添加到 Azure AD B2C
3.  向策略添加声明提供程序
4.  向用户旅程注册 Microsoft 帐户声明提供程序
5.  将策略上传到 Azure AD B2C 租户，并对其进行测试

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序
要将 Microsoft 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Microsoft 帐户应用程序并向其提供合适的参数。 需要一个 Microsoft 帐户。 如果没有此帐户，可以访问 [https://www.live.com/](https://www.live.com/)。

1.  转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，并使用 Microsoft 帐户凭据登录。
2.  单击“添加应用程序”。

    ![Microsoft 帐户 - 添加应用](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  提供应用程序的“名称”、“联系人电子邮件”，取消选中“让我们帮助你开始入门”，并单击“创建”。

    ![Microsoft 帐户 - 注册应用程序](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  复制“应用程序 Id”的值。将 Microsoft 帐户配置为租户中的标识提供者时需要此项。

    ![Microsoft 帐户 - 复制“应用程序 ID”的值](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  单击“添加平台”

    ![Microsoft 帐户 — 添加平台](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  从平台列表中，选择“Web”。

    ![Microsoft 帐户 - 从平台列表选择 Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  在“重定向 URI”字段中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为租户名称（例如 contosob2c.onmicrosoft.com）。

    ![Microsoft 帐户 - 设置重定向 URL](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  单击“应用程序密码”部分下的“生成新密码”。 复制屏幕上显示的新密码。 将 Microsoft 帐户配置为租户中的标识提供者时需要此项。 该密码是一个非常重要的安全凭据。

    ![Microsoft 帐户 — 生成新密码](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Microsoft 帐户 - 复制新密码](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  选中“高级选项”部分下的“Live SDK 支持”复选框。 单击“保存” 。

    ![Microsoft 帐户 — Live SDK 支持](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>将 Microsoft 帐户应用程序密钥添加到 Azure AD B2C
与 Microsoft 帐户的联合身份验证要求 Microsoft 帐户的客户端密码代表应用程序信任 Azure AD B2C。 需要在 Azure AD B2C 租户中存储 Microsoft 帐户应用程序密码：   

1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“手动”。
5.  使用 `MSASecret` 作为“名称”。  
    可能会自动添加前缀 `B2C_1A_`。
6.  在“密码”框中，从 https://apps.dev.microsoft.com 输入你的 Microsoft 应用程序密码
7.  使用“签名”作为“密钥用法”。
8.  单击“创建” 
9.  确认已创建密钥 `B2C_1A_MSASecret`。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>在扩展策略中添加声明提供程序
如果要允许用户使用 Microsoft 帐户登录，则需将 Microsoft 帐户定义为声明提供程序。 换言之，需指定要与 Azure AD B2C 通信的终结点。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 `<ClaimsProvider>` 节点，将 Microsoft 帐户定义为声明提供程序：

1.  从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。 如果需要 XML 编辑器，请[尝试使用 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型跨平台编辑器）。
2.  找到 `<ClaimsProviders>` 节
3.  将下面的 XML 代码段添加到 `ClaimsProviders` 元素下：

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
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
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

4.  使用 Microsoft 帐户应用程序客户端 ID 来替换 `client_id` 值

5.  保存文件。

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>注册 Microsoft 帐户声明提供程序以注册或登录用户旅程

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 现在需要将 Microsoft 帐户标识提供者添加到用户 `SignUpOrSignIn` 用户旅程。 为使其可用，需创建现有模板用户旅程的副本。  然后，添加 Microsoft 帐户标识提供者：

> [!NOTE]
>
>如果此前已将策略基本文件中的 `<UserJourneys>` 元素复制到扩展文件 `TrustFrameworkExtensions.xml`，则可以跳到本部分。

1.  打开策略的基文件（例如 TrustFrameworkBase.xml）。
2.  找到 `<UserJourneys>` 元素并复制整个 `<UserJourneys>` 节点的内容。
3.  打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
4.  将复制的整个 `<UserJournesy>` 节点的内容粘贴为 `<UserJourneys>` 元素的子级。

### <a name="display-the-button"></a>显示按钮
`<ClaimsProviderSelections>` 元素定义声明提供程序选择选项的列表及其顺序。  `<ClaimsProviderSelection>` 元素类似于注册/登录页上的标识提供者按钮。 如果为 Microsoft 帐户添加 `<ClaimsProviderSelection>` 元素，则当用户进入页面时，会显示一个新按钮。 添加此元素：

1.  在刚刚复制的用户旅程中找到包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 节点。
2.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
3.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作
准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Microsoft 帐户通信以接收令牌。 可通过链接 Microsoft 帐户声明提供程序的技术配置文件来将按钮链接到操作：

1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值
>   * 确保 `TechnicalProfileReferenceId` ID 设置为前面创建的技术配置文件 (MSA-OIDC)。

## <a name="upload-the-policy-to-your-tenant"></a>将策略上传到租户
1.  在 [Azure 门户](https://portal.azure.com) 中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md) ，然后打开“Azure AD B2C”边栏选项卡。
2.  选择“标识体验框架”。
3.  打开“所有策略”边栏选项卡。
4.  选择“上传策略”。
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="test-the-custom-policy-by-using-run-now"></a>使用“立即运行”测试自定义策略

1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。
> [!NOTE]
>
>“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。
2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3.  应能够使用 Microsoft 帐户登录。

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[可选]向配置文件编辑用户旅程注册 Microsoft 帐户声明提供程序
此外，你可能需要将 Microsoft 帐户标识提供者添加到用户 `ProfileEdit` 用户旅程。 为使其可用，我们将重复最后两个步骤：

### <a name="display-the-button"></a>显示按钮
1.  打开策略的扩展文件（例如 TrustFrameworkExtensions.xml）。
2.  在刚刚复制的用户旅程中找到包含 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。
3.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
4.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作
1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>使用“立即运行”测试自定义配置文件编辑策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。
2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_ProfileEdit”。 选择“立即运行”。
3.  应能够使用 Microsoft 帐户登录。

## <a name="download-the-complete-policy-files"></a>下载完整的策略文件
可选：建议在完成自定义策略入门演练后，使用你自己的自定义策略文件来构建方案，而不是使用这些示例文件。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
