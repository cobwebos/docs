---
title: TechnicalProfiles | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 TechnicalProfiles 元素。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c17159e1fa901e8219d1727769d234719c4321c6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856547"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**TechnicalProfiles** 元素包含声明提供程序支持的一组技术配置文件。 每个声明提供程序必须包含一个或多个用于确定终结点的技术配置文件，以及与该声明提供程序通信所需的协议。 一个声明提供程序可以包含多个技术配置文件。

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 元素包含以下属性：

| 属性 | 必选 | 说明 |
|---------|---------|---------|
| ID | 是 | 技术配置文件的唯一标识符。 可以使用此标识符从策略文件中的其他元素引用技术配置文件。 例如，**OrchestrationSteps** 和 **ValidationTechnicalProfile**。 |

**TechnicalProfile** 包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 域 | 0:1 | 技术配置文件的域名。 例如，如果技术配置文件指定 Facebook 标识提供者，则域名是 Facebook.com。 |
| DisplayName | 0:1 | 可向用户显示的技术配置文件名称。 |
| 说明 | 0:1 | 可向用户显示的技术配置文件说明。 |
| 协议 | 0:1 | 用来与另一方通信的协议。 |
| 元数据 | 0:1 | 在事务处理过程中，协议用来与终结点通信的键/值对集合。 |
| InputTokenFormat | 0:1 | 输入令牌的格式。 可能的值：`JSON`、`JWT`、`SAML11` 或 `SAML2`。 `JWT` 值表示符合 IETF 规范的 JSON Web 令牌。 `SAML11` 值表示符合 OASIS 规范的 SAML 1.1 安全令牌。  `SAML2` 值表示符合 OASIS 规范的 SAML 2.0 安全令牌。 |
| OutputTokenFormat | 0:1 | 输出令牌的格式。 可能的值：`JSON`、`JWT`、`SAML11` 或 `SAML2`。 |
| CryptographicKeys | 0:1 | 技术配置文件中使用的加密密钥列表。 |
| InputClaimsTransformations | 0:1 | 事先定义的、对在将任何声明发送到声明提供程序或信赖方之前应执行的声明转换的引用列表。 |
| InputClaims | 0:1 | 事先定义的、对在技术配置文件中用作输入的声明类型的引用列表。 |
| PersistedClaims | 0:1 | 事先定义的、对技术配置文件相关的声明提供程序所保存的声明类型的引用列表。 |
| OutputClaims | 0:1 | 事先定义的、对在技术配置文件中用作输出的声明类型的引用列表。 |
| OutputClaimsTransformations | 0:1 | 事先定义的、对在从声明提供程序收到声明之后应执行的声明转换的引用列表。 |
| ValidationTechnicalProfiles | 0:n | 对技术配置文件用来进行验证的其他技术配置文件的引用列表。 有关详细信息，请参阅[验证技术配置文件](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | 控制令牌中使用者名称的生成，其中的使用者名称与声明分开指定。 例如 OAuth 或 SAML。  |
| IncludeClaimsFromTechnicalProfile | 0:1 | 要将其中的所有输入和输出声明添加到此技术配置文件的技术配置文件的标识符。 必须在同一个策略文件中定义被引用的技术配置文件。 | 
| IncludeTechnicalProfile |0:1 | 要将其中的所有数据添加到此技术配置文件的技术配置文件的标识符。 被引用的技术配置文件必须在同一个策略文件中。 |
| UseTechnicalProfileForSessionManagement | 0:1 | 用于会话管理的另一个技术配置文件。 |
|EnabledForUserJourneys| 0:1 |控制是否在用户旅程中执行技术配置文件。  |

### <a name="protocol"></a>协议

Protocol 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| 名称 | 是 | Azure AD B2C 支持的有效协议的名称，用作技术配置文件的一部分。 可能的值：`OAuth1`、`OAuth2`、`SAML2`、`OpenIdConnect`、`WsFed`、`WsTrust`、`Proprietary`、`session management`、`self-asserted` 或 `None`。 |
| Handler | 否 | 当协议名称设置为 `Proprietary` 时，指定 Azure AD B2C 用来确定协议处理程序的程序集的完全限定名称。 |

### <a name="metadata"></a>元数据

**Metadata** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| Item | 0:n | 与技术配置文件相关的元数据。 每种类型的技术配置文件具有一组不同的元数据项。 有关详细信息，请参阅“技术配置文件类型”部分。 |

#### <a name="item"></a>Item

**Metadata** 元素的 **Item** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| 密钥 | 是 | 元数据密钥。 请参阅每种技术配置文件类型了解元数据项的列表。 |

### <a name="cryptographickeys"></a>CryptographicKeys

**CryptographicKeys** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 密钥 | 1:n | 此技术配置文件中使用的加密密钥。 |

#### <a name="key"></a>密钥

**Key** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ID | 否 | 从策略文件中的其他元素引用的特定密钥对的唯一标识符。 |
| StorageReferenceId | 是 | 从策略文件中的其他元素引用的存储密钥容器的标识符。 |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

**InputClaimsTransformations** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 在将任何声明发送到声明提供程序或信赖方之前应执行的声明转换的标识符。 声明转换可用于修改现有的或生成新的 ClaimsSchema 声明。 |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的声明转换的标识符。 |

### <a name="inputclaims"></a>InputClaims

InputClaims 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 预期的输入声明类型。 |

#### <a name="inputclaim"></a>InputClaim 

InputClaim 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在策略文件或父策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |
| DefaultValue | 否 | 当 ClaimTypeReferenceId 指示的声明不存在时用来创建声明的默认值。技术配置文件可将生成的声明用作 InputClaim。 |
| PartnerClaimType | 否 | 指定的策略声明类型映射到的外部合作伙伴声明类型的标识符。 如果未指定 PartnerClaimType 属性，则指定的策略声明类型将映射到同名的合作伙伴声明类型。 当声明类型名称不同于另一方时，请使用此属性。 例如，第一个声明名称为“givenName”，而合作伙伴使用名为“first_name”的声明。 |

### <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 要保存的声明类型。 |

#### <a name="persistedclaim"></a>PersistedClaim 

**PersistedClaim** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在策略文件或父策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |
| DefaultValue | 否 | 当 ClaimTypeReferenceId 指示的声明不存在时用来创建声明的默认值。技术配置文件可将生成的声明用作 InputClaim。 |
| PartnerClaimType | 否 | 指定的策略声明类型映射到的外部合作伙伴声明类型的标识符。 如果未指定 PartnerClaimType 属性，则指定的策略声明类型将映射到同名的合作伙伴声明类型。 当声明类型名称不同于另一方时，请使用此属性。 例如，第一个声明名称为“givenName”，而合作伙伴使用名为“first_name”的声明。 |

### <a name="outputclaims"></a>OutputClaims

OutputClaims 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 预期的输出声明类型。 |

#### <a name="outputclaim"></a>OutputClaim 

OutputClaim 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在策略文件或父策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |
| DefaultValue | 否 | 当 ClaimTypeReferenceId 指示的声明不存在时用来创建声明的默认值。技术配置文件可将生成的声明用作 InputClaim。 |
|AlwaysUseDefaultValue |否 |强制使用默认值。  |
| PartnerClaimType | 否 | 指定的策略声明类型映射到的外部合作伙伴声明类型的标识符。 如果未指定 PartnerClaimType 属性，则指定的策略声明类型将映射到同名的合作伙伴声明类型。 当声明类型名称不同于另一方时，请使用此属性。 例如，第一个声明名称为“givenName”，而合作伙伴使用名为“first_name”的声明。 |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

**OutputClaimsTransformations** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 在将任何声明发送到声明提供程序或信赖方之前应执行的声明转换的标识符。 声明转换可用于修改现有的或生成新的 ClaimsSchema 声明。 |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的声明转换的标识符。 |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 用于验证引用技术配置文件的部分或所有输出声明的技术配置文件的标识符。 被引用技术配置文件的所有输入声明必须出现在引用技术配置文件的输出声明中。 |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的技术配置文件的标识符。 |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** 包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimType | 是 | 已在策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

**IncludeTechnicalProfile** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的技术配置文件的标识符。 |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

**UseTechnicalProfileForSessionManagement** 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的技术配置文件的标识符。 |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
用户旅程中的 **ClaimsProviderSelections** 定义声明提供程序选项的列表及其顺序。 使用 **EnabledForUserJourneys** 元素可以筛选用户可用的声明提供程序。 **EnabledForUserJourneys** 元素包含以下值之一：

- **Always**：执行技术配置文件。
- **Never**：跳过技术配置文件。 
- **OnClaimsExistence**：仅当技术配置文件中指定的特定声明存在时才执行技术配置文件。 
- **OnItemExistenceInStringCollectionClaim**：仅当字符串集合声明中存在某个项时才执行技术配置文件。 
- **OnItemAbsenceInStringCollectionClaim**：仅当字符串集合声明中不存在某个项时才执行技术配置文件。

如果使用 OnClaimsExistence、OnItemExistenceInStringCollectionClaim 或 OnItemAbsenceInStringCollectionClaim，要求提供以下元数据：“ClaimTypeOnWhichToEnable”指定要评估的声明类型，“ClaimValueOnWhichToEnable”指定要比较的值。

仅当 **identityProviders** 字符串集合包含 `facebook.com` 值时，才执行以下技术配置文件：

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
        <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
        <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>        
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>  
```












