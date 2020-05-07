---
title: 使用自定义策略设置使用 Amazon 帐户登录
titleSuffix: Azure AD B2C
description: 使用自定义策略在 Azure Active Directory B2C 中设置 Amazon 帐户登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 059c43b24ddc9f319eac4f2783cfc203bed8c7f1
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900435"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略设置 Amazon 帐户登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自定义策略](custom-policy-overview.md)，从 Amazon 帐户启用用户登录。

## <a name="prerequisites"></a>先决条件

- 完成[自定义策略入门](custom-policy-get-started.md)中的步骤。
- 如果还没有 Amazon 帐户，请在处[https://www.amazon.com/](https://www.amazon.com/)创建一个。

## <a name="create-an-app-in-the-amazon-developer-console"></a>在 Amazon 开发人员控制台中创建应用

若要使用 Amazon 帐户作为 Azure Active Directory B2C （Azure AD B2C）中的联合标识提供者，需要在[Amazon 开发人员服务和技术](https://developer.amazon.com)中创建应用程序。 如果还没有 Amazon 帐户，可以在[https://www.amazon.com/](https://www.amazon.com/)注册。

> [!NOTE]  
> 在下面的**步骤 8**中使用以下 url， `your-tenant-name`并将替换为你的租户的名称。 输入租户名称时，请使用全部小写字母，即使租户在 Azure AD B2C 中使用大写字母定义。
> - 对于 "**允许的来源**"，输入`https://your-tenant-name.b2clogin.com` 
> - 对于 "**允许的返回 url**"，输入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
4. 在“概述”页上选择“标识体验框架”****。
5. 选择“策略密钥”****，然后选择“添加”****。
6. 对于“选项”****，请选择 `Manual`。
7. 输入策略密钥的**名称**。 例如 `AmazonSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 在“机密”中，输入前面记录的应用程序机密****。
9. 在“密钥用法”处选择 `Signature`。****
10. 单击“创建”。 

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Amazon 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Amazon 帐户添加到 **ClaimsProviders** 元素，将该帐户定义为声明提供程序。


1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
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

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”********。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件****。
3. 单击“上载” 。 

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 Amazon 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如 `SignUpSignInAmazon`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 Amazon 帐户添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `AmazonExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Amazon 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，确保在 ID 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 ID。 例如 `Amazon-OAuth`。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

与 Azure AD B2C 通信是通过在 B2C 租户中注册的应用程序进行的。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInAmazon.xml*。
2. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如 `SignUpSignInAmazon`。
3. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_amazon`
4. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignAmazon)。
5. 保存更改并上传文件，然后选择列表中的新策略。
6. 确保在“选择应用程序”字段选择你创建的 Azure AD B2C 应用程序，然后单击“立即运行”对其进行测试********。
