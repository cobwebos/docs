---
title: UserJourneys | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 UserJourneys 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 189343888d2856a6945723c030485e58394c912f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559600"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

用户旅程指定策略允许信赖方应用程序为用户获取所需声明的显式路径。 用户通过这些路径检索要提供给信赖方的声明。 换言之，用户旅程定义最终用户在 Azure AD B2C 标识体验框架处理请求时所经历的业务逻辑。

这些用户旅程可视为一些模板，可用于满足感兴趣社区的各种信赖方的核心需求。 用户旅程有助于定义策略的信赖方部分。 策略可以定义多个用户旅程。 每个用户旅程都是一系列的业务流程步骤。

若要定义策略支持的用户旅程，请在策略文件的顶级元素下添加 UserJourneys  元素。

UserJourneys  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 定义完整用户流所需的所有构造的用户旅程。 |

UserJourney  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用户旅程的标识符，可用于从策略中的其他元素中引用它。 [信赖方策略](relyingparty.md)的 DefaultUserJourney  元素指向此属性。 |

UserJourney  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 成功事务必须遵循的业务流程序列。 每个用户旅程都包含按顺序执行的业务流程步骤的有序列表。 如果任何步骤失败，则事务将失败。 |

## <a name="orchestrationsteps"></a>OrchestrationSteps

用户旅程表示为成功事务必须遵循的业务流程序列。 如果任何步骤失败，则事务将失败。 这些业务流程步骤引用策略文件中允许的构建基块和声明提供程序。 负责显示或呈现用户体验的任何业务流程步骤也具有对相应内容定义标识符的引用。

业务流程步骤可以基于业务流程步骤元素中定义的前提条件有条件地执行。 例如，仅当存在特定声明或声明等于或未达到指定值时，才能检查执行业务流程步骤。

若要指定业务流程步骤的有序列表，请将 OrchestrationSteps  元素作为策略的一部分添加。 此元素是必需的。

OrchestrationSteps  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 一个有序的业务流程步骤。 |

OrchestrationStep  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| `Order` | 是 | 业务流程步骤的顺序。 |
| `Type` | 是 | 业务流程步骤的类型。 可能的值： <ul><li>ClaimsProviderSelection  - 指示业务流程步骤向用户提供各种声明提供程序以选择一个。</li><li>CombinedSignInAndSignUp  - 指示业务流程步骤提供组合的社交提供程序登录和本地帐户注册页面。</li><li>ClaimsExchange  - 指示业务流程步骤与声明提供程序交换声明。</li><li>**GetClaims** -指定业务流程步骤应通过其`InputClaims`配置处理从信赖方发送到 Azure AD B2C 的声明数据。</li><li>SendClaims  - 指示业务流程步骤将声明发送给具有声明颁发者颁发的令牌的信赖方。</li></ul> |
| ContentDefinitionReferenceId | 否 | 与此业务流程步骤相关联的[内容定义](contentdefinitions.md)的标识符。 通常内容定义引用标识符在自断言的技术配置文件中定义。 但是，在某些情况下，Azure AD B2C 需要显示无技术配置文件的某些内容。 有两个示例 - 如果业务流程步骤的类型是以下类型之一：`ClaimsProviderSelection` 或 `CombinedSignInAndSignUp`，Azure AD B2C 需要在没有技术配置文件的情况下显示标识提供者选择。 |
| CpimIssuerTechnicalProfileReferenceId | 否 | 业务流程步骤的类型是 `SendClaims`。 此属性定义为信赖方颁发令牌的声明提供程序的技术配置文件标识符。  如果不存在，则不会创建任何信赖方令牌。 |


OrchestrationStep  元素可以包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | 为执行业务流程步骤而必须满足的前置条件列表。 |
| ClaimsProviderSelections | 0:n | 业务流程步骤的声明提供程序选择列表。 |
| ClaimsExchanges | 0:n | 业务流程步骤的声明交换列表。 |

### <a name="preconditions"></a>Preconditions

