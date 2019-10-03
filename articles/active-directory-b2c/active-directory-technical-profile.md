---
title: 在 Azure Active Directory B2C 的自定义策略中定义 Azure Active Directory 技术配置文件 | Microsoft Docs
description: 在 Azure Active Directory B2C 的自定义策略中定义 Azure Active Directory 技术配置文件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4383980953147560b9e51e4ccab3032dd8173dd4
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064624"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 Azure Active Directory 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）提供对 Azure Active Directory 用户管理的支持。 本文介绍了与支持此标准化协议的声明提供程序进行交互的技术配置文件的详细信息。

## <a name="protocol"></a>Protocol

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 **handler** 属性必须包含协议处理程序程序集 `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` 的完全限定名称。

所有 Azure AD 的技术配置文件包括 **AAD-Common** 技术配置文件。 以下技术配置文件不会指定协议，因为协议是在 **AAD-Common** 技术配置文件中配置的：

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

## <a name="input-claims"></a>输入声明

以下技术配置文件包含社交和本地帐户的 **InputClaims**：

- 社交帐户技术配置文件 **AAD-UserReadUsingAlternativeSecurityId** 和 **AAD-UserWriteUsingAlternativeSecurityId** 包含 **AlternativeSecurityId** 声明。 此声明包含社交帐户用户标识符。
- 本地帐户技术配置文件 **AAD-UserReadUsingEmailAddress** 和 **AAD-UserWriteUsingLogonEmail** 包含 **email** 声明。 此声明包含本地帐户的登录名。
- 统一（本地和社交）技术配置文件 **AAD-UserReadUsingObjectId**、**AAD-UserWritePasswordUsingObjectId**、**AAD-UserWriteProfileUsingObjectId** 和 **AAD-UserWritePhoneNumberUsingObjectId** 包含 **objectId** 声明。 帐户的唯一标识符。

**InputClaimsTransformations** 元素可以包含用于修改输入声明或生成新输入声明的 **InputClaimsTransformation** 元素集合。

## <a name="output-claims"></a>输出声明

**OutputClaims** 元素包含 Azure AD 技术配置文件返回的声明列表。 可能需要将策略中定义的声明名称映射到 Azure Active Directory 中定义的名称。 如果设置了 `DefaultValue` 属性，则还可以包含 Azure Active Directory 不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

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

**PersistedClaims** 元素包含 Azure AD 应保存的所有值，以及策略的 ClaimsSchema 节中已定义的声明类型与 Azure AD 属性名称之间可能存在的映射的信息。

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
- 如果操作为 `Write` 或 `DeleteClaims`，则 **PersistedClaims** 元素中也必须包含此操作。
- **userPrincipalName** 声明的值必须采用 `user@tenant.onmicrosoft.com` 格式。
- **displayName** 声明是必需的，不能为空字符串。

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 技术提供程序操作

### <a name="read"></a>读取

**Read** 操作读取有关单个用户帐户的数据。 若要读取用户数据，需要提供一个键作为输入声明，例如 **objectId**、**userPrincipalName**、**signInNames**（任何类型，可以是用户名和基于电子邮件的帐户）或 **alternativeSecurityId**。

以下技术配置文件使用用户的 objectId 读取有关用户帐户的数据：

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

**Write** 操作创建或更新单个用户帐户。 若要写入用户帐户，需要提供一个键作为输入声明，例如 **objectId**、**userPrincipalName**、**signInNames.emailAddress** 或 **alternativeSecurityId**。

以下技术配置文件创建新社交帐户：

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

**DeleteClaims** 操作从提供的声明列表中清除信息。 若要从声明中删除信息，需要提供一个键作为输入声明，例如 **objectId**、**userPrincipalName**、**signInNames.emailAddress** 或 **alternativeSecurityId**。

以下技术配置文件删除声明：

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

**DeleteClaimsPrincipal** 操作从目录中删除单个用户帐户。 若要删除用户帐户，需要提供一个键作为输入声明，例如 **objectId**、**userPrincipalName**、**signInNames.emailAddress** 或 **alternativeSecurityId**。

以下技术配置文件使用用户主体名称从目录中删除用户帐户：

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

| 特性 | 必填 | 描述 |
| --------- | -------- | ----------- |
| 操作 | 是 | 要执行的操作。 可能的值：`Read`、`Write`、`DeleteClaims` 或 `DeleteClaimsPrincipal`。 |
| RaiseErrorIfClaimsPrincipalDoesNotExist | 否 | 如果目录中不存在该用户对象，则引发错误。 可能的值：`true` 或 `false`。 |
| UserMessageIfClaimsPrincipalDoesNotExist | 否 | 如果要引发错误（参阅 RaiseErrorIfClaimsPrincipalDoesNotExist 属性说明），则指定当用户对象不存在时要向用户显示的消息。 可将值[本地化](localization.md)。|
| RaiseErrorIfClaimsPrincipalAlreadyExists | 否 | 如果该用户对象已存在，则引发错误。 可能的值：`true` 或 `false`。|
| UserMessageIfClaimsPrincipalAlreadyExists | 否 | 如果要引发错误（参阅 RaiseErrorIfClaimsPrincipalAlreadyExists 属性说明），则指定当用户对象已存在时要向用户显示的消息。 可将值[本地化](localization.md)。|
| ApplicationObjectId | 否 | 扩展属性的应用程序对象标识符。 值：应用程序的 ObjectId。 有关详细信息，请参阅[在自定义配置文件编辑策略中使用自定义属性](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)。 |
| ClientId | 否 | 作为第三方访问租户的客户端标识符。 有关详细信息，请参阅[在自定义配置文件编辑策略中使用自定义属性](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) |














