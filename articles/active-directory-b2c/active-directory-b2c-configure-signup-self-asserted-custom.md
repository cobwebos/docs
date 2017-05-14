---
title: "Azure Active Directory B2C：修改自定义策略中的注册和配置自我断言提供程序"
description: "有关在注册中添加声明和配置用户输入的演练"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: tbd
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/29/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 9f7ee61c9c2ee3c68b215dde81e718db183d3870
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017

---
# <a name="azure-active-directory-b2c-modify-signup-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C：修改注册以添加新声明和配置用户输入。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何在注册用户旅程中添加新用户提供的条目（声明）。  我们要将该条目配置为下拉列表，并根据需要进行定义。

## <a name="prerequisites"></a>先决条件

* 完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。  测试注册/登录用户旅程，以便在继续之前注册新的本地帐户。


可以通过注册/登录从用户收集初始数据。  稍后可以通过配置文件编辑用户旅程收集其他声明。 每当 Azure AD B2C 以交互方式直接从用户收集信息时，标识体验框架都会使用其 `selfasserted provider`。 每当使用此提供程序时，都要执行以下步骤。


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>定义声明、其显示名称和用户输入类型
我们请求用户输入其所在城市。  将以下元素添加到 TrustFrameWorkExtensions 策略文件中的 `<ClaimsSchema>` 元素：
```
        <ClaimType Id="city">
            <DisplayName>city</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
```
此处可以选择其他选项来自定义声明。  有关完整的架构，请参阅**标识体验框架技术参考指南**。  本指南即将在参考部分中发布。

* <DisplayName> 是一个字符串，用于定义面向用户的*标签*

* <UserHelpText> 可帮助用户了解需要提供哪些信息

* <UserInputType> 提供下面突出显示的四个选项：
    * `TextBox`
```
        <ClaimType Id="city">
            <DisplayName>city where you work</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
```

    * `RadioSingleSelectduration` 强制单项选择。
```
    <ClaimType Id="city">
      <DisplayName>city where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>RadioSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
        <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
        <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
      </Restriction>
    </ClaimType>
```

    * `DropdownSingleSelect` 只允许选择一个有效值。

![下拉列表选项的屏幕截图](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


```
<ClaimType Id="city">
       <DisplayName>city where you work</DisplayName>
       <DataType>string</DataType>
       <UserInputType>DropdownSingleSelect</UserInputType>
       <Restriction>
         <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
         <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
         <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
       </Restriction>
     </ClaimType>
```


* `CheckboxMultiSelect` 允许选择一个或多个值。

![多项选择选项的屏幕截图](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


```
<ClaimType Id="city">
        <DisplayName>Receive updates from which cities?</DisplayName>
        <DataType>string</DataType>
        <UserInputType>CheckboxMultiSelect</UserInputType>
        <Restriction>
          <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
          <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
          <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
        </Restriction>
      </ClaimType>
```

## <a name="add-the-claim-to-the-singupsign-user-journey"></a>将声明添加到注册/登录用户旅程

1. 将声明作为 `<OutputClaim ClaimTypeReferenceId="city"/>` 添加到技术配置文件 `LocalAccountSignUpWithLogonEmail`（可在 TrustFrameworkBase 策略文件中找到）。  请注意，此技术配置文件使用 SelfAssertedAttributeProvider。

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <DisplayName>Email signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
            <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
            <Item Key="language.button_continue">Create</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" />
            <OutputClaim ClaimTypeReferenceId="newUser" />
<!-- Optional claims, to be collected from the user -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surName" />

            <OutputClaim ClaimTypeReferenceId="city"/>

          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
          </ValidationTechnicalProfiles>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
        </TechnicalProfile>
```

2. 将声明作为 `<PersistedClaim ClaimTypeReferenceId="city" />` 添加到 AAD-UserWriteUsingLogonEmail，以便在从用户收集声明后将其写入 AAD 目录。 如果不想要在目录中保留该声明供将来使用，可以跳过此步骤。

```xml
<!-- Technical profiles for local accounts -->
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
            <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
            <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
            <!-- Optional claims. -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />

            <PersistedClaim ClaimTypeReferenceId="city" />

          </PersistedClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
          </OutputClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
        </TechnicalProfile>
```

3. 将声明作为 `<OutputClaim ClaimTypeReferenceId="city" />` 添加到在用户登录时读取目录的技术配置文件

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
  </InputClaims>
  <OutputClaims>
    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />

    <OutputClaim ClaimTypeReferenceId="city" />

  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

4. 将 `<OutputClaim ClaimTypeReferenceId="city" />` 添加到 RP 策略文件 SignUporSignIn.xml，以便在用户旅程成功完成后，在令牌中将此声明发送到应用程序。


```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />

        <OutputClaim ClaimTypeReferenceId="city" />

      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ```

## <a name="test-the-custom-policy-using-run-now"></a>使用“立即运行”测试自定义策略

       1. Open the **Azure AD B2C Blade** and navigate to **Identity Experience Framework > Custom policies**.
       2. Select the custom policy that you uploaded, and click the **Run now** button.
       3. You should be able to sign up using an email address.

测试模式下的注册屏幕应类似于：![修改的注册选项的屏幕截图](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  返回给应用程序的令牌现在包含 `city` 声明，如下所示
```
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification-from-signup-journey"></a>（可选）从注册旅程中删除电子邮件验证

若要跳过电子邮件验证，策略作者可以选择删除 `PartnerClaimType="Verified.Email"`。 电子邮件地址是必需的，但除非删除了 “Required” = true，否则不会验证该地址。  请仔细考虑此选项是否适合你的用例！

在初学者包中 TrustFrameworkBase 策略文件的 `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` 内，默认已启用验证的电子邮件：
```
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
            ```

## Next steps


Add the new claim to the flows for social account logins by changing the TechnicalProfiles listed below. These are used by social/federated account logins to write and read the user data using the alternativeSecurityId as the locator.
```
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