Preconditions  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| Precondition | 1:n | 具体取决于正在使用的技术配置文件，根据声明提供程序选择重定向客户端或对交换声明进行服务器调用。 |


#### <a name="precondition"></a>Precondition

Precondition  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| `Type` | 是 | 要对此前置条件执行的检查或查询的类型。 值可以是 ClaimsExist  （指定在用户当前声明集中存在指定声明时应执行操作）或 ClaimEquals  （指定当指定声明存在且其值等于指定值时应执行操作）。 |
| `ExecuteActionsIf` | 是 | 使用 true 或 false 测试确定是否应执行前置条件中的操作。 |

Precondition  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| Value | 1:n | 要查询的 ClaimTypeReferenceId。 另一个值元素包含要检查的值。</li></ul>|
| 操作 | 1:1 | 在业务流程步骤中的前置条件检查为 true 时应执行的操作。 如果 `Action` 的值设置为 `SkipThisOrchestrationStep`，则不应执行相关联的 `OrchestrationStep`。 |

#### <a name="preconditions-examples"></a>Preconditions 示例

以下前置条件检查是否存在用户的 objectId。 在用户旅程中，用户已选择使用本地帐户进行登录。 如果存在 objectId，请跳过此业务流程步骤。

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

以下前置条件检查用户是否使用社交帐户登录。 已尝试在目录中查找用户帐户。 如果用户使用本地帐户登录或注册，请跳过此业务流程步骤。

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Preconditions 可以检查多个前置条件。 以下示例检查是否存在“objectId”或“电子邮件”。 如果第一个条件为 true，旅程将跳到下一个业务流程步骤。

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

类型 `ClaimsProviderSelection` 或 `CombinedSignInAndSignUp` 的业务流程步骤可能包含用户可以登录的声明提供程序列表。 `ClaimsProviderSelections` 元素内的元素顺序控制提供给用户的标识提供程序的顺序。

**ClaimsProviderSelections** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 提供可以选择的声明提供程序的列表。|

**ClaimsProviderSelections** 元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| DisplayOption| 否 | 控制单个声明提供程序选择可用时的行为。 可能的值： `DoNotShowSingleProvider` （默认值），用户将立即重定向到联合标识提供者。 或  `ShowSingleProvider` Azure AD B2C 会显示带有单一个标识提供者选择的登录页。 若要使用此属性，[内容定义版本](page-layout.md)必须为  `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` 及更高版本。|

ClaimsProviderSelection  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | 否 | 声明交换的标识符，在声明提供程序选择的下一个业务流程步骤中执行。 必须指定此属性或 ValidationClaimsExchangeId 属性，但不能同时指定这两个属性。 |
| ValidationClaimsExchangeId | 否 | 声明交换的标识符，在当前业务流程步骤中执行以验证声明提供程序选择。 必须指定此属性或 TargetClaimsExchangeId 属性，但不能同时指定这两个属性。 |

### <a name="claimsproviderselection-example"></a>ClaimsProviderSelection 示例

在下面的业务流程步骤中，用户可以选择使用 Facebook、LinkedIn、Twitter、Google 或本地帐户进行登录。 如果用户选择其中一个社交标识提供者，则第二个业务流程步骤将使用 `TargetClaimsExchangeId` 属性中指定的所选声明交换执行。 第二个业务流程步骤将用户重定向到社交标识提供者以完成登录过程。 如果用户选择使用本地帐户登录，Azure AD B2C 将保持相同的业务流程步骤（相同的注册页面或登录页面），并跳过第二个业务流程步骤。

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

ClaimsExchanges  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 具体取决于正在使用的技术配置文件，根据所选的 ClaimsProviderSelection 重定向客户端，或对交换声明进行服务器调用。 |

ClaimsExchange  元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 声明交换步骤的标识符。 该标识符用于从策略中的声明提供程序选择步骤引用声明交换。 |
| TechnicalProfileReferenceId | 是 | 要执行的技术配置文件的标识符。 |
