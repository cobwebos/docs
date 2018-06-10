---
title: 在 Azure Active Directory B2C 中使用自定义策略将 LinkedIn 添加为 OAuth2 标识提供者 | Microsoft Docs
description: 有关如何使用 OAuth2 协议和自定义策略设置 LinkedIn 应用程序的操作指南文章。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0544daf310d40ea419cb6a46cbbf308ebc924715
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709319"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略将 LinkedIn 添加为标识提供者
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用[自定义策略](active-directory-b2c-overview-custom.md)来让 LinkedIn 帐户用户登录。

## <a name="prerequisites"></a>先决条件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

## <a name="step-1-create-a-linkedin-account-application"></a>步骤 1：创建 LinkedIn 帐户应用程序
若要将 LinkedIn 用作 Azure Active Directory B2C (Azure AD B2C) 中的标识提供者，必须创建 LinkedIn 应用程序并向其提供合适的参数。 可以转到 [LinkedIn 注册页](https://LinkedIn.com/signup)来注册 LinkedIn 应用程序。

1. 转到 [LinkedIn 应用程序管理](https://www.linkedin.com/secure/developer?newapp=)网站，使用 LinkedIn 帐户凭据登录，选择“创建应用程序”。

    ![LinkedIn 帐户 - 创建应用程序](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. 在“创建新应用程序”页上执行以下操作：

    a. 键入**公司名称**、公司的描述性**名称**，以及新应用的**说明**。

    b. 上传**应用程序徽标**。

    c. 选择一种**应用程序用途**。

    d. 在“网站 URL”框中，粘贴 https://login.microsoftonline.com。

    e. 键入**办公电子邮件**地址和**办公电话**号码。

    f. 在页面底部，阅读并接受使用条款，选择“提交”。

    ![LinkedIn 帐户 - 配置应用程序属性](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. 选择“身份验证”，记下“客户端 ID”和“客户端机密”值。

4. 在“已授权的重定向 URL”框中，粘贴 https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp。 将 {*tenant*} 替换为你自己的租户名称（例如 contosob2c.onmicrosoft.com）。 请务必使用 HTTPS 方案。 

    ![LinkedIn 帐户 - 设置已授权的重定向 URL](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >客户端密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。

5. 选择 **添加** 。

6. 选择“设置”，将“应用程序状态”更改为“实时”，选择“更新”。

    ![LinkedIn 帐户 - 设置应用程序状态](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>步骤 2：将 LinkedIn 应用程序密钥添加到 Azure AD B2C
与 LinkedIn 帐户的联合身份验证要求 LinkedIn 帐户的客户端机密代表应用程序信任 Azure AD B2C。 若要将 LinkedIn 应用程序机密存储在 Azure AD B2C 租户中，请执行以下操作：  

1. 在 Azure AD B2C 租户中，选择“B2C 设置” > “标识体验框架”。

2. 若要查看租户中的可用密钥，请选择“策略密钥”。

3. 选择 **添加** 。

4. 在“选项”框中，选择“上传”。

5. 在“名称”框中，键入 **B2cRestClientCertificate**。  
    可能会自动添加前缀 *B2C_1A_*。

6. 在“机密”框中，输入从[应用程序注册门户](https://apps.dev.microsoft.com)获取的 LinkedIn 应用程序机密。

7. 对于“密钥用法”，请选择“加密”。

8. 选择**创建**。 

9. 确认已创建 `B2C_1A_LinkedInSecret` 密钥。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>步骤 3：在扩展策略中添加声明提供程序
如果希望用户使用其 LinkedIn 帐户登录，则必须将 LinkedIn 定义为声明提供程序。 换言之，必须指定要与 Azure AD B2C 通信的终结点。 终结点提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 `<ClaimsProvider>` 节点，将 LinkedIn 定义为声明提供程序：

1. 在工作目录中，打开 *TrustFrameworkExtensions.xml* 扩展策略文件。 

2. 搜索 `<ClaimsProviders>` 元素。

3. 在 `<ClaimsProviders>` 元素中添加以下 XML 代码片段： 

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

4. 将 *client_id* 值替换为 LinkedIn 应用程序客户端 ID。

5. 保存文件。

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>步骤 4：注册 LinkedIn 帐户声明提供程序
前面已设置标识提供者。 但是，目前还不能在任何注册或登录窗口中使用它。 现在，必须将 LinkedIn 帐户标识提供者添加到用户的 `SignUpOrSignIn` 用户旅程。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>步骤 4.1：创建用户旅程的副本
若要使用户旅程可用，需创建现有用户旅程模板的副本，然后添加 LinkedIn 标识提供者：

>[!NOTE]
>如果已将策略基本文件中的 `<UserJourneys>` 元素复制到 *TrustFrameworkExtensions.xml* 扩展文件，则可以跳到本部分。

1. 打开策略的基文件（例如 TrustFrameworkBase.xml）。

2. 搜索 `<UserJourneys>` 元素，选择 `<UserJourney>` 节点的整个内容，再选择“剪切”将所选文本移动到剪贴板。

3. 打开扩展文件（例如 TrustFrameworkExtensions.xml），并搜索 `<UserJourneys>` 元素。 如果该元素不存在，请添加该元素。

4. 将 `<UserJourney>` 节点的整个内容（已在步骤 2 中移动到剪贴板）粘贴到 `<UserJourneys>` 元素。

### <a name="step-42-display-the-button"></a>步骤 4.2：显示“按钮”
`<ClaimsProviderSelections>` 元素定义声明提供程序选择选项的列表及其顺序。 `<ClaimsProviderSelection>` 节点类似于注册或登录页上的标识提供者按钮。 如果为 LinkedIn 帐户添加了 `<ClaimsProviderSelection>` 节点，则当用户进入页面时，会显示一个新按钮。 若要添加此元素，请执行以下操作：

1. 在复制的用户旅程中搜索包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 节点。

2. 找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点。

3. 在 `<ClaimsProviderSelections>` 元素中添加以下 XML 片段：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>步骤 4.3：将按钮链接到操作
准备好按钮后，必须将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 LinkedIn 帐户通信以接收令牌。 可通过链接 LinkedIn 帐户声明提供程序的技术配置文件来将按钮链接到操作：

1. 在 `<UserJourney>` 节点中搜索包含 `Order="2"` 的 `<OrchestrationStep>` 节点。

2. 在 `<ClaimsExchanges>` 元素中添加以下 XML 代码片段：

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值。
    >* 确保 `TechnicalProfileReferenceId` ID 设置为前面创建的技术配置文件 (LinkedIn-OAuth)。

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
    现在，应可使用 LinkedIn 帐户登录。

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>步骤 7：（可选）向配置文件编辑用户旅程注册 LinkedIn 帐户声明提供程序
可能还需要将 LinkedIn 帐户标识提供者添加到用户 `ProfileEdit` 用户旅程。 若要使用户旅程可用，请重复“步骤 4”。 这次，请选择包含 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。 保存、上传并测试策略。

## <a name="optional-download-the-complete-policy-files"></a>（可选）下载完整的策略文件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)演练后，我们建议你使用自己的自定义策略文件来构建方案。 我们提供了[示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)供你参考。
