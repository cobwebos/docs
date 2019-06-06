---
title: 在 Azure Active Directory B2C 中使用自定义策略为多租户 Azure AD 标识提供者设置登录 | Microsoft Docs
description: 使用自定义策略添加多租户 Azure AD 标识提供者 - Azure Active Directory B2C。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 72d01d6927ee421d01a831244acf65c44a084354
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508667"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略为多租户 Azure Active Directory 设置登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文展示了如何在 Azure AD B2C 中通过使用[自定义策略](active-directory-b2c-overview-custom.md)为使用 Azure Active Directory (Azure AD) 的多租户终结点的用户实现登录。 这将允许来自多个 Azure AD 租户的用户登录到 Azure AD B2C，而无需为每个租户配置技术提供商。 但是，任何这些租户中的来宾成员都将无法  登录。 为此，你需要[单独配置每个租户](active-directory-b2c-setup-aad-custom.md)。

>[!NOTE]
>以下说明中将 `Contoso.com` 用于组织 Azure AD 租户，将 `fabrikamb2c.onmicrosoft.com` 用作 Azure AD B2C 租户。

## <a name="prerequisites"></a>必备组件

完成 [Azure Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。

## <a name="register-an-application"></a>注册应用程序

若要让用户从特定的 Azure AD 组织登录，需要在组织 Azure AD 租户中注册一个应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含组织 Azure AD 租户 (contoso.com) 的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”   。
4. 选择“新建应用程序注册”  。
5. 输入应用程序的名称。 例如，`Azure AD B2C App`。
6. 对于“应用程序类型”，选择 `Web app / API`  。
7. 对于“登录 URL”，输入以下 URL（全部为小写字母），并将 `your-tenant` 替换为你的 Azure AD B2C 租户的名称 (fabrikamb2c.onmicrosoft.com)  ：

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. 单击**创建**。 复制“应用程序 ID”供后续使用  。
9. 选择应用程序，然后选择“设置”  。
10. 选择“密钥”，输入密钥说明，选择持续时间，然后单击“保存”   。 复制显示的密钥值供后续使用。
11. 在“设置”  下，选择“属性”  ，将“多租户”  设置为 `Yes`，然后单击“保存”。 

## <a name="create-a-policy-key"></a>创建策略密钥

需将创建的应用程序密钥存储在 Azure AD B2C 租户中。

1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
3. 在“概述”页上，选择“标识体验框架 - 预览”  。
4. 选择“策略密钥”  ，然后选择“添加”  。
5. 对于“选项”  ，请选择 `Manual`。
6. 输入策略密钥的**名称**。 例如，`ContosoAppSecret`。  前缀 `B2C_1A_` 会自动添加到密钥名称。
7. 在“机密”中，输入之前记录的应用程序密钥  。
8. 在“密钥用法”处选择 `Signature`。 
9. 单击**创建**。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Azure AD 登录，则需将 Azure AD 定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。 

要将 Azure AD 定义为声明提供程序，可在策略的扩展文件中将 Azure AD 添加到 ClaimsProvider 元素  。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

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
        
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
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

4. 在 ClaimsProvider 元素下，将 Domain 的值更新为可用于与其他标识提供者进行区分的唯一值   。
5. 在 **TechnicalProfile** 元素下，更新 **DisplayName** 的值。 此值会显示在登录屏幕中的登录按钮上。
6. 将 **client_id** 设置为 Azure AD 多租户应用注册中的应用程序 ID。

### <a name="restrict-access"></a>限制访问

> [!NOTE]
> 使用 `https://sts.windows.net` 作为 **ValidTokenIssuerPrefixes** 的值将允许所有 Azure AD 用户登录到你的应用程序。

你需要更新有效令牌颁发者列表，并且仅允许可以登录的一组特定 Azure AD 租户用户进行访问。 若要获取值，需要查看希望让用户从中登录的每个特定 Azure AD 租户的元数据。 数据格式如下所示：`https://login.windows.net/your-tenant/.well-known/openid-configuration`，其中，`your-tenant` 是 Azure AD 租户名称（contoso.com 或其他任何 Azure AD 租户）。

1. 打开浏览器并转到 **METADATA** URL，查找**颁发者**对象并复制其值。 该对象应与下面类似：`https://sts.windows.net/tenant-id/`。
2. 复制并粘贴 **ValidTokenIssuerPrefixes** 密钥的值， 可以添加多个值并使用逗号将其隔开。 上述示例 XML 中做了相关注释。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现已配置策略，因此 Azure AD B2C 知道如何与 Azure AD 目录通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”   。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件  。
3. 单击“上传” 。 

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册/登录屏幕中。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 Azure AD 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInContoso`。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册/登录屏幕上的标识提供者按钮。 如果为 Azure AD 添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `AzureADExchange`：

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
    
    将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 **Id**。 例如，`Common-AAD`。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过在租户中创建的应用程序与 Azure AD B2C 进行通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
4. 选择“应用程序”，然后选择“添加”   。
5. 输入应用程序的名称，例如 *testapp1*。
6. 对于“Web 应用/Web API”，请选择 `Yes`，然后为“回复 URL”输入 `https://jwt.ms`   。
7. 单击**创建**。

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignContoso.xml*。
2. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInContoso`。
3. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_contoso`
4. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignContoso)。
5. 保存更改并上传文件，然后选择列表中的新策略。
6. 确保在“选择应用程序”字段选择你创建的 Azure AD B2C 应用程序，然后单击“立即运行”对其进行测试   。
