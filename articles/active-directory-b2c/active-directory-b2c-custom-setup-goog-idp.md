---
title: "Azure Active Directory B2C：使用自定义策略添加 Google+ 作为 OAuth2 标识提供者"
description: "使用 OAuth2 协议将 Google+ 用作标识提供者的示例"
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
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 54bf10acfb885042278c4457a70ec86248c96c1c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略添加 Google+ 作为 OAuth2 标识提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本指南演示如何通过使用[自定义策略](active-directory-b2c-overview-custom.md)，使用户能够从 Google+ 帐户进行登录。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

这些步骤包括：

1.  创建 Google+ 帐户应用程序。
2.  将 Google+ 帐户应用程序密钥添加到 Azure AD B2C
3.  向策略添加声明提供程序
4.  向用户旅程注册 Google+ 帐户声明提供程序
5.  将策略上传到 Azure AD B2C 租户，并对其进行测试

## <a name="create-a-google-account-application"></a>创建 Google+ 帐户应用程序
要将 Google+ 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Google+ 应用程序并向其提供合适的参数。 可以在此处注册 Google+ 应用程序：[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  导航到 [Google 开发人员控制台](https://console.developers.google.com/)，并使用 Google+ 帐户凭据登录。
2.  单击“创建项目”，输入**项目名称**，并单击“创建”。

3.  单击“项目菜单”。

    ![Google+ 帐户 - 选择项目](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  单击“+”按钮。

    ![Google+ 帐户 - 创建新项目](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  输入“项目名称”，并单击“创建”。

    ![Google+ 帐户 - 新项目](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  等待直到项目准备就绪，然后单击“项目菜单”。

    ![Google+ 帐户 - 等待直到新项目可供使用](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  单击项目名称。

    ![Google+ 帐户 - 选择新项目](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  单击“API 管理器”，并在左侧导航区域中单击“凭据”。
9.  单击顶部的“OAuth 许可屏幕”选项卡。

    ![Google+ 帐户 - 设置 OAuth 许可屏幕](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  选择或指定一个有效的**电子邮件地址**，提供**产品名称**，并单击“保存”。

    ![Google+ - 应用程序凭据](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  单击“新建凭据”，并选择“OAuth 客户端 ID”。

    ![Google+ - 创建新的应用程序凭据](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  在“应用程序类型”下，选择“Web 应用程序”。

    ![Google+ - 选择应用程序类型](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  提供应用程序的**名称**，在**已授权 JavaScript 来源**字段中输入 `https://login.microsoftonline.com`，并在**已授权重定向 URI** 字段输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为租户名称（例如 contosob2c.onmicrosoft.com）。 **{tenant}** 值区分大小写。 单击“创建” 。

    ![Google + - 提供授权的 JavaScript 源和重定向 URI](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  复制“客户端 ID”和“客户端密钥”的值。 将 Google+ 配置为租户中的标识提供者时需要这两个值。 **客户端密码**是一个重要的安全凭据。

    ![Google+ - 复制“客户端 ID”和“客户端密钥”的值](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>将 Google+ 帐户应用程序密钥添加到 Azure AD B2C
与 Google+ 帐户的联合身份验证要求 Google+ 帐户的客户端密码代表应用程序信任 Azure AD B2C。 需要在 Azure AD B2C 租户中存储 Google + 应用程序密码：  

1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“手动”。
5.  使用 `GoogleSecret` 作为“名称”。  
    可能会自动添加前缀 `B2C_1A_`。
6.  在“机密”框中，输入前面从 [Google 开发人员控制台](https://console.developers.google.com/)复制的 Google 应用程序机密。
7.  使用“签名”作为“密钥用法”。
8.  单击“创建” 
9.  确认已创建密钥 `B2C_1A_GoogleSecret`。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>在扩展策略中添加声明提供程序

如果要允许用户使用 Google+ 帐户登录，则需将 Google+ 帐户定义为声明提供程序。 换言之，需指定要与 Azure AD B2C 通信的终结点。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 `<ClaimsProvider>` 节点，将 Google+ 帐户定义为声明提供程序：

1.  从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。 如果需要 XML 编辑器，请[尝试使用 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型跨平台编辑器）。
2.  找到 `<ClaimsProviders>` 节
3.  在保存文件之前，将下面的 XML 代码段添加到 `ClaimsProviders` 元素下，并使用 Google+ 帐户应用程序客户机 ID 替换 `client_id` 值。  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>注册 Google+ 帐户声明提供程序以注册或登录用户旅程

已设置标识提供者。  但是，它不能在任何注册/登录屏幕中使用。 将 Google+ 帐户标识提供者添加到用户 `SignUpOrSignIn` 用户旅程。 为使其可用，需创建现有模板用户旅程的副本。  然后，添加 Google+ 帐户标识提供者：

>[!NOTE]
>
>如果已将策略基本文件中的 `<UserJourneys>` 元素复制到扩展文件 (TrustFrameworkExtensions.xml)，则可以跳到本部分。

1.  打开策略的基文件（例如 TrustFrameworkBase.xml）。
2.  找到 `<UserJourneys>` 元素并复制整个 `<UserJourneys>` 节点的内容。
3.  打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
4.  将复制的整个 `<UserJournesy>` 节点的内容粘贴为 `<UserJourneys>` 元素的子级。

### <a name="display-the-button"></a>显示按钮
`<ClaimsProviderSelections>` 元素定义声明提供程序选择选项的列表及其顺序。  `<ClaimsProviderSelection>` 元素类似于注册/登录页上的标识提供者按钮。 如果为 Google+ 帐户添加 `<ClaimsProviderSelection>` 元素，则当用户进入页面时，会显示一个新按钮。 添加此元素：

1.  在刚刚复制的用户旅程中找到包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 节点。
2.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
3.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作
准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Google+ 帐户通信以接收令牌。

1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值
> * 确保 `TechnicalProfileReferenceId` ID 设置为前面创建的技术配置文件 (Google-OAUTH)。

## <a name="upload-the-policy-to-your-tenant"></a>将策略上传到租户
1.  在 [Azure 门户](https://portal.azure.com) 中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md) ，然后打开“Azure AD B2C”边栏选项卡。
2.  选择“标识体验框架”。
3.  打开“所有策略”边栏选项卡。
4.  选择“上传策略”。
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="test-the-custom-policy-by-using-run-now"></a>使用“立即运行”测试自定义策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。

    >[!NOTE]
    >
    >    “立即运行”需要在租户中至少预先注册一个应用程序。 
    >    在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。


2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3.  应能够使用 Google+ 帐户登录。

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[可选]向配置文件编辑用户旅程注册 Google+ 帐户声明提供程序
此外，你可能想要将 Google+ 帐户标识提供者添加到用户 `ProfileEdit` 用户旅程。 为使其可用，我们将重复最后两个步骤：

### <a name="display-the-button"></a>显示按钮
1.  打开策略的扩展文件（例如 TrustFrameworkExtensions.xml）。
2.  在刚刚复制的用户旅程中找到包含 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。
3.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
4.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作
1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>使用“立即运行”测试自定义配置文件编辑策略

1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。
2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_ProfileEdit”。 选择“立即运行”。
3.  应能够使用 Google+ 帐户登录。

## <a name="download-the-complete-policy-files"></a>下载完整的策略文件
可选：建议在完成自定义策略入门演练后，使用你自己的自定义策略文件来构建方案，而不是使用这些示例文件。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
