---
title: RelyingParty - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 RelyingParty 元素。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 502b4cef4fc44abcc55c1733b86fb6052e3e43ab
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2020
ms.locfileid: "77372744"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**RelyingParty**元素指定要将当前请求强制用于 Azure Active Directory B2C （Azure AD B2C）的用户旅程。 它还指定依赖方 (RP) 应用程序需要作为已颁发令牌一部分的声明列表。 RP 应用程序（例如 Web、移动或桌面应用程序）调用 RP 策略文件。 RP 策略文件执行特定任务，例如登录、重置密码，或编辑配置文件。 多个应用程序可以使用相同的 RP 策略，单个应用程序可以使用多个策略。 所有 RP 应用程序都接收具有相同声明的令牌，用户会经历相同的用户旅程。

下面的示例演示 B2C_1A_signup_signin 策略文件中的 RelyingParty 元素：

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

可选 RelyingParty 元素包含下列元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | 信赖方应用的默认用户旅程。 |
| UserJourneyBehaviors | 0:1 | 用户旅程行为的范围。 |
| TechnicalProfile | 1:1 | 信赖方应用支持的技术配置文件。 该技术配置文件提供了信赖方应用与 Azure AD B2C 联系的协定。 |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` 元素指定对通常在基本或扩展策略中定义的用户旅程标识符的引用。 下面的示例演示在 RelyingParty 元素中指定的注册或登录用户旅程：

B2C_1A_signup_signin 策略：

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase 或 B2C_1A_TrustFrameworkExtensionPolicy：

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

DefaultUserJourney 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 策略中用户旅程的标识符。 有关详细信息，请参阅[用户旅程](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

UserJourneyBehaviors 元素包含下列元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | 用户旅程单一登录 (SSO) 会话行为的范围。 |
| SessionExpiryType |0:1 | 会话的身份验证行为。 可能的值：`Rolling` 或 `Absolute`。 `Rolling` 值（默认值）表示用户保持登录状态，只要用户在应用程序中保持持续活动状态。 `Absolute` 值指示在应用程序会话生命周期指定的时间段后将强制用户重新进行身份验证。 |
| SessionExpiryInSeconds | 0:1 | 身份验证成功后，存储在用户浏览器上指定为整数的 Azure AD B2C 会话 Cookie 的生存期。 |
| JourneyInsights | 0:1 | 要使用的 Azure Application Insights 检测密钥。 |
| ContentDefinitionParameters | 0:1 | 要追加到内容定义负载 URI 的键值对列表。 |

### <a name="singlesignon"></a>SingleSignOn

SingleSignOn 元素包含在以下属性中：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| 范围 | 是 | 单一登录行为的范围。 可能的值：`Suppressed`、`Tenant`、`Application` 或 `Policy`。 `Suppressed` 值指示已取消行为。 例如，在单一登录会话的情况下，不会为用户维护会话，并且始终提示用户选择标识提供者。 `TrustFramework` 值指示该行为适用于信任框架中的所有策略。 例如，不会提示在两个策略旅程中导航信任框架的用户选择标识提供者。 `Tenant` 值指示该行为适用于租户中的所有策略。 例如，不会提示在两个策略旅程中导航租户的用户选择标识提供者。 `Application` 值指示该行为适用于发出请求的应用程序的所有策略。 例如，不会提示在应用程序的两个策略旅程中导航的用户选择标识提供者。 `Policy` 值指示该行为仅适用于一个策略。 例如，当在策略之间切换时，会提示在两个策略旅程中导航信任框架的用户选择标识提供者。 |
| KeepAliveInDays | 是 | 控制用户保持登录状态的时间长短。 将此值设置为 0 会关闭 KMSI 功能。 有关详细信息，请参阅[使我保持登录状态](custom-policy-keep-me-signed-in.md)。 |
|EnforceIdTokenHintOnLogout| 是|  强制将以前颁发的 ID 令牌作为有关最终用户当前通过身份验证的会话与客户端的提示传递到注销终结点。 可能的值为 `false`（默认）或 `true`。 有关详细信息，请参阅[通过 OpenID connect 进行 Web 登录](openid-connect.md)。  |


## <a name="journeyinsights"></a>JourneyInsights

JourneyInsights 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| TelemetryEngine | 是 | 值必须是 `ApplicationInsights`。 |
| InstrumentationKey | 是 | 一个字符串，其中包含 application insights 元素的检测密钥。 |
| DeveloperMode | 是 | 可能的值：`true` 或 `false`。 如果是 `true`，Application Insights 将加快遥测数据通过处理管道。 此设置非常适合用于开发，但在高容量方面受到限制。详细的活动日志仅用于帮助开发自定义策略。 请勿在生产中使用开发模式。 日志收集在开发过程中发送到标识提供者以及从中发出的所有声明。 如果在生产中使用，则开发人员对他们所拥有的 App Insights 日志中收集的 PII（私人身份信息）负责。 只有当该值设置为 `true`，才会收集这些详细日志。|
| ClientEnabled | 是 | 可能的值：`true` 或 `false`。 如果是 `true`，则发送用于跟踪页面视图和客户端错误的 Application Insights 客户端脚本。 |
| ServerEnabled | 是 | 可能的值：`true` 或 `false`。 如果是 `true`，则将现有 UserJourneyRecorder JSON 作为自定义事件发送到 Application Insights。 |
| TelemetryVersion | 是 | 值必须是 `1.0.0`。 |

有关详细信息，请参阅[收集日志](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

在 Azure AD B2C 中使用自定义策略可在查询字符串中发送参数。 通过将该参数传递到 HTML 终结点，可以动态更改页面内容。 例如，可以基于从 Web 或移动应用程序传递的参数，更改 Azure AD B2C 注册或登录页面上的背景图像。 Azure AD B2C 向动态 HTML 文件传递查询字符串参数，例如 aspx 文件。

下面的示例传递名为 `campaignId` 的参数，查询字符串中的值为 `hawaii`：

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

ContentDefinitionParameters 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | 一个字符串，包含追加到内容定义负载 URI 查询字符串的键值对。 |

ContentDefinitionParameters 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| 名称 | 是 | 键值对的名称。 |

有关详细信息，请参阅[使用自定义策略配置包含动态内容的 UI](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>TechnicalProfile

**TechnicalProfile** 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 值必须是 `PolicyProfile`。 |

**TechnicalProfile** 包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | 包含技术配置文件的名称的字符串。 |
| 说明 | 0:1 | 包含技术配置文件的说明的字符串。 |
| 协议 | 1:1 | 用于联合的协议。 |
| 元数据 | 0:1 | 一个键/值对项集合，由协议在事务过程中与终结点进行通信，以配置依赖方与其他社区参与者之间的交互。 |
| OutputClaims | 1:1 | 作为技术配置文件中的输出的声明类型列表。 这些元素中的每一个都包含对已在 ClaimsSchema 部分或策略文件继承自的策略中定义的 ClaimType。 |
| SubjectNamingInfo | 1:1 | 在令牌中使用的使用者名称。 |

Protocol 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| 名称 | 是 | Azure AD B2C 支持的有效协议的名称，用作技术配置文件的一部分。 可能的值：`OpenIdConnect` 或 `SAML2`。 `OpenIdConnect` 值表示根据 OpenID 基本规范的 OpenID Connect 1.0 协议标准。 `SAML2` 表示根据 OASIS 规范的 SAML 2.0 协议标准。 不要在生产环境中使用 SAML 令牌。 |

## <a name="outputclaims"></a>OutputClaims

OutputClaims 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 信赖方订阅的策略中受支持列表的预期声明类型的名称。 此声明可作为技术配置文件输出。 |

OutputClaim 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 对在策略文件的 ClaimsSchema 部分定义的 ClaimType 的引用。 |
| DefaultValue | 是 | 一个默认值，如果声明值为空，则可以使用该值。 |
| PartnerClaimType | 是 | 按照 ClaimType 定义中配置的不同名称发送声明。 |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

使用 SubjectNameingInfo 元素，可以控制令牌使用者的值：
- **JWT 令牌**-`sub` 声明。 这是令牌针对其断言信息的主体，例如应用程序的用户。 此值是固定不变的，无法重新分配或重复使用。 可使用它安全地执行授权检查，例如，使用令牌访问资源时。 默认情况下，将使用目录中用户的对象 ID 填充使用者声明。 有关详细信息，请参阅[令牌、会话和单一登录配置](session-behavior.md)。
- SAML 令牌 - 标识使用者元素的 `<Subject><NameID>` 元素。

SubjectNamingInfo 元素包含以下属性：

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ClaimType | 是 | 对输出声明的 PartnerClaimType 的引用。 输出声明必须在信赖方策略 OutputClaims 集合中定义。 |

下面的示例演示如何定义 OpenID Connect 信赖方。 使用者名称信息配置为 `objectId`：

```XML
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
JWT 令牌包括带用户 objectId 的 `sub` 声明：

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
