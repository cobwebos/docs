---
title: 使用自定义策略添加声明和自定义用户输入 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中自定义用户输入并将声明添加到注册或登录旅程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c15d315d186af5622d9fa988945750d93e8eb11f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507558"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略添加声明和自定义用户输入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何在 Azure Active Directory (Azure AD) B2C 中向注册用户旅程添加新的用户提供的条目（声明）。  该条目配置为一个下拉列表和定义这是否是必需。

## <a name="prerequisites"></a>必备组件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。 测试注册或登录用户旅程，以便注册新的本地帐户，然后再继续操作。

## <a name="add-claims"></a>添加声明

可以通过注册或登录用户旅程从用户收集初始数据。 稍后可以通过配置文件编辑用户旅程收集其他声明。 每当 Azure AD B2C 收集的信息直接由用户以交互方式，标识体验框架将使用其自断言提供程序。


### <a name="define-the-claim"></a>定义声明

让我们要求用户提供其所在城市。 将以下元素添加到 TrustFrameworkBase 策略文件中的 **ClaimsSchema** 元素：

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

以下元素用于定义声明：

- **DisplayName** - 一个字符串，用于定义面向用户的标签。
- **UserHelpText** - 帮助用户了解需要提供哪些信息。
- **UserInputType** - 可以是文本框、单选、下拉列表或多选。

#### <a name="textbox"></a>TextBox

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

```xml
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

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

![下拉列表选项的屏幕截图](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)

```xml
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

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

![多项选择选项的屏幕截图](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)

```xml
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

### <a name="add-the-claim-to-the-user-journey"></a>将声明添加到用户旅程

1. 将声明作为 `<OutputClaim ClaimTypeReferenceId="city"/>` 添加到可在 TrustFrameworkBase 策略文件中找到的 `LocalAccountSignUpWithLogonEmail` 技术配置文件。 此技术配置文件使用 SelfAssertedAttributeProvider。

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

2. 将声明作为 `<PersistedClaim ClaimTypeReferenceId="city" />` 添加到 AAD-UserWriteUsingLogonEmail 技术配置文件，以便在从用户收集声明后将其写入 AAD 目录。 如果不想要在目录中保留该声明供将来使用，可以跳过此步骤。

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

3. 将 `<OutputClaim ClaimTypeReferenceId="city" />` 声明添加到当用户登录时会从目录读取内容的技术配置文件。

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

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```
   
4. 将 `<OutputClaim ClaimTypeReferenceId="city" />` 声明添加到 SignUporSignIn.xml 文件，以便在用户旅程成功完成后，在令牌中将此声明发送到应用程序。

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

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请务必使用包含 Azure AD 租户的目录，具体方法是：单击顶部菜单中的“目录和订阅筛选器”  ，再选择包含 Azure AD 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”   。
4. 选择“Identity Experience Framework (预览)”。 
5. 选择“上传自定义策略”，然后上传已更改的两个策略文件。 
2. 选择已上传的注册或登录策略，并单击“立即运行”按钮。 
3. 现在，应该可以使用电子邮件地址注册。

注册屏幕应类似于：

![修改后的注册选项屏幕截图](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

发送回应用程序的令牌包含 `city` 声明。

```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-remove-email-verification"></a>可选：删除电子邮件验证

若要跳过电子邮件验证，可以选择删除 `PartnerClaimType="Verified.Email"`。 在这种情况下，电子邮件地址是必需的，但除非删除了 “Required” = true，否则不会验证该地址。  请仔细考虑此选项是否适合用例。

在 TrustFrameworkBase 策略文件的 `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` 内，默认启用经验证的电子邮件：

```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>后续步骤

了解如何[在自定义配置文件编辑策略中使用自定义属性](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)。
