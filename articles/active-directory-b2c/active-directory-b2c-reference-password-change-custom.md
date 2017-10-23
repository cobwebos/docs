---
title: "Azure Active Directory B2C：自助服务密码更改 | Microsoft Docs"
description: "本主题演示了如何在 Azure Active Directory B2C 中为使用者设置自助服务密码更改"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: ajalexander
ms.assetid: 712a7128-5788-4914-8a52-24e200aa4de1
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: b152c22c96da38f8724010504cc2711ab82af00a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C：在自定义策略中配置密码更改  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

使用密码更改功能，已登录的使用者（使用本地帐户）可以如[自助服务密码重置流](active-directory-b2c-reference-sspr.md)中所述更改其密码，不需要通过电子邮件验证来证明其真实性。 如果会话在使用者到达密码更改流时已过期，则会提示用户重新登录。 

## <a name="prerequisites"></a>先决条件

根据[入门](active-directory-b2c-get-started-custom.md)中所述配置一个 Azure AD B2C 租户，以完成本地帐户注册/登录。

## <a name="how-to-configure-password-change-in-custom-policy"></a>如何在自定义策略中配置密码更改

若要在自定义策略中配置密码更改，请在信任框架扩展策略中进行以下更改。 

## <a name="define-a-claimtype-oldpassword"></a>定义 ClaimType“oldPassword”

自定义策略的整体结构必须包括一个 `ClaimsSchema` 并定义一个新的 `ClaimType`“oldPassword”，如下所示： 

```XML
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="oldPassword">
        <DisplayName>Old Password</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter password</UserHelpText>
        <UserInputType>Password</UserInputType>
      </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

这些元素的用途如下所示：

- `ClaimsSchema` 定义正在验证的声明。  在本例中，将验证“旧密码”。 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>添加密码更改声明提供程序及其支持元素

密码更改声明提供程序将

1. 针对旧密码对用户进行身份验证
2. 并且，如果“新密码”与“确认新密码”匹配，则此值将存储在 B2C 数据存储中，因此密码成功更改。 

![img](images/passwordchange.jpg)

将以下声明提供程序添加到扩展策略 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="login-NonInteractive-PasswordChange">
          <DisplayName>Local Account SignIn</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
            <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
            <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
            <Item Key="ProviderName">https://sts.windows.net/</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">query</Item>
            <Item Key="scope">email openid</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
            <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
            <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
            <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
            <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Local Account Password Change</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
          <DisplayName>Change password (username)</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
            <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
```



### <a name="add-the-application-ids-to-your-custom-policy"></a>将应用程序 ID 添加到自定义策略

将应用程序 ID 添加到扩展文件 (`TrustFrameworkExtensions.xml`)：

1. 在扩展文件 (TrustFrameworkExtensions.xml) 中，找到元素 `<TechnicalProfile Id="login-NonInteractive">` 和 `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. 将 `IdentityExperienceFrameworkAppId` 的所有实例替换为[入门](active-directory-b2c-get-started-custom.md)中所述的标识体验框架应用程序的应用程序 ID。 下面是一个示例：

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. 将 `ProxyIdentityExperienceFrameworkAppId` 的所有实例替换为[入门](active-directory-b2c-get-started-custom.md)中所述的代理标识体验框架应用程序的应用程序 ID。

4. 保存扩展文件。



## <a name="create-a-password-change-user-journey"></a>创建密码更改用户旅程

```XML
 <UserJourneys>
    <UserJourney Id="PasswordChange">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

现已完成修改扩展文件。 保存并上传次文件。 确保所有验证都成功。



## <a name="create-a-relying-party-rp-file"></a>创建信赖方 (RP) 文件

接下来，更新用于启动创建的用户旅程的信赖方 (RP) 文件：

1. 在工作目录中创建 ProfileEdit.xml 的副本。 然后，将其重命名（例如 PasswordChange.xml）。
2. 打开新文件，并使用唯一值更新 `<TrustFrameworkPolicy>` 的 `PolicyId` 属性。 这是策略的名称（例如 PasswordChange）。
3. 修改 `<DefaultUserJourney>` 中的 `ReferenceId` 属性，使其与创建的新用户旅程的 `Id` 匹配（例如 PasswordChange）。
4. 保存更改，然后上传文件。
5. 若要测试所上传的自定义策略，请在 Azure 门户中，转到策略边栏选项卡，然后单击“立即运行”。




## <a name="link-to-password-change-sample-policy"></a>密码更改示例策略的链接

可以[在此处](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)找到示例策略。 










