---
title: "Azure Active Directory B2C：使用自定义策略将 LinkedIn 添加为 OAuth2 标识提供者"
description: "有关如何使用 OAuth2 协议和自定义策略设置 LinkedIn 应用程序的操作指南文章"
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
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略将 LinkedIn 添加为标识提供者
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用[自定义策略](active-directory-b2c-overview-custom.md)，使用户能够从 LinkedIn 帐户登录。

## <a name="prerequisites"></a>先决条件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

这些步骤包括：
1.  创建 LinkedIn 帐户应用程序。
2.  将 LinkedIn 帐户应用程序密钥添加到 Azure AD B2C
3.  向策略添加声明提供程序
4.  向用户旅程注册 LinkedIn 帐户声明提供程序
5.  将策略上传到 Azure AD B2C 租户，并测试该策略

## <a name="step-1-create-a-linkedin-account-application"></a>步骤 1：创建 LinkedIn 帐户应用程序
要将 LinkedIn 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 LinkedIn 应用程序并向其提供合适的参数。 可以通过以下网址注册 LinkedIn 应用程序：[https://LinkedIn.com/signup](https://LinkedIn.com/signup)

1.  导航到 [LinkedIn 应用程序管理](https://www.linkedin.com/secure/developer?newapp=)网站，使用 LinkedIn 帐户凭据登录，单击“创建应用程序”。

    ![LinkedIn 帐户 - 创建应用程序](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  键入“公司名称”，提供应用程序的描述性“名称”和“说明”。
    2.  上传**应用程序徽标**
    3.  选择“应用程序用途”
    4.  粘贴 `https://login.microsoftonline.com` 作为“网站 URL”值。
    5.  键入“办公电子邮件”和“办公电话”
    6.  在页面底部，阅读并接受使用条款。 然后单击“提交”

    ![LinkedIn 帐户 - 配置应用程序属性](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  在菜单中单击“身份验证”。 记下“客户端 ID”和“客户端机密”的值。

    对于“已授权的重定向 URL”，请粘贴 `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`。 将 {tenant} 替换为租户名称（例如 contosob2c.onmicrosoft.com）。 确保使用 HTTPS 方案，单击“添加”

    ![LinkedIn 帐户 - 设置已授权的重定向 URL](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >客户端机密是非常重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。

4.  单击“设置”选项卡，将“应用程序状态”更改为“实时”，单击“更新”。

    ![LinkedIn 帐户 - 设置应用程序状态](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>步骤 2：将 LinkedIn 应用程序密钥添加到 Azure AD B2C
与 LinkedIn 帐户的联合身份验证要求 LinkedIn 帐户的客户端机密代表应用程序信任 Azure AD B2C。 需将 LinkedIn 应用程序机密存储在 Azure AD B2C 租户中：  

1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“手动”。
5.  使用 `LinkedInSecret` 作为“名称”。  
    可能会自动添加前缀 `B2C_1A_`。
6.  在“密码”框中，输入从 https://apps.dev.microsoft.com 获取的 LinkedIn 应用程序机密
7.  使用“加密”作为“密钥用法”。
8.  单击“创建”  
9.  确认已创建密钥 `B2C_1A_LinkedInSecret`。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>步骤 3：在扩展策略中添加声明提供程序
如果希望用户使用 LinkedIn 帐户登录，需将 LinkedIn 定义为声明提供程序。 换言之，需指定要与 Azure AD B2C 通信的终结点。 终结点提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 `<ClaimsProvider>` 节点，将 LinkedIn 定义为声明提供程序：

1.  从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。 
2.  找到 `<ClaimsProviders>` 节
3.  将下面的 XML 代码片段添加到 `<ClaimsProviders>` 节点下：  

  ```xml
  <ClaimsProvider>
    <Domain>linkedin.com</Domain>
    <DisplayName>LinkedIn</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="LinkedIn-OAUTH">
        <DisplayName>LinkedIn</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">linkedin</Item>
          <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
          <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
          <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
          <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
          <Item Key="ClaimsEndpointFormatName">format</Item>
          <Item Key="ClaimsEndpointFormat">json</Item>
          <Item Key="scope">r_emailaddress r_basicprofile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your LinkedIn application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
          <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4.  使用 LinkedIn 应用程序客户端 ID 替换 `client_id` 值
5.  保存文件。

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>步骤 4：注册 LinkedIn 帐户声明提供程序以注册或登录用户旅程
至此，已设置标识提供者。 但是，它不能在任何注册/登录屏幕中使用。 现在需要将 LinkedIn 帐户标识提供者添加到用户 `SignUpOrSignIn` 用户旅程。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>步骤 4.1：创建用户旅程的副本
为使其可用，需创建现有模板用户旅程的副本。 然后，添加 LinkedIn 标识提供者：

> [!NOTE]
>
>如果已将策略基本文件中的 `<UserJourneys>` 元素复制到扩展文件 (TrustFrameworkExtensions.xml)，则可以跳到本部分。

1.  打开策略的基文件（例如 TrustFrameworkBase.xml）。
2.  找到 `<UserJourneys>` 元素并复制整个 `<UserJourneys>` 节点的内容。
3.  打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
4.  将复制的整个 `<UserJournesy>` 节点的内容粘贴为 `<UserJourneys>` 元素的子级。

### <a name="step-42-display-the-button"></a>步骤 4.2：显示“按钮”
`<ClaimsProviderSelections>` 元素定义声明提供程序选择选项的列表及其顺序。  `<ClaimsProviderSelection>` 元素类似于注册/登录页上的标识提供者按钮。 如果为 LinkedIn 帐户添加 `<ClaimsProviderSelection>` 元素，则当用户进入页面时，会显示一个新按钮。 添加此元素：

1.  在刚刚复制的用户旅程中找到包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 节点。
2.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
3.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>步骤 4.3：将按钮链接到操作
准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 LinkedIn 帐户通信以接收令牌。 可通过链接 LinkedIn 帐户声明提供程序的技术配置文件来将按钮链接到操作：

1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值
    > * 确保 `TechnicalProfileReferenceId` ID 设置为前面创建的技术配置文件 (LinkedIn-OAuth)。

## <a name="step-5-upload-the-policy-to-your-tenant"></a>步骤 5：将策略上传到租户
1.  在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，单击“Azure AD B2C”。
2.  选择“标识体验框架”。
3.  单击“所有策略”。
4.  选择“上传策略”。
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>步骤 6：使用“立即运行”测试自定义策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。

    > [!NOTE]
    >
    >“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3.  应能够使用 LinkedIn 帐户登录。

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>步骤 7：[可选] 向配置文件编辑用户旅程注册 LinkedIn 帐户声明提供程序
可能需要将 LinkedIn 帐户标识提供者添加到用户 `ProfileEdit` 用户旅程。 若要使其可用，请重复步骤 #4。 这次，选择包括 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。 保存、上传并测试策略。

## <a name="download-the-complete-policy-files"></a>下载完整的策略文件
可选：建议在完成自定义策略入门演练后，使用你自己的自定义策略文件来构建方案，而不是使用这些示例文件。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)