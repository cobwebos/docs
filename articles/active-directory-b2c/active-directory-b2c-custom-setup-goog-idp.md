---
title: 使用自定义策略在 Azure Active Directory B2C 中设置 Google 帐户登录 | Microsoft Docs
description: 在 Azure Active Directory B2C 中使用自定义策略设置使用 LinkedIn 帐户的登录。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 94b71dc8a355b818af6789022eb297100b569e73
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712020"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 中的自定义策略设置 Google 帐户登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何让 Google 帐户的用户使用 Azure Active Directory (Azure AD) B2C 中的[自定义策略](active-directory-b2c-overview-custom.md)登录。

## <a name="prerequisites"></a>必备组件

- 完成 [Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。
- 如果没有 Google 帐户，请根据[创建 Google 帐户](https://accounts.google.com/SignUp)中所述创建一个帐户。

## <a name="register-the-application"></a>注册应用程序

若要让 Google 帐户的用户登录，需要创建一个 Google 应用程序项目。 

1. 使用帐户凭据登录到 [Google 开发人员控制台](https://console.developers.google.com/)。
2. 输入“项目名称”，单击“创建”，然后确保使用的是新项目。
3. 在左侧菜单中选择“凭据”，然后选择“创建凭据”>“Oauth 客户端 ID”。
4. 选择“配置许可屏幕”。
5. 选择或指定有效的**电子邮件地址**，提供向用户显示的**产品名称**，在“授权的域”中输入 `b2clogin.com`，然后单击“保存”。
6. 在“应用程序类型”下，选择“Web 应用程序”。
7. 输入应用程序的**名称**。
8. 在“授权的 JavaScript 来源”中输入 `https://your-tenant-name.b2clogin.com`，然后在“授权的重定向 URI”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 your-tenant-name 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
8. 单击**创建**。
9. 复制“客户端 ID”和“客户端密码”的值。 将 Google 配置为租户中的标识提供者时需要这两项。 客户端机密是一个重要的安全凭据。

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录。 选择**目录和订阅筛选器**顶部菜单中选择包含你的租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 在“概述”页上，选择“标识体验框架 - 预览”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Manual`。
7. 输入策略密钥的**名称**。 例如，`GoogleSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 在“机密”中，输入前面记录的应用程序机密。
9. 在“密钥用法”处选择 `Signature`。
10. 单击**创建**。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Google 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。 

可以通过在策略的扩展文件中将 Google 帐户添加到 **ClaimsProviders** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 按如下所示添加新的 **ClaimsProvider**：

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
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
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
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
5. 保存文件。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现已配置策略，因此 Azure AD B2C 知道如何与 Azure AD 目录通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
3. 单击“上传” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 Azure AD 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInGoogle`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 Google 帐户添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `GoogleExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Google 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下**ClaimsExchange**并确保你用于 ID 使用相同的值的元素**TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```
    
    将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 ID。 例如，`Google-OAuth`。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过在租户中创建的应用程序与 Azure AD B2C 进行通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录。 选择**目录和订阅筛选器**顶部菜单中选择包含你的租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“应用程序”，然后选择“添加”。
5. 输入应用程序的名称，例如 *testapp1*。
6. 对于“Web 应用/Web API”，请选择 `Yes`，然后为“回复 URL”输入 `https://jwt.ms`。
7. 单击**创建**。

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInGoogle.xml*。
2. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInGoogle`。
3. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_google`
4. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignGoogle)。
5. 保存更改并上传文件，然后选择列表中的新策略。
6. 确保在“选择应用程序”字段选择你创建的 Azure AD B2C 应用程序，然后单击“立即运行”对其进行测试。
