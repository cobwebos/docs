---
title: 使用自定义策略使用 Azure AD 帐户设置登录
titleSuffix: Azure AD B2C
description: 使用自定义策略在 Azure Active Directory B2C 中设置 Azure Active Directory 帐户登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: be3a7a3ce4ce3a06398436058ea5d4d935ef5a5c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678097"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>使用自定义策略在 Azure Active Directory B2C 中设置 Azure Active Directory 帐户登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示如何使用 Azure 活动目录 B2C （Azure AD B2C） 中的[自定义策略](custom-policy-overview.md)为 Azure 活动目录 （Azure AD） 组织的用户启用登录。

## <a name="prerequisites"></a>先决条件

完成 [Azure Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的步骤。


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>创建策略密钥

需将创建的应用程序密钥存储在 Azure AD B2C 租户中。

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的**目录 + 订阅筛选器**，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“策略”下，选择“Identity Experience Framework”。********
1. 选择**策略键**，然后选择 **"添加**"。
1. 对于“选项”****，请选择 `Manual`。
1. 输入策略密钥的**名称**。 例如，`ContosoAppSecret` 。  前缀`B2C_1A_`在创建时会自动添加到密钥的名称中，因此其在下一节中的 XML 中的引用B2C_1A_ContosoAppSecret *。*
1. 在 **"秘密**"中，输入您之前记录的客户机密。
1. 在“密钥用法”处选择 `Signature`。****
1. 选择“创建”  。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Azure AD 登录，则需将 Azure AD 定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中将 Azure AD 添加到 ClaimsProvider 元素****。

1. 打开 *TrustFrameworkExtensions.xml* 文件。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
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

4. 在 ClaimsProvider 元素下，将 Domain 的值更新为可用于与其他标识提供者进行区分的唯一值********。 例如，`Contoso`。 你没有在此域设置的末尾添加 `.com`。
5. 在 ClaimsProvider 元素下，将 DisplayName 的值更新为声明提供程序的友好名称********。 目前不会使用此值。

### <a name="update-the-technical-profile"></a>更新技术配置文件

若要从 Azure AD 终结点获取令牌，需要定义 Azure AD B2C 与 Azure AD 通信时应使用的协议。 此操作在 ClaimsProvider 的 TechnicalProfile 元素内完成********。

1. 更新 TechnicalProfile 元素的 ID****。 此 ID 用于引用策略其他部分（例如`OIDC-Contoso`）。
1. 更新 DisplayName 的值****。 此值会显示在登录屏幕中的登录按钮上。
1. 更新 Description 的值****。
1. Azure AD 使用 OpenID Connect 协议，因此请确保 Protocol 的值是 `OpenIdConnect`****。
1. 将 METADATA 的值设置为 `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`，其中 `tenant-name` 为你的 Azure AD 租户名称****。 例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
1. 在 **"加密密钥**"下，将**StorageReferenceId**的值更新为之前创建的策略密钥的名称。 例如，`B2C_1A_ContosoAppSecret` 。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现已配置策略，因此 Azure AD B2C 知道如何与 Azure AD 目录通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”********。
1. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件****。
1. 单击“上载” 。 

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供程序已设置，但尚未在任何注册/登录页中可用。 要使其可用，请创建现有模板用户旅程的重复项，然后对其进行修改，以便它还必须具有 Azure AD 标识提供程序：

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
1. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
1. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
1. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
1. 重命名用户旅程的 ID。 例如，`SignUpSignInContoso` 。

### <a name="display-the-button"></a>显示按钮

**声明提供程序选择**元素类似于注册/登录页面上的标识提供程序按钮。 如果为 Azure AD 添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 查找在*TrustFramework 扩展.xml*中创建的用户旅程中包含`Order="1"`的**业务流程步骤**元素。
1. 在 **ClaimsProviderSelections** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `ContosoExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Azure AD 通信以接收令牌。 可通过链接 Azure AD 声明提供程序的技术配置文件来将按钮链接到操作：

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
1. 添加以下 **ClaimsExchange** 元素，确保在 **Id** 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    将**技术配置文件参考 Id**的值更新到您之前创建的技术配置文件的**ID。** 例如，`OIDC-Contoso` 。

1. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过您在 B2C 租户中注册的应用程序与 Azure AD B2C 的通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 SignUpSignInContoso.xml**。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInContoso` 。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如，`http://contoso.com/B2C_1A_signup_signin_contoso` 。
1. 更新**默认用户旅程**中的**参考 Id**属性的值，以匹配您之前创建的用户旅程的 ID。 例如，*注册登录 Contoso*。
1. 保存更改并上传文件。
1. 在 **"自定义策略"** 下，在列表中选择新策略。
1. 在 **"选择应用程序**下拉列表"中，选择之前创建的 Azure AD B2C 应用程序。 例如 *，testapp1*。
1. 立即复制 **"立即运行"终结点**并在专用浏览器窗口中打开它，例如，Google Chrome 中的隐身模式或 Microsoft Edge 中的 InPrivate 窗口。 通过在专用浏览器窗口中打开，您可以通过不使用任何当前缓存的 Azure AD 凭据来测试完整的用户旅程。
1. 选择 Azure AD 登录按钮，例如*Contoso 员工*，然后在 Azure AD 组织租户中输入用户的凭据。 系统将要求您授权应用程序，然后输入您的个人资料信息。

如果登录过程成功，浏览器将重定向到`https://jwt.ms`，显示 Azure AD B2C 返回的令牌的内容。

## <a name="next-steps"></a>后续步骤

使用自定义策略时，有时在开发策略期间对策略进行故障排除时可能需要其他信息。

为了帮助诊断问题，可以暂时将策略置于"开发人员模式"，并使用 Azure 应用程序见解收集日志。 了解如何在[Azure 活动目录 B2C 中：收集日志](troubleshoot-with-application-insights.md)。
