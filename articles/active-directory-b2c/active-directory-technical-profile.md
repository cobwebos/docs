---
title: 在自定义策略中定义 Azure AD 技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 Azure Active Directory 技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330387"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 Azure Active Directory 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 为 Azure Active Directory 用户管理提供支持。 本文介绍了与支持此标准化协议的声明提供程序进行交互的技术配置文件的详细信息。

## <a name="protocol"></a>协议

"**Name****协议"** 元素的名称属性需要设置为`Proprietary`。 **handler** 属性必须包含协议处理程序程序集 `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` 的完全限定名称。

遵循[自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)Azure AD 技术配置文件包括**AAD 通用**技术配置文件。 Azure AD 技术配置文件未指定协议，因为协议在**AAD 通用**技术配置文件中配置：
 
- **AAD-UserReadUsingAlternativeSecurityId** 和 **AAD-UserReadUsingAlternativeSecurityId-NoError** - 在目录中查找社交帐户。
- **AAD-UserWriteUsingAlternativeSecurityId** - 创建新的社交帐户。
- **AAD-UserReadUsingEmailAddress** - 在目录中查找本地帐户。
- **AAD-UserWriteUsingLogonEmail** - 创建新的本地帐户。
- **AAD-UserWritePasswordUsingObjectId** - 更新本地帐户的密码。
- **AAD-UserWriteProfileUsingObjectId** - 更新本地或社交帐户的用户配置文件。
- **AAD-UserReadUsingObjectId** - 读取本地或社交帐户的用户配置文件。
- **AAD-UserWritePhoneNumberUsingObjectId** - 写入本地或社交帐户的 MFA 电话号码

以下示例演示了 **AAD-Common** 技术配置文件：

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

InputClaim 元素包含一个声明，用于查找目录中的帐户或创建新帐户。 所有 Azure AD 技术配置文件的输入声明集合中必须有一个输入声明元素。 可能需要将策略中定义的声明名称映射到 Azure Active Directory 中定义的名称。

要读取、更新或删除现有用户帐户，输入声明是一个密钥，用于唯一标识 Azure AD 目录中的帐户。 例如 **，objectid、****用户主名称**、**登录名称.email地址**、**登录名称.用户名**或**替代安全ID。** 

要创建新用户帐户，输入声明是唯一标识本地或联合帐户的密钥。 例如，本地帐户：**签名名称.电子邮件地址**，或**登录名称.用户名**。 对于联合帐户：**替代安全Id**。

[InputClaim 转换](technicalprofiles.md#inputclaimstransformations)元素可能包含用于修改输入声明或生成新声明的输入声明转换元素的集合。

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 元素包含 Azure AD 技术配置文件返回的声明列表。 可能需要将策略中定义的声明名称映射到 Azure Active Directory 中定义的名称。 如果设置了 `DefaultValue` 属性，则还可以包含 Azure Active Directory 不会返回的声明。

[OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

例如，**AAD-UserWriteUsingLogonEmail** 技术配置文件可创建本地帐户并返回以下声明：

- **objectId**：新帐户的标识符
- **newUser**：指示用户是否为新用户
- **authenticationSource**：将身份验证设置为 `localAccountAuthentication`
- **userPrincipalName**：新帐户的用户主体名称
- **signInNames.emailAddress**：帐户登录名，类似于 **email** 输入声明

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**持久声明元素**包含 Azure AD 应保留的所有值，以及策略中声明[架构](claimsschema.md)部分中已定义的声明类型和 Azure AD 属性名称之间可能映射的信息。

**AAD-UserWriteUsingLogonEmail** 技术配置文件，它可以创建新本地帐户并保存以下声明：

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

除非指定了包含 Azure AD 属性名称的 **PartnerClaimType** 属性，否则声明名称是 Azure AD 属性的名称。

## <a name="requirements-of-an-operation"></a>操作要求

- 所有 Azure AD 技术配置文件的声明包中必须刚好有一个 **InputClaim** 元素。
- [用户配置文件属性文章](user-profile-attributes.md)介绍支持的 Azure AD B2C 用户配置文件属性，可用于输入声明、输出声明和持久声明。 
- 如果操作为 `Write` 或 `DeleteClaims`，则 **PersistedClaims** 元素中也必须包含此操作。
- **userPrincipalName** 声明的值必须采用 `user@tenant.onmicrosoft.com` 格式。
- **displayName** 声明是必需的，不能为空字符串。

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 技术提供程序操作

### <a name="read"></a>读取

**Read** 操作读取有关单个用户帐户的数据。 以下技术配置文件使用用户的 objectId 读取有关用户帐户的数据：

```XML
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

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>写入

**Write** 操作创建或更新单个用户帐户。 以下技术配置文件创建新社交帐户：

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** 操作从提供的声明列表中清除信息。 以下技术配置文件删除声明：

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

**DeleteClaimsPrincipal** 操作从目录中删除单个用户帐户。 以下技术配置文件使用用户主体名称从目录中删除用户帐户：

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

以下技术配置文件使用 **alternativeSecurityId** 删除社交用户帐户：

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>元数据

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| Operation | 是 | 要执行的操作。 可能的值：`Read`、`Write`、`DeleteClaims` 或 `DeleteClaimsPrincipal`。 |
| RaiseErrorIfClaimsPrincipalDoesNotExist | 否 | 如果目录中不存在该用户对象，则引发错误。 可能的值：`true` 或 `false`。 |
| RaiseErrorIfClaimsPrincipalAlreadyExists | 否 | 如果该用户对象已存在，则引发错误。 可能的值：`true` 或 `false`。|
| ApplicationObjectId | 否 | 扩展属性的应用程序对象标识符。 值：应用程序的 ObjectId。 有关详细信息，请参阅[在自定义配置文件编辑策略中使用自定义属性](custom-policy-custom-attributes.md)。 |
| ClientId | 否 | 作为第三方访问租户的客户端标识符。 有关详细信息，请参阅[在自定义配置文件编辑策略中使用自定义属性](custom-policy-custom-attributes.md) |
| 包括索赔解决索赔处理  | 否 | 对于输入和输出声明，指定[索赔解析](claim-resolver-overview.md)是否包含在技术配置文件中。 可能的值：`true`或`false` （默认值）。 如果要在技术配置文件中使用声明解析器，则将此解决方案设置为`true`。 |

### <a name="ui-elements"></a>UI 元素
 
以下设置可用于配置故障时显示的错误消息。 元数据应在[自断言](self-asserted-technical-profile.md)的技术配置文件中配置。 错误消息可以[本地化](localization.md)。

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | 否 | 如果要引发错误（参阅 RaiseErrorIfClaimsPrincipalAlreadyExists 属性说明），则指定当用户对象已存在时要向用户显示的消息。 |
| UserMessageIfClaimsPrincipalDoesNotExist | 否 | 如果要引发错误（参阅 RaiseErrorIfClaimsPrincipalDoesNotExist 属性说明），则指定当用户对象不存在时要向用户显示的消息。 |


## <a name="next-steps"></a>后续步骤

请参阅以下文章，例如使用 Azure AD 技术配置文件：

- [在 Azure Active Directory B2C 中使用自定义策略添加声明和自定义用户输入](custom-policy-configure-user-input.md)














