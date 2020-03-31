---
title: 使用自定义策略使用LinkedIn帐户设置登录
titleSuffix: Azure AD B2C
description: 使用自定义策略在 Azure 活动目录 B2C 中使用LinkedIn帐户设置登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188165"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略设置使用 LinkedIn 帐户的登录

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用 Azure 活动目录 B2C （Azure AD B2C） 中的[自定义策略](custom-policy-overview.md)，从LinkedIn帐户为用户启用登录。

## <a name="prerequisites"></a>先决条件

- 完成 [Azure Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的步骤。
- LinkedIn帐户 - 如果您还没有帐户，[请创建一个帐户](https://www.linkedin.com/start/join)。
- LinkedIn页面 - 您需要[一个LinkedIn页面](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page)才能与您在下一节中创建LinkedIn应用程序相关联。

## <a name="create-an-application"></a>创建应用程序

若要将 LinkedIn 用作 Azure AD B2C 中的标识提供者，需要创建一个 LinkedIn 应用程序。

### <a name="create-app"></a>创建应用

1. 使用你的 LinkedIn 帐户凭据登录到 [LinkedIn 应用程序管理](https://www.linkedin.com/secure/developer?newapp=)网站。
1. 选择“创建应用”****。
1. 输入**应用名称**。
1. 输入与LinkedIn页名称对应**的公司**名称。 如果尚未创建主页，请创建LinkedIn主页。
1. （可选）输入**隐私政策 URL**。 它必须是有效的 URL，但不需要是可访问的终结点。
1. 输入**商务电子邮件**。
1. 上传**应用徽标**图像。 徽标图像必须为正方形，其尺寸必须至少为 100x100 像素。
1. 在"**产品"** 部分保留默认设置。
1. 审查以**法律术语**形式提供的信息。 如果您同意这些条款，请选中此框。
1. 选择“创建应用”****。

### <a name="configure-auth"></a>配置身份验证

1. 选择 **"Auth"** 选项卡。
1. 记录**客户端 ID**。
1. 揭示和记录**客户秘密**。
1. 在**OAuth 2.0 设置**下，添加以下**重定向 URL**。 将 `your-tenant` 替换为租户的名称。 使用租户名称**的所有小写字母**，即使该名称在 Azure AD B2C 中使用大写字母定义也是如此。

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录****。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
4. 在“概述”页上选择“标识体验框架”****。
5. 选择**策略键**，然后选择 **"添加**"。
6. 对于“选项”****，请选择 `Manual`。
7. 输入策略密钥的**名称**。 例如，`LinkedInSecret` 。 前缀*B2C_1A_* 将自动添加到密钥的名称中。
8. 在 **"机密**"中，输入您以前记录的客户端密钥。
9. 在“密钥用法”处选择 `Signature`。****
10. 单击 **“创建”**。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 LinkedIn 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过将LinkedIn帐户添加到策略扩展文件中**的声明提供程序**元素，将其定义为声明提供程序。

1. 在编辑器中打开*社交和本地帐户/[信任框架扩展.xml]* 文件。 此文件位于作为先决条件之一的一部分下载的[自定义策略初学者包][starter-pack]中。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

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
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

1. 将**client_id**的值替换为您以前记录的应用程序的LinkedIn的客户端 ID。
1. 保存文件。

### <a name="add-the-claims-transformations"></a>添加声明转换

技术配置文件LinkedIn需要**将"从链接响应中提取"** 和 **"从链接响应中提取"** 声明转换添加到索赔转换列表中。 如果文件中未定义**声明转换**元素，请添加父 XML 元素，如下所示。 声明转换还需要定义名为**nullStringClaim**的新声明类型。

在*TrustFramework 扩展.xml*文件顶部附近添加**构建块**元素。 有关示例，请参阅*TrustFrameworkBase.xml。*

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现在配置了策略，以便 Azure AD B2C 知道如何与LinkedIn帐户进行通信。 请尝试上传策略的扩展文件，以确认到目前为止没有任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”********。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件****。
3. 单击 **“上载”**。

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供程序已设置，但它在任何注册或登录屏幕中不可用。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 LinkedIn 标识提供者。

1. 在初学者包中打开*TrustFrameworkBase.xml*文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如，`SignUpSignInLinkedIn` 。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册或登录屏幕上的标识提供者按钮。 如果为 LinkedIn 帐户添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelections** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `LinkedInExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 LinkedIn 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，确保在 ID 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 ID。 例如，`LinkedIn-OAUTH` 。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过您在 B2C 租户中注册的应用程序与 Azure AD B2C 的通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInLinkedIn.xml*。
2. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如，`SignUpSignInLinkedIn` 。
3. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_linkedin`
4. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignLinkedIn)。
5. 保存更改并上传文件，然后选择列表中的新策略。
6. 确保在“选择应用程序”字段选择你创建的 Azure AD B2C 应用程序，然后单击“立即运行”对其进行测试********。

## <a name="migration-from-v10-to-v20"></a>从 v1.0 迁移到 v2.0

LinkedIn 最近[已将其 API 从 v1.0 更新到 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)。 要将现有配置迁移到新配置，请使用以下部分中的信息更新技术配置文件中的元素。

### <a name="replace-items-in-the-metadata"></a>替换元数据中的项

在**技术配置文件**的现有**元数据**元素中，从以下**项**元素更新：

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

更改为：

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>将项目添加到元数据

在**技术配置文件**的**元数据**中，添加以下**项**元素：

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>更新输出要求

在**技术配置文件**的现有**输出声明**中，从以下内容更新以下**输出声明**元素：

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

更改为：

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>添加新的输出声明转换元素

在**技术配置文件**的 **"输出要求转换"中**，添加以下**输出要求转换**元素：

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>定义新的声明转换和声明类型

在最后一步中，您添加了需要定义的新声明转换。 要定义声明转换，请将它们添加到**声明转换**列表中。 如果文件中未定义**声明转换**元素，请添加父 XML 元素，如下所示。 声明转换还需要定义名为**nullStringClaim**的新声明类型。

构建**块**元素应在文件顶部附近添加。 例如，请参阅*信任框架基础.xml。*

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>获取电子邮件地址

作为从 v1.0 迁移到 v2.0 LinkedIn迁移的一部分，需要对另一个 API 进行额外调用才能获取电子邮件地址。 如果您在注册期间需要获取电子邮件地址，请执行以下操作：

1. 完成上述步骤，允许 Azure AD B2C 与LinkedIn联合，以便用户登录。 作为联合的一部分，Azure AD B2C 接收LinkedIn的访问令牌。
2. 将LinkedIn访问令牌保存到声明中。 [在此处查看说明](idp-pass-through-custom.md)。
3. 添加以下声明提供程序，将请求添加到LinkedIn的`/emailAddress`API。 为了授权此请求，您需要LinkedIn访问令牌。

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 将以下业务流程步骤添加到用户旅程中，以便在用户使用LinkedIn登录时触发 API 声明提供程序。 请确保适当地更新`Order`号码。 在触发LinkedIn技术配置文件的业务流程步骤之后立即添加此步骤。

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

在注册期间从LinkedIn获取电子邮件地址是可选的。 如果您选择不从LinkedIn获取电子邮件，但在注册期间需要一封电子邮件，则用户需要手动输入电子邮件地址并验证它。

有关使用LinkedIn标识提供程序的策略的完整示例，请参阅[自定义策略初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)。

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
