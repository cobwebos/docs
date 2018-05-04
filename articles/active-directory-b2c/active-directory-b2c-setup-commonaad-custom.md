---
title: 使用自定义策略添加多租户 Azure AD 标识提供者 - Azure Active Directory B2C | Microsoft Docs
description: 使用自定义策略添加多租户 Azure AD 标识提供者 - Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: cff5c1eed374683ad3e2c1f1a69f6f172f36c536
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C：让用户使用自定义策略登录到多租户 Azure AD 标识提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何让用户通过[自定义策略](active-directory-b2c-overview-custom.md)使用 Azure Active Directory (Azure AD) 的通用终结点登录。

>[!NOTE]
> 我们在以下说明中将“contoso.com”用于组织 Azure AD 租户，并将“fabrikamb2c.onmicrosoft.com”用作 Azure AD B2C 租户。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

这些步骤包括：
     
1. 创建 Azure Active Directory B2C (Azure AD B2C) 租户。
2. 创建 Azure AD B2C 应用程序。    
3. 注册两个策略引擎应用程序。  
4. 设置密钥。 
5. 设置初学者包。

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>步骤 1。 创建多租户 Azure AD 应用

若要让用户使用多租户 Azure AD 终结点登录，需要在任何 Azure AD 租户中注册一个多租户应用程序。 本文将会演示如何在 Azure AD B2C 租户中创建多租户 Azure AD 应用程序。 然后，让用户使用该多租户 Azure AD 应用程序登录。

