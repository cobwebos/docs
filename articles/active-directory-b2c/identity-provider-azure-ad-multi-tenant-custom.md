---
title: 按自定义策略设置多租户 Azure AD 的登录
titleSuffix: Azure AD B2C
description: 使用 Azure 活动目录 B2C 中的自定义策略添加多租户 Azure AD 标识提供程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ad51e113a752e0692cb377a83d4819b4e284bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188425"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略为多租户 Azure Active Directory 设置登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文展示了如何在 Azure AD B2C 中通过使用[自定义策略](custom-policy-overview.md)为使用 Azure Active Directory (Azure AD) 的多租户终结点的用户实现登录。 这允许来自多个 Azure AD 租户的用户使用 Azure AD B2C 登录，而无需为每个租户配置标识提供程序。 但是，任何这些租户中的来宾成员都将无法**** 登录。 为此，你需要[单独配置每个租户](identity-provider-azure-ad-single-tenant-custom.md)。

## <a name="prerequisites"></a>先决条件

完成 [Azure Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的步骤。

## <a name="register-an-application"></a>注册应用程序

若要让用户从特定的 Azure AD 组织登录，需要在组织 Azure AD 租户中注册一个应用程序。

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 确保使用的目录包含组织 Azure AD 租户（例如，contoso.com）。 在顶部菜单中选择**目录 + 订阅筛选器**，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择“新注册”。****
1. 输入应用程序的**名称**。 例如，`Azure AD B2C App` 。
1. 选择此应用程序**的任何组织目录中的帐户**。
1. 对于“重定向 URI”****，接受值 **Web**，并以全小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如，`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

1. 选择“注册”****。 记录“应用程序(客户端) ID”，以便在后续步骤中使用****。
1. 依次选择“证书和机密”、“新建客户端机密”。********
1. 输入机密**的描述**，选择过期，然后选择 **"添加**"。 记录机密**的值**，以便在后面的步骤中使用。

## <a name="configuring-optional-claims"></a>配置可选声明

如果要从 Azure AD `family_name` `given_name`获取 和 声明，可以在 Azure 门户 UI 或应用程序清单中为应用程序配置可选声明。 有关详细信息，请参阅[如何向 Azure AD 应用提供可选声明](../active-directory/develop/active-directory-optional-claims.md)。

1. 登录到 Azure[门户](https://portal.azure.com)。 搜索并选择“Azure Active Directory”****。
1. 在“管理”部分，选择“应用注册”。********
1. 在列表中选择要为其配置可选声明的应用程序。
1. 在“管理”部分，选择“令牌配置(预览)”。********
1. 选择“添加可选声明”。****
1. 选择要配置的令牌类型。
1. 选择要添加的可选声明。
1. 单击 **“添加”**。

## <a name="create-a-policy-key"></a>创建策略密钥

需将创建的应用程序密钥存储在 Azure AD B2C 租户中。

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的**目录 + 订阅筛选器**，然后选择包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 在“策略”下，选择“Identity Experience Framework”。********
1. 选择**策略键**，然后选择 **"添加**"。
1. 对于“选项”****，请选择 `Manual`。
1. 输入策略密钥的**名称**。 例如，`AADAppSecret` 。  前缀`B2C_1A_`在创建时会自动添加到密钥的名称中，因此其在下一节中的 XML 中的引用是*B2C_1A_AADAppSecret*。
1. 在 **"秘密**"中，输入您之前记录的客户机密。
1. 在“密钥用法”处选择 `Signature`。****
1. 选择 **“创建”**。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Azure AD 登录，则需将 Azure AD 定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中将 Azure AD 添加到 ClaimsProvider 元素****。

1. 打开 *TrustFrameworkExtensions.xml* 文件。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. 在 ClaimsProvider 元素下，将 Domain 的值更新为可用于与其他标识提供者进行区分的唯一值********。
1. 在 **"技术配置文件"** 元素下，更新**DisplayName**的值，例如`Contoso Employee`。 此值会显示在登录页中的登录按钮上。
1. **将client_id**设置为之前注册的 Azure AD 多租户应用程序的应用程序 ID。
1. 在**加密密钥**下，将**StorageReferenceId**的值更新为前面创建的策略密钥的名称。 例如，`B2C_1A_AADAppSecret` 。

### <a name="restrict-access"></a>限制访问

> [!NOTE]
> 使用 `https://login.microsoftonline.com/` 作为 **ValidTokenIssuerPrefixes** 的值将允许所有 Azure AD 用户登录到你的应用程序。

你需要更新有效令牌颁发者列表，并且仅允许可以登录的一组特定 Azure AD 租户用户进行访问。

要获取这些值，请查看希望让用户登录的每个 Azure AD 租户的 OpenID Connect 发现元数据。 元数据 URL 的格式类似于`https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`，Azure `your-tenant` AD 租户名称位于此名称。 例如：

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

对应用于登录的每个 Azure AD 租户执行以下步骤：

1. 打开浏览器并转到租户的 OpenID 连接元数据 URL。 查找**颁发对象**并记录其值。 它应该看起来类似于`https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`。
1. 复制该值并将其粘贴到**ValidToken 问题前缀键中**。 使用逗号分隔多个颁发者。 上`ClaimsProvider`一个 XML 示例中将显示一个包含两个颁发者的示例。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

到目前为止，您已经配置了策略，以便 Azure AD B2C 知道如何与 Azure AD 目录进行通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”********。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件****。
3. 选择 **"上传**"。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 Azure AD 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInContoso` 。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 Azure AD 添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 查找在*TrustFramework 扩展.xml*中创建的用户旅程中包含`Order="1"`的**业务流程步骤**元素。
1. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `AzureADExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 Azure AD 通信以接收令牌。 可通过链接 Azure AD 声明提供程序的技术配置文件来将按钮链接到操作。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，确保在 **Id** 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    将**技术配置文件参考 Id**的值更新到您之前创建的技术配置文件的**ID。** 例如，`Common-AAD` 。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过您在 B2C 租户中注册的应用程序与 Azure AD B2C 的通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新启动您创建的用户旅程的依赖方 （RP） 文件：

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignContoso.xml*。
1. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInContoso` 。
1. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如，`http://contoso.com/B2C_1A_signup_signin_contoso` 。
1. 更新**默认用户旅程**中的**参考 Id**属性的值，以匹配您之前创建的用户旅程的 ID。 例如，*注册登录 Contoso*。
1. 保存更改并上传文件。
1. 在 **"自定义策略"** 下，在列表中选择新策略。
1. 在 **"选择应用程序**下拉列表"中，选择之前创建的 Azure AD B2C 应用程序。 例如 *，testapp1*。
1. 立即复制 **"立即运行"终结点**并在专用浏览器窗口中打开它，例如，Google Chrome 中的隐身模式或 Microsoft Edge 中的 InPrivate 窗口。 通过在专用浏览器窗口中打开，您可以通过不使用任何当前缓存的 Azure AD 凭据来测试完整的用户旅程。
1. 选择 Azure AD 登录按钮，例如*Contoso 员工*，然后在 Azure AD 组织租户之一中输入用户的凭据。 系统将要求您授权应用程序，然后输入您的个人资料信息。

如果登录过程成功，浏览器将重定向到`https://jwt.ms`，显示 Azure AD B2C 返回的令牌的内容。

要测试多租户登录功能，请使用存在另一个 Azure AD 租户的用户的凭据执行最后两个步骤。

## <a name="next-steps"></a>后续步骤

使用自定义策略时，有时在开发策略期间对策略进行故障排除时可能需要其他信息。

为了帮助诊断问题，可以暂时将策略置于"开发人员模式"，并使用 Azure 应用程序见解收集日志。 了解如何在[Azure 活动目录 B2C 中：收集日志](troubleshoot-with-application-insights.md)。
