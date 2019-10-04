---
title: 使用自定义策略在 Azure Active Directory B2C 中使用 Azure Active Directory 帐户设置登录
description: 使用自定义策略在 Azure Active Directory B2C 中设置 Azure Active Directory 帐户登录。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2aa2ed6fe4d8218737c42bb3d76084c5d677623f
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71826950"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>使用自定义策略在 Azure Active Directory B2C 中设置 Azure Active Directory 帐户登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自定义策略](active-directory-b2c-overview-custom.md)，从 Azure Active Directory （Azure AD）组织中启用用户登录。

## <a name="prerequisites"></a>先决条件

完成 [Azure Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。

## <a name="register-an-application"></a>注册应用程序

若要让用户从特定的 Azure AD 组织登录，需要在组织 Azure AD 租户中注册一个应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含组织 Azure AD 租户的目录（例如，contoso.com）。 在顶部菜单中选择 "**目录 + 订阅" 筛选器**，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”。
1. 选择“新注册”。
1. 输入应用程序的**名称**。 例如， `Azure AD B2C App` 。
1. 仅对此应用程序接受**此组织目录中**的默认帐户选择。
1. 对于“重定向 URI”，接受值 **Web**，并以全小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如， `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

1. 选择“注册”。 记录**应用程序（客户端） ID** ，以便在后面的步骤中使用。
1. 依次选择“证书和机密”、“新建客户端机密”。
1. 输入机密的**说明**，选择过期时间，然后选择 "**添加**"。 记录机密**值**以便在后面的步骤中使用。

## <a name="create-a-policy-key"></a>创建策略密钥

需将创建的应用程序密钥存储在 Azure AD B2C 租户中。

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅" 筛选器**，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
1. 在 "**策略**" 下，选择 "**标识体验框架**"。
1. 选择 "**策略密钥**"，然后选择 "**添加**"。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的**名称**。 例如， `ContosoAppSecret` 。  创建时，前缀 `B2C_1A_` 会自动添加到密钥的名称中，因此，在下一部分的 XML 中，其引用为*B2C_1A_ContosoAppSecret*。
1. 在 "**密钥**" 中，输入你之前记录的客户端密码。
1. 在“密钥用法”处选择 `Signature`。
1. 选择“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Azure AD 登录，则需将 Azure AD 定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中将 Azure AD 添加到 ClaimsProvider 元素。

1. 打开 *TrustFrameworkExtensions.xml* 文件。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 在 ClaimsProvider 元素下，将 Domain 的值更新为可用于与其他标识提供者进行区分的唯一值。 例如：`Contoso`。 你没有在此域设置的末尾添加 `.com`。
5. 在 ClaimsProvider 元素下，将 DisplayName 的值更新为声明提供程序的友好名称。 目前不会使用此值。

### <a name="update-the-technical-profile"></a>更新技术配置文件

若要从 Azure AD 终结点获取令牌，需要定义 Azure AD B2C 与 Azure AD 通信时应使用的协议。 此操作在 ClaimsProvider 的 TechnicalProfile 元素内完成。

1. 更新 TechnicalProfile 元素的 ID。 此 ID 用于从策略的其他部分引用此技术配置文件。
1. 更新 DisplayName 的值。 此值会显示在登录屏幕中的登录按钮上。
1. 更新 Description 的值。
1. Azure AD 使用 OpenID Connect 协议，因此请确保 Protocol 的值是 `OpenIdConnect`。
1. 将 METADATA 的值设置为 `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`，其中 `your-AD-tenant-name` 为你的 Azure AD 租户名称。 例如： `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`
1. 打开浏览器并中转到刚更新的**元数据**URL，查找**颁发者**对象，然后将该值复制并粘贴到 XML 文件中的**ProviderName**值中。
1. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
1. 在**CryptographicKeys**下，将**StorageReferenceId**的值更新为之前创建的策略密钥的名称。 例如， `B2C_1A_ContosoAppSecret` 。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现已配置策略，因此 Azure AD B2C 知道如何与 Azure AD 目录通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”。
1. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
1. 单击“上传” 。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但在任何注册/登录页面中都不提供该服务。 若要使其可用，请创建现有模板用户旅程的副本，然后对其进行修改，使其也具有 Azure AD 标识提供者：

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 重命名用户旅程的 ID。 例如， `SignUpSignInContoso` 。

### <a name="display-the-button"></a>显示按钮

**Claimsexchange**元素类似于注册/登录页上的标识提供者按钮。 如果为 Azure AD 添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 查找**OrchestrationStep**元素，该元素`Order="1"`包含在*trustframeworkextensions.xml*中创建的用户旅程中。
1. 在 **ClaimsProviderSelections** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `ContosoExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Azure AD 通信以接收令牌。 可通过链接 Azure AD 声明提供程序的技术配置文件来将按钮链接到操作：

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
1. 添加以下 **ClaimsExchange** 元素，确保在 **Id** 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 **Id**。 例如， `ContosoProfile` 。

1. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

与 Azure AD B2C 通信是通过在 B2C 租户中注册的应用程序进行的。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 SignUpSignInContoso.xml。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如， `SignUpSignInContoso` 。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如， `http://contoso.com/B2C_1A_signup_signin_contoso` 。
1. 更新**DefaultUserJourney**中的**ReferenceId**属性的值，使其与之前创建的用户旅程的 ID 匹配。 例如， *SignUpSignInContoso*。
1. 保存更改并上传文件。
1. 在 "**自定义策略**" 下，选择列表中的新策略。
1. 在 "**选择应用程序**" 下拉菜单中，选择之前创建的 Azure AD B2C 应用程序。 例如， *testapp1-template.json*。
1. 复制 "**立即运行" 终结点**并在专用浏览器窗口中打开它，例如，在 Google Chrome 中的 Incognito 模式或 Microsoft Edge 中的 InPrivate 窗口。 通过在专用浏览器窗口中打开，可以通过不使用任何当前缓存 Azure AD 凭据来测试整个用户旅程。
1. 选择 Azure AD 登录 "按钮（例如*Contoso Employee*），然后输入 Azure AD 组织租户中某个用户的凭据。 系统会要求你对应用程序进行授权，然后为你的配置文件输入信息。

如果登录过程成功，浏览器将重定向到`https://jwt.ms`，后者显示 Azure AD B2C 返回的令牌的内容。

## <a name="next-steps"></a>后续步骤

使用自定义策略时，有时可能需要在部署过程中对策略进行故障排除时提供其他信息。

若要帮助诊断问题，可以暂时将策略置于 "开发人员模式" 中，并 Azure 应用程序 Insights 收集日志。 了解 Azure Active Directory B2C 中的[操作方法：正在收集](active-directory-b2c-troubleshoot-custom.md)日志。