>[!NOTE]
> 如果希望 Azure AD 用户**和具有 Microsoft 帐户的用户**登录，请跳过本部分，并改为在 [Microsoft 开发人员门户](https://apps.dev.microsoft.com)中注册应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部栏中选择帐户。 在“目录”列表中，选择要将 Azure AD 应用程序 (fabrikamb2c.onmicrosoft.com) 注册到的 Azure AD B2C 租户。
2. 在左侧窗格中选择“更多服务”，并搜索“应用注册”。
3. 选择“新建应用程序注册”。
4. 输入应用程序的名称（例如 `Azure AD B2C App`）。
5. 选择“Web 应用/API”作为应用程序类型。
6. 对于“登录 URL”，输入以下 URL，其中 `yourtenant` 替换为 Azure AD B2C 租户的名称 (`fabrikamb2c.onmicrosoft.com`)：

    >[!NOTE]
    >“登录 URL”中“yourtenant”的值必须全小写。

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. 保存应用程序 ID。
1. 选择新建的应用程序。
1. 在“设置”边栏选项卡下，选择“属性”。
1. 将“多租户”设置为“是”。
1. 在“设置”边栏选项卡下，选择“密钥”。
1. 创建新密钥，然后保存。 在下一部分的步骤中将使用它。

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>步骤 2. 将 Azure AD 密钥添加到 Azure AD B2C

需要在 Azure AD B2C 设置中注册应用程序密钥。 为此，请按以下步骤操作：

1. 转到 Azure AD B2C 的设置菜单
1. 单击“标识体验框架” > “策略密钥”。
1. 选择“+添加”。
1. 选择或输入这些选项：
   * 选择“手动”。
   * 对于“名称”，选择与 Azure AD 租户名称匹配的名称（例如 `AADAppSecret`）。  前缀 `B2C_1A_` 会自动添加到密钥名称。
   * 将应用程序密钥粘贴到“机密”文本框中。
   * 选择“签名”。
5. 选择**创建**。
6. 确认已创建密钥 `B2C_1A_AADAppSecret`。

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>步骤 3. 在基本策略中添加声明提供程序

如果要允许用户使用 Azure AD 登录，则需将 Azure AD 定义为声明提供程序。 换而言之，需指定要与 Azure AD B2C 通信的终结点。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。 

可以通过在策略的扩展文件中将 Azure AD 添加到 `<ClaimsProvider>` 节点，将 Azure AD 定义为声明提供程序：

1. 从工作目录打开扩展文件 (TrustFrameworkExtensions.xml)。
1. 找到 `<ClaimsProviders>` 节。 如果该节不存在，请在根节点的下面添加它。
1. 按如下所示添加新 `<ClaimsProvider>` 节点：

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. If you would like only specific tenants to be able to sign in, uncomment the line below and update the GUIDs. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item> -->

        <!-- The commented key below specifies that users from any tenant can sign-in. Comment or remove the line below if using the line above. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

1. 在 `<ClaimsProvider>` 节点下面，将 `<Domain>` 的值更新为可用于区分其他标识提供者的唯一值。
1. 在 `<TechnicalProfile>` 节点下，更新 `<DisplayName>` 的值。 此值会显示在登录屏幕中的登录按钮上。
1. 更新 `<Description>` 的值。
1. 将 `<Item Key="client_id">` 设置为 Azure AD 多租户应用注册中的应用程序 ID。

### <a name="step-31-optional-restrict-access-to-specific-list-of-azure-ad-tenants"></a>步骤 3.1 [可选]：限制对特定 Azure AD 租户列表的访问
你可能想要更新有效令牌颁发者列表，并限制访问用户可以登录到的特定 Azure AD 租户列表。 若要获取值，需要查看希望让用户从中登录的每个特定 Azure AD 租户的元数据。 数据格式如下所示：`https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`，其中，`yourAzureADtenant` 是 Azure AD 租户名称（contoso.com 或其他任何 Azure AD 租户）。
1. 打开浏览器并转到元数据 URL。
1. 在浏览器中，找到“issuer”对象并复制其值。 该对象应与下面类似：`https://sts.windows.net/{tenantId}/`。
1. 粘贴 `ValidTokenIssuerPrefixes` 密钥的值。 可以添加多个值并使用逗号将其隔开。 上述示例 XML 中做了相关注释。

> [!NOTE]
> 使用 `https://sts.windows.net` 作为前缀值可让所有 Azure AD 用户登录到你的应用。

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>步骤 4. 注册 Azure AD 帐户声明提供程序

### <a name="step-41-make-a-copy-of-the-user-journey"></a>步骤 4.1：创建用户旅程的副本

现在，需要将 Azure AD 标识提供者添加到用户旅程之一。 此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。

若要使其可用，我们需要创建现有模板用户旅程的副本，并对其进行修改，以便它也包含 Azure AD 标识提供者：

1. 打开策略的基文件（例如 TrustFrameworkBase.xml）。
1. 找到 `<UserJourneys>` 元素并复制包含 `Id="SignUpOrSignIn"` 的整个 `<UserJourney>`。
1. 打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
1. 将复制的整个 `<UserJourney>` 节点粘贴为 `<UserJourneys>` 元素的子级。
1. 重命名新用户旅程的 ID（例如，重命名为 `SignUpOrSignUsingAzureAD`）。 

### <a name="step-42-display-the-button"></a>步骤 4.2：显示“按钮”

`<ClaimsProviderSelection>` 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 Azure AD 添加 `<ClaimsProviderSelection>` 元素，则当用户进入页面时，会显示一个新按钮。 添加此元素：

1. 在创建的用户旅程中找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点。
1. 添加以下内容：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. 将 `TargetClaimsExchangeId` 设置为相应的值。 我们建议遵循其他元素使用的相同约定 - \[ClaimProviderName\]Exchange。

### <a name="step-43-link-the-button-to-an-action"></a>步骤 4.3：将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Azure AD 通信以接收令牌。 可通过链接 Azure AD 声明提供程序的技术配置文件来将按钮链接到操作：

1. 在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
1. 添加以下内容：

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. 将 `Id` 更新为与前一部分中的 `TargetClaimsExchangeId` 相同的值。
1. 将 `TechnicalProfileReferenceId` 更新为前面创建的技术配置文件的 ID (Common-AAD)。

## <a name="step-5-create-a-new-rp-policy"></a>步骤 5：创建新 RP 策略

现在，需要更新用于启动刚刚创建的用户旅程的信赖方 (RP) 文件：
 
1. 在工作目录创建 SignUpOrSignIn.xml 的副本并将它重命名（例如，将它重命名为 SignUpOrSignInWithAAD.xml）。  
1. 打开新文件，并使用唯一值（例如 SignUpOrSignInWithAAD）更新 `<TrustFrameworkPolicy>` 的 `PolicyId` 属性。 这会是策略的名称（例如 B2C\_1A\_SignUpOrSignInWithAAD）。 
1. 修改 `<DefaultUserJourney>` 中的 `ReferenceId` 属性，使其与创建的新用户旅程的 ID 匹配 (SignUpOrSignUsingAzureAD)。 
1. 保存更改，然后上传文件。 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>步骤 6：将策略上传到租户

1. 在 [Azure 门户](https://portal.azure.com)中，切换到[你的 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后选择“Azure AD B2C”。
2. 选择“标识体验框架”。
3. 选择“所有策略”。
4. 选择“上传策略”。
5. 选中“覆盖策略(如果存在)”复选框。
6. 上传 `TrustFrameworkExtensions.xml` 文件和 RP 文件（例如 `SignUpOrSignInWithAAD.xml`），并确保它们通过验证。

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>步骤 7：使用“立即运行”测试自定义策略

1. 选择“Azure AD B2C 设置”，然后选择“标识体验框架”。
    > [!NOTE]
    > “立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

1. 打开已上传的信赖方 (RP) 自定义策略 (*B2C\_1A\_SignUpOrSignInWithAAD*)，然后选择“立即运行”。
1. 现在，应该能够使用 Azure AD 帐户登录。

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>（可选）步骤 8：将 Azure AD 帐户声明提供程序注册到 Profile-Edit 用户旅程

可能还需要将 Azure AD 帐户标识提供者添加到 `ProfileEdit` 用户旅程。 若要使用户旅程可用，请重复步骤 4 至 6。 这次，请选择包含 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。 保存、上传并测试策略。

## <a name="troubleshooting"></a>故障排除

若要诊断问题，请阅读[故障排除](active-directory-b2c-troubleshoot-custom.md)。

## <a name="next-steps"></a>后续步骤

向 [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) 提供反馈。
