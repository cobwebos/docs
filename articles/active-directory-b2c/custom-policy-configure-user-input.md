---
title: 在自定义策略中添加声明和自定义用户输入
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中自定义用户输入并将声明添加到注册或登录旅程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473670"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略添加声明和自定义用户输入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何在 Azure Active Directory B2C （Azure AD B2C）的注册旅程中收集新属性。 你将获得用户的 "城市"，将其配置为下拉箭头，并定义是否需要提供此项。

> [!NOTE]
> 此示例使用内置声明 "city"。 相反，您可以选择一个受支持的[Azure AD B2C 内置属性](user-profile-attributes.md)或自定义属性。 若要使用自定义属性，请[在策略中启用自定义属性](custom-policy-custom-attributes.md)。 若要使用不同的内置或自定义属性，请将 "city" 替换为所选的属性，例如内置属性*jobTitle*或*extension_loyaltyId*之类的自定义属性。  

你可以使用注册或登录用户旅程收集用户的初始数据。 稍后可以通过配置文件编辑用户旅程收集其他声明。 Azure AD B2C 以交互方式直接从用户那里收集信息，标识体验框架将使用其[自断言技术配置文件](self-asserted-technical-profile.md)。 在此示例中，你：

1. 定义 "city" 声明。 
1. 要求用户提供其城市。
1. 将城市保存到 Azure AD B2C 目录中的用户配置文件。
1. 在每次登录时，请从 Azure AD B2C 目录中读取 city 声明。
1. 登录或注册后，将城市返还给信赖方应用程序。  

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 你应具有用于注册的工作自定义策略，并使用社交帐户和本地帐户进行登录。

## <a name="define-a-claim"></a>定义声明

声明在 Azure AD B2C 策略执行过程中提供数据的临时存储。 声明[架构](claimsschema.md)是声明声明的位置。 以下元素用于定义声明：

- **DisplayName** - 一个字符串，用于定义面向用户的标签。
- [DataType](claimsschema.md#datatype) -声明的类型。
- **UserHelpText** - 帮助用户了解需要提供哪些信息。
- [UserInputType](claimsschema.md#userinputtype) -输入控件（如文本框、广播选择、下拉列表或多个选项）的类型。

打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>。

1. 搜索 BuildingBlocks[](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 " [ClaimsSchema](claimsschema.md) " 元素。 如果该元素不存在，请添加该元素。
1. 将 city 声明添加到**ClaimsSchema**元素。  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>向用户界面添加声明

以下技术配置文件[自断言](self-asserted-technical-profile.md)，在用户需要提供输入时被调用：

- **LocalAccountSignUpWithLogonEmail** -本地帐户注册流。
- **SelfAsserted-** 联合帐户首次用户登录。
- **SelfAsserted-self-asserted-profileupdate** -编辑配置文件流。

若要在注册期间收集城市声明，则必须将其作为输出声明添加到`LocalAccountSignUpWithLogonEmail`技术配置文件中。 覆盖扩展文件中的此技术配置文件。 指定输出声明的完整列表，以控制声明在屏幕上的显示顺序。 找到 **ClaimsProviders** 元素。 添加新的 ClaimsProviders，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

若要在使用联合帐户初次登录后收集 city 声明，必须将其作为输出声明添加到`SelfAsserted-Social`技术配置文件。 为了使本地和联合帐户用户以后能够编辑其配置文件数据，请将输出声明添加到`SelfAsserted-ProfileUpdate`技术配置文件中。 覆盖扩展文件中的这些技术配置文件。 指定输出声明的完整列表，以控制声明在屏幕上的显示顺序。 找到 **ClaimsProviders** 元素。 添加新的 ClaimsProviders，如下所示：

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>读取和写入声明

以下技术配置文件是[Active Directory 技术配置文件，这些配置文件](active-directory-technical-profile.md)将数据读写到 Azure Active Directory 中。  
使用`PersistedClaims`将数据写入用户配置文件，并`OutputClaims`从各自 Active Directory 技术配置文件中的用户配置文件中读取数据。

覆盖扩展文件中的这些技术配置文件。 找到 **ClaimsProviders** 元素。  添加新的 ClaimsProviders，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>在令牌中包括声明 

若要将城市声明返回给信赖方应用程序，请将输出声明添加到<em> `SocialAndLocalAccounts/` </em>该文件。 用户成功旅程后，会将输出声明添加到令牌中，并将其发送到应用程序。 修改 "信赖方" 部分中的 "技术配置文件" 元素，以将城市添加为输出声明。
 
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
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含 Azure AD 租户的目录，确保使用的是包含 Azure AD 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
4. 选择 "**标识体验框架**"。
5. 选择“上传自定义策略”，然后上传已更改的两个策略文件。****
2. 选择已上传的注册或登录策略，并单击“立即运行”按钮。****
3. 现在，应该可以使用电子邮件地址注册。

注册屏幕应类似于以下屏幕截图：

![修改后的注册选项屏幕截图](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

发送回应用程序的令牌包含 `city` 声明。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>后续步骤

- 详细了解 IEF 参考中的[ClaimsSchema](claimsschema.md)元素。
- 了解如何[使用自定义配置文件编辑策略中的自定义属性](custom-policy-custom-attributes.md)。
