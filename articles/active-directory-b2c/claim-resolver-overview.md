---
title: 关于 Azure Active Directory B2C 自定义策略中的声明解析程序 | Microsoft Docs
description: 了解声明解析程序如何用于 Azure Active Directory B2C 中的自定义策略。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a13d4b0b44c51f78a068b1619fe083a08756af6b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511606"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>关于 Azure Active Directory B2C 自定义策略中的声明解析程序

Azure Active Directory (Azure AD) B2C [自定义策略](active-directory-b2c-overview-custom.md)中的声明解析程序提供关于授权请求的上下文信息，例如策略名称、请求相关 ID、用户界面语言等。

若要在输入或输出声明中使用声明解析程序，请在 [ClaimsSchema](claimsschema.md) 元素下定义字符串 ClaimType，然后将 DefaultValue 设置为输入或输出声明元素中的声明解析程序   。 Azure AD B2C 读取声明解决程序的值并将该值用于技术配置文件中。 

在以下示例中，使用 `string` 数据类型定义名为 `correlationId` 的声明类型  。  

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

在技术配置文件中，将声明解析程序映射到声明类型。 Azure AD B2C 将声明解析程序的值 `{Context:CorrelationId}` 填充到声明 `correlationId` 中，并向技术配置文件发送声明。

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>声明解析程序类型

以下部分列出了可用的声明解析程序。

### <a name="culture"></a>环境

| 声明 | 描述 | 示例 |
| ----- | ----------- | --------|
| {Culture:LanguageName} | 语言的两字母 ISO 代码。 | en |
| {Culture:LCID}   | 语言代码的 LCID。 | 1033 |
| {Culture:RegionName} | 区域的两字母 ISO 代码。 | 美国 |
| {Culture:RFC5646} | RFC5646 语言代码。 | en-US |

### <a name="policy"></a>策略

| 声明 | 描述 | 示例 |
| ----- | ----------- | --------|
| {Policy:PolicyId} | 信赖方策略名称。 | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | 信赖方策略的租户 ID。 | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | 信赖方策略的租户对象 ID。 | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | 信任框架的租户 ID。 | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| 声明 | 描述 | 示例 |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |`acr_values` 查询字符串参数。 | 不适用 |
| {OIDC:ClientId} |`client_id` 查询字符串参数。 | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |`domain_hint` 查询字符串参数。 | facebook.com |
| {OIDC:LoginHint} |  `login_hint` 查询字符串参数。 | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`。 | 不适用 |
| {OIDC:Nonce} |`Nonce` 查询字符串参数。 | defaultNonce |
| {OIDC:Prompt} | `prompt` 查询字符串参数。 | 登录 |
| {OIDC:Resource} |`resource` 查询字符串参数。 | 不适用 |
| {OIDC:scope} |`scope` 查询字符串参数。 | openid |

### <a name="context"></a>上下文

| 声明 | 描述 | 示例 |
| ----- | ----------- | --------|
| {Context:BuildNumber} | 标识体验框架版本（内部版本号）。  | 1.0.507.0 |
| {Context:CorrelationId} | 相关 ID。  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |UTC 格式的日期时间。  | 2018/10/10 中午 12:00 |
| {Context:DeploymentMode} |策略部署模式。  | 生产 |
| {Context:IPAddress} | 用户 IP 地址。 | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>非协议参数

可以将 OIDC 或 OAuth2 请求中包括的任何参数名称映射到用户旅程中的某个声明， 例如，来自应用程序的请求可能包括名为 `app_session`、`loyalty_number` 的查询字符串参数或任何自定义查询字符串。

| 声明 | 描述 | 示例 |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | 查询字符串参数。 | hawaii |
| {OAUTH-KV:app_session} | 查询字符串参数。 | A3C5R |
| {OAUTH-KV:loyalty_number} | 查询字符串参数。 | 1234 |
| {OAUTH-KV:any custom query string} | 查询字符串参数。 | 不适用 |

### <a name="oauth2"></a>OAuth2

| 声明 | 描述 | 示例 |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | 访问令牌。 | 不适用 |

## <a name="how-to-use-claim-resolvers"></a>如何使用声明解析程序

### <a name="restful-technical-profile"></a>RESTful 技术配置文件

在 [RESTful](restful-technical-profile.md) 技术配置文件中，可能想要发送用户语言、策略名称、作用域和客户端 ID。 根据这些声明，REST API 可以运行自定义业务逻辑，并提出已本地化的错误消息（如有必要）。 

以下示例演示了一个 RESTful 技术配置文件：

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>直接登录

使用声明解析程序时，可以预填充登录名或直接登录到特定的社交标识提供者，例如 Facebook、领英或 Microsoft 帐户。 有关详细信息，请参阅[使用 Azure Active Directory B2C 设置直接登录](direct-signin.md)。

### <a name="dynamic-ui-customization"></a>动态 UI 自定义

通过 Azue AD B2C，可将查询字符串参数传递给 HTML 内容定义终结点，以便可以动态呈现页面内容。 例如，可以基于从 Web 或移动应用程序传递的自定义参数，更改 Azure AD B2C 注册或登录页面上的背景图像。 有关详细信息，请参阅[使用 Azure Active Directory B2C 中的自定义策略动态配置 UI](active-directory-b2c-ui-customization-custom-dynamic.md)。 此外，还可以根据语言参数本地化 HTML 页，或者根据客户端 ID 更改内容。

以下示例将名为 campaignId，且值为 `hawaii`，语言代码为 `en-US`，以及表示客户端 ID 的 app 的参数传入查询字符串    ：

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

结果，Azure AD B2C 将上述参数发送到 HTML 内容页：

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Application Insights 技术配置文件

使用 Azure Application Insights 和声明解析程序，可以了解用户行为。 在 Application Insights 技术配置文件中，将向 Azure Application Insights 发送保留的输入声明。 有关详细信息，请参阅[使用 Application Insights 在 Azure AD B2C 过程中跟踪用户行为](active-directory-b2c-custom-guide-eventlogger-appins.md)。 下面的示例将向 Azure Application Insights 发送策略 ID、相关 ID、语言 ID 和客户端 ID。

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```
