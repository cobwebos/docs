---
title: 在 Azure Active Directory B2C 中使用自定义策略添加 Azure AD 提供程序 | Microsoft Docs
description: 了解 Azure Active Directory B2C 自定义策略。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9d010564aadcb6ea33312b7fb40854cfd0a97f1a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709234"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C：使用 Azure AD 帐户登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何让用户使用[自定义策略](active-directory-b2c-overview-custom.md)从特定 Azure Active Directory (Azure AD) 登录。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

这些步骤包括：

1. 创建 Azure Active Directory B2C (Azure AD B2C) 租户。
2. 创建 Azure AD B2C 应用程序。
3. 注册两个策略引擎应用程序。
4. 设置密钥。
5. 设置初学者包。

## <a name="create-an-azure-ad-app"></a>创建 Azure AD 应用

若要让用户从特定的 Azure AD 组织登录，需要在组织 Azure AD 租户中注册一个应用程序。

>[!NOTE]
> 我们在以下说明中将“contoso.com”用于组织 Azure AD 租户，并将“fabrikamb2c.onmicrosoft.com”用作 Azure AD B2C 租户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏中选择帐户。 在“目录”列表中，选择要注册应用程序的组织 Azure AD 租户 (contoso.com)。
3. 在左侧窗格中选择“更多服务”，并搜索“应用注册”。
4. 选择“新建应用程序注册”。
5. 输入应用程序的名称（例如 `Azure AD B2C App`）。
6. 选择“Web 应用/API”作为应用程序类型。
7. 对于“登录 URL”，输入以下 URL，其中 `yourtenant` 替换为 Azure AD B2C 租户的名称 (`fabrikamb2c.onmicrosoft.com`)：

    >[!NOTE]
    >“登录 URL”中“yourtenant”的值必须全小写。

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. 保存应用程序 ID。
9. 选择新建的应用程序。
10. 在“设置”边栏选项卡下，选择“密钥”。
11. 输入密钥说明，选择持续时间，然后单击“保存”。 随即会显示密钥的值。 复制密钥值，便于在下一部分的步骤中使用。

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>将 Azure AD 密钥添加到 Azure AD B2C

需将 contoso.com 应用程序密钥存储在 Azure AD B2C 租户中。 为此，请按以下步骤操作：
1. 转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架” > “策略密钥”。
1. 选择“+添加”。
1. 选择或输入这些选项：
   * 选择“手动”。
   * 对于“名称”，选择与 Azure AD 租户名称匹配的名称（例如 `ContosoAppSecret`）。  前缀 `B2C_1A_` 会自动添加到密钥名称。
   * 将应用程序密钥粘贴到“机密”文本框中。
   * 选择“签名”。
1. 选择**创建**。
1. 确认已创建密钥 `B2C_1A_ContosoAppSecret`。


## <a name="add-a-claims-provider-in-your-base-policy"></a>在基本策略中添加声明提供程序

如果要允许用户使用 Azure AD 登录，则需将 Azure AD 定义为声明提供程序。 换而言之，需指定要与 Azure AD B2C 通信的终结点。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。 

可以通过在策略的扩展文件中将 Azure AD 添加到 `<ClaimsProvider>` 节点，将 Azure AD 定义为声明提供程序：

1. 从工作目录打开扩展文件 (TrustFrameworkExtensions.xml)。
1. 找到 `<ClaimsProviders>` 节。 如果该节不存在，请在根节点的下面添加它。
1. 按如下所示添加新 `<ClaimsProvider>` 节点：

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 在 `<ClaimsProvider>` 节点下面，将 `<Domain>` 的值更新为可用于区分其他标识提供者的唯一值。
1. 在 `<ClaimsProvider>` 节点下面，将 `<DisplayName>` 的值更新为声明提供程序的友好名称。 目前不会使用此值。

### <a name="update-the-technical-profile"></a>更新技术配置文件

若要从 Azure AD 终结点获取令牌，需要定义 Azure AD B2C 与 Azure AD 通信时应使用的协议。 可在 `<ClaimsProvider>` 的 `<TechnicalProfile>` 元素中执行此操作。
1. 更新 `<TechnicalProfile>` 节点的 ID。 此 ID 用于从策略的其他部分引用此技术配置文件。
1. 更新 `<DisplayName>` 的值。 此值会显示在登录屏幕中的登录按钮上。
1. 更新 `<Description>` 的值。
1. Azure AD 使用 OpenID Connect 协议，因此请确保 `<Protocol>` 的值是 `"OpenIdConnect"`。

