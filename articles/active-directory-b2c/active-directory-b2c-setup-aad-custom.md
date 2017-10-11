---
title: "Azure Active Directory B2C： 添加 Azure AD 提供程序通过使用自定义策略 |Microsoft 文档"
description: "了解有关 Azure Active Directory B2C 自定义策略"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 6c073d70debfdc3560405955d65fa9ccaa7d8b1f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>通过使用 Azure AD 帐户登录的 azure Active Directory B2C:

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

这篇文章演示如何在使用特定的 Azure Active Directory (Azure AD) 组织的用户的登录启用[自定义策略](active-directory-b2c-overview-custom.md)。

## <a name="prerequisites"></a>必备条件

完成中的步骤[开始使用自定义策略](active-directory-b2c-get-started-custom.md)文章。

这些步骤包括：

1. 创建 Azure Active Directory B2C (Azure AD B2C) 租户。
2. 创建 Azure AD B2C 应用程序。
3. 注册两个策略引擎应用程序。
4. 设置密钥。
5. 设置初学者包。

## <a name="create-an-azure-ad-app"></a>创建 Azure AD 应用程序

若要启用在从特定的用户的登录 Azure AD 的组织，你需要注册中的组织的应用程序的 Azure AD 租户。

>[!NOTE]
> 我们会将"contoso.com"用于组织 Azure AD 租户和"fabrikamb2c.onmicrosoft.com"作为 Azure AD B2C 租户中的以下说明进行操作。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部栏上，选择你的帐户。 从**目录**列表中，选择组织想要注册你的应用程序 (contoso.com) 的 Azure AD 租户。
1. 选择**更多的服务**在左窗格中，然后搜索"应用程序注册"。
1. 选择**新应用程序注册**。
1. 输入你的应用程序的名称 (例如， `Azure AD B2C App`)。
1. 选择**Web 应用程序 / API**的应用程序类型。
1. 有关**登录 URL**，输入以下 URL，其中`yourtenant`替换为你的 Azure AD B2C 租户的名称 (`fabrikamb2c.onmicrosoft.com`):

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. 保存应用程序 id。
1. 选择新创建的应用程序。
1. 下**设置**边栏选项卡，选择**密钥**。
1. 创建新密钥，并将其保存。 你将在下一节中的步骤中使用它。

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>将 Azure AD 密钥添加到 Azure AD B2C

你需要将 contoso.com 应用程序密钥存储在你的 Azure AD B2C 租户。 为此，请执行以下操作：
1. 转到你的 Azure AD B2C 租户，并选择**B2C 设置** > **标识体验 Framework** > **策略键**。
1. 选择**+ 添加**。
1. 选择或输入这些选项：
   * 选择**手动**。
   * 有关**名称**，选择你的 Azure AD 租户名称相匹配的名称 (例如， `ContosoAppSecret`)。  前缀`B2C_1A_`被自动添加到你的密钥的名称。
   * 将应用程序中的密钥粘贴**机密**框。
   * 选择**签名**。
1. 选择“创建”。
1. 确认你已创建密钥`B2C_1A_ContosoAppSecret`。


## <a name="add-a-claims-provider-in-your-base-policy"></a>在基策略中添加声明提供程序

如果你希望用户通过使用 Azure AD 中进行登录，你需要定义 Azure AD 用作声明提供程序。 换而言之，你需要指定 Azure AD B2C 将与之通信的终结点。 终结点将提供一组 Azure AD B2C 用于验证特定用户已进行身份验证的声明。 

可以通过添加到 Azure AD 用作声明提供程序中定义 Azure AD`<ClaimsProvider>`你的策略的扩展文件中的节点：

1. 从你的工作目录中打开扩展文件 (TrustFrameworkExtensions.xml)。
1. 查找`<ClaimsProviders>`部分。 如果不存在，则将其添加根节点下。
1. 添加新`<ClaimsProvider>`节点，如下所示：

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

1. 下`<ClaimsProvider>`节点，更新的值`<Domain>`到可用于将它与其他标识提供者区分开来的唯一值。
1. 下`<ClaimsProvider>`节点，更新的值`<DisplayName>`声明提供程序的友好名称。 当前未使用此值。

### <a name="update-the-technical-profile"></a>更新的技术的配置文件

若要从 Azure AD 终结点获取令牌，你需要定义 Azure AD B2C 应使用与 Azure AD 进行通信的协议。 这是内部`<TechnicalProfile>`元素`<ClaimsProvider>`。
1. 更新的 ID`<TechnicalProfile>`节点。 使用此 ID 来从该策略的其他部分引用此技术的配置文件。
1. 更新的值`<DisplayName>`。 此值将显示在登录屏幕上的登录按钮。
1. 更新的值`<Description>`。
1. Azure AD 使用 OpenID Connect 协议，因此确保的值`<Protocol>`是`"OpenIdConnect"`。

你需要更新`<Metadata>`XML 文件中的部分引用前面以反映您的特定的配置设置 Azure AD 租户。 在 XML 文件中，更新元数据值，如下所示：