需要更新前面提到的 XML 中的 `<Metadata>` 节，以反映特定 Azure AD 租户的配置设置。 在 XML 文件中，按如下所示更新元数据值：

1. 将 `<Item Key="METADATA">` 设置为 `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`，其中，`yourAzureADtenant` 是 Azure AD 租户名称 (contoso.com)。
1. 打开浏览器并转到刚刚更新的 `METADATA` URL。
1. 在浏览器中，找到“issuer”对象并复制其值。 该对象应与下面类似：`https://sts.windows.net/{tenantId}/`。
1. 在 XML 文件中粘贴 `<Item Key="ProviderName">` 的值。
1. 将 `<Item Key="client_id">` 设置为应用注册中的应用程序 ID。
1. 将 `<Item Key="IdTokenAudience">` 设置为应用注册中的应用程序 ID。
1. 确保 `<Item Key="response_types">` 设置为 `id_token`。
1. 确保 `<Item Key="UsePolicyInRedirectUri">` 设置为 `false`。

还需要将 Azure AD B2C 租户中注册的 Azure AD 机密链接到 Azure AD `<ClaimsProvider>` 信息：

* 在上述 XML 文件的 `<CryptographicKeys>` 节中，将 `StorageReferenceId` 的值更新为定义的机密的引用 ID（例如 `ContosoAppSecret`）。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现已配置策略，因此 Azure AD B2C 知道如何与 Azure AD 目录通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。 为此，请执行以下操作：

1. 打开 Azure AD B2C 租户中的“所有策略”边栏选项卡。
1. 选中“覆盖策略(如果存在)”框。
1. 上传扩展文件 (TrustFrameworkExtensions.xml)，并确保它能够通过验证。

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>将 Azure AD 声明提供程序注册到用户旅程

现在，需要将 Azure AD 标识提供者添加到用户旅程之一。 此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，我们需要创建现有模板用户旅程的副本，并对其进行修改，以便它也包含 Azure AD 标识提供者：

1. 打开策略的基文件（例如 TrustFrameworkBase.xml）。
1. 找到 `<UserJourneys>` 元素并复制包含 `Id="SignUpOrSignIn"` 的整个 `<UserJourney>`。
1. 打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
1. 将复制的整个 `<UserJourney>` 节点粘贴为 `<UserJourneys>` 元素的子级。
1. 重命名新用户旅程的 ID（例如，重命名为 `SignUpOrSignUsingContoso`）。

### <a name="display-the-button"></a>显示“按钮”

`<ClaimsProviderSelection>` 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 Azure AD 添加 `<ClaimsProviderSelection>` 元素，则当用户进入页面时，会显示一个新按钮。 添加此元素：

1. 在刚刚创建的用户旅程中找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点。
1. 添加以下内容：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. 将 `TargetClaimsExchangeId` 设置为相应的值。 我们建议遵循其他元素使用的相同约定 - \[ClaimProviderName\]Exchange。

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Azure AD 通信以接收令牌。 可通过链接 Azure AD 声明提供程序的技术配置文件来将按钮链接到操作：

1. 在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
1. 添加以下内容：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. 将 `Id` 更新为与前一部分中的 `TargetClaimsExchangeId` 相同的值。
1. 将 `TechnicalProfileReferenceId` 更新为前面创建的技术配置文件的 ID (ContosoProfile)。

### <a name="upload-the-updated-extension-file"></a>上传更新的扩展文件

现已完成修改扩展文件。 保存它。 然后上传文件，确保所有验证成功。

### <a name="update-the-rp-file"></a>更新 RP 文件

现在，需要更新用于启动刚刚创建的用户旅程的信赖方 (RP) 文件：

1. 在工作目录创建 SignUpOrSignIn.xml 的副本并将它重命名（例如，将它重命名为 SignUpOrSignInWithAAD.xml）。
1. 打开新文件，并使用唯一值（例如 SignUpOrSignInWithAAD）更新 `<TrustFrameworkPolicy>` 的 `PolicyId` 属性。 <br> 这会是策略的名称（例如 B2C\_1A\_SignUpOrSignInWithAAD）。
1. 修改 `<DefaultUserJourney>` 中的 `ReferenceId` 属性，使其与创建的新用户旅程的 ID 匹配 (SignUpOrSignUsingContoso)。
1. 保存更改，然后上传文件。

## <a name="troubleshooting"></a>故障排除

测试刚刚上传的自定义策略：打开其边栏选项卡，并单击“立即运行”。 若要诊断问题，请阅读[故障排除](active-directory-b2c-troubleshoot-custom.md)。

## <a name="next-steps"></a>后续步骤

向 [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) 提供反馈。