1. 设置`<Item Key="METADATA">`到`https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`，其中`yourAzureADtenant`是你的 Azure AD 租户名称 (contoso.com)。
1. 打开你的浏览器并转到`METADATA`刚刚更新了你的 URL。
1. 在浏览器中，查找颁发者对象并复制其值。 它应如下所示： `https://sts.windows.net/{tenantId}/`。
1. 粘贴的值`<Item Key="ProviderName">`XML 文件中。
1. 设置`<Item Key="client_id">`到从应用程序注册的应用程序 ID。
1. 设置`<Item Key="IdTokenAudience">`到从应用程序注册的应用程序 ID。
1. 确保`<Item Key="response_types">`设置为`id_token`。
1. 确保`<Item Key="UsePolicyInRedirectUri">`设置为`false`。

你还必须链接到 Azure AD 在 Azure AD B2C 租户中注册的 Azure AD 机密`<ClaimsProvider>`信息：

* 在`<CryptographicKeys>`在上面的 XML 文件中的节，更新的值`StorageReferenceId`到你定义的机密的引用 ID (例如， `ContosoAppSecret`)。

### <a name="upload-the-extension-file-for-verification"></a>上载验证的扩展文件

现在，你已配置你的策略，以便 Azure AD B2C 知道如何与你的 Azure AD 目录进行通信。 尝试上载你的策略，只是为了确认它没有任何问题到目前为止的扩展文件。 为此，请执行以下操作：

1. 打开**所有策略**Azure AD B2C 租户中的边栏选项卡。
1. 检查**策略存在时覆盖它**框。
1. 上载扩展文件 (TrustFrameworkExtensions.xml)，并确保它不会通过验证。

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>注册用户旅程的 Azure AD 声明提供程序

现在需要将 Azure AD 标识提供程序添加到用户旅程之一。 此时，标识提供程序已设置，但不在任何 sign-高/登录屏幕中可用。 以使其可用，我们将创建重复的现有模板用户旅程，，然后修改它，以便它还具有 Azure AD 标识提供程序：

1. 打开你的策略 (例如，TrustFrameworkBase.xml) 的基文件。
1. 查找`<UserJourneys>`元素并将复制整个`<UserJourney>`包括的节点`Id="SignUpOrSignIn"`。
1. 打开扩展文件 (例如，TrustFrameworkExtensions.xml) 并查找`<UserJourneys>`元素。 如果元素不存在，添加一个。
1. 将粘贴整个`<UserJourney>`节点的子级复制`<UserJourneys>`元素。
1. 重命名新用户旅程的 ID (例如，重命名为`SignUpOrSignUsingContoso`)。

### <a name="display-the-button"></a>显示"按钮"

`<ClaimsProviderSelection>`元素是类似于 sign-高/登录屏幕上的标识提供程序按钮。 如果你添加`<ClaimsProviderSelection>`Azure AD 的元素，新的按钮显示时用户登陆页。 若要添加此元素：

1. 查找`<OrchestrationStep>`包括的节点`Order="1"`中你刚刚创建的用户迁移。
1. 添加以下代码：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. 设置`TargetClaimsExchangeId`为适当的值。 建议遵循相同的约定与其他人：  *\[ClaimProviderName\]Exchange*。

### <a name="link-the-button-to-an-action"></a>将按钮链接的操作

现在，你准备好一个按钮，你需要将其链接到某项操作。 操作，在这种情况下，适用于 Azure AD B2C 与 Azure AD 接收的令牌进行通信。 通过链接你的 Azure AD 声明提供程序的技术的配置文件，将按钮链接到某项操作：

1. 查找`<OrchestrationStep>`包括`Order="2"`中`<UserJourney>`节点。
1. 添加以下代码：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. 更新`Id`到相同的值的`TargetClaimsExchangeId`在上一部分中。
1. 更新`TechnicalProfileReferenceId`为技术配置文件的 ID 创建早期 (ContosoProfile)。

### <a name="upload-the-updated-extension-file"></a>上载更新的扩展文件

完成修改扩展文件。 省省吧。 然后上载该文件，并确保所有验证都成功。

### <a name="update-the-rp-file"></a>更新 RP 文件

你现在必须更新将启动你刚刚创建的用户旅程信赖方 (RP) 文件：

1. SignUpOrSignIn.xml 一份工作目录，然后将其重命名 （例如，其重命名为 SignUpOrSignInWithAAD.xml）。
1. 打开新文件并更新该`PolicyId`属性，则为`<TrustFrameworkPolicy>`具有一个唯一值 (例如，SignUpOrSignInWithAAD)。 <br> 这将是你的策略的名称 (例如，B2C\_1A\_SignUpOrSignInWithAAD)。
1. 修改`ReferenceId`属性中`<DefaultUserJourney>`以匹配新的用户旅程创建 (SignUpOrSignUsingContoso) 的 ID。
1. 保存所做的更改，并将文件上载。

## <a name="troubleshooting"></a>疑难解答

测试通过打开的边栏选项卡并单击您刚刚上载的自定义策略**立即运行**。 若要诊断问题，请阅读有关[疑难解答](active-directory-b2c-troubleshoot-custom.md)。

## <a name="next-steps"></a>后续步骤

提供反馈至[ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com)。
