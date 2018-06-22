---
title: 通过 Azure Active Directory B2C 使用 Application Insights 中的事件跟踪用户行为 | Microsoft Docs
description: 有关如何通过 Azure AD B2C 用户旅程使用自定义策略在 Application Insights 中启用事件日志的分步指南（预览版）
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1b37e61763b34e320ffb4078600e08b1d32330a1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709958"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>使用 Application Insights 在 Azure AD B2C 旅程中跟踪用户行为

Azure Active Directory B2C (Azure AD B2C) 可与 Azure Application Insights 很好地配合使用。 它们可以为通过自定义方式创建的用户旅程提供详细的自定义事件日志。 本文介绍以下操作的入门方式：

* 洞察用户行为。
* 排查自己在开发或生产过程中的策略问题。
* 衡量性能。
* 通过 Application Insights 创建通知。

> [!NOTE]
> 此功能为预览版。

## <a name="how-it-works"></a>工作原理

Azure AD B2C 中的标识体验框架现在包括提供程序 `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`。  它使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到 Application Insights。

某个技术配置文件会使用此提供程序来定义 B2C 提供的事件。  此配置文件可指定事件的名称、将要记录的声明以及检测密钥。  然后会在自定义用户旅程中将此技术配置文件作为 `orchestration step` 或 `validation technical profile` 添加，以便发布事件。

Application Insights 可以使用关联 ID 来记录用户会话，以便统一事件。 Application Insights 可以在数秒内提供事件和会话，并提供许多可视化工具、导出工具和分析工具。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。 本文假设使用的是自定义策略初学者包。 但是，初学者包不是必需的。

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>步骤 1。 创建 Application Insights 资源并获取检测密钥

将 Application Insights 与 Azure AD B2C 配合使用时，唯一的要求是创建资源并获取检测密钥。 在 [Azure 门户](https://portal.azure.com)中创建一个资源。

1. 在 Azure 门户的订阅租户中选择“+ 创建资源”。 此租户不是 Azure AD B2C 租户。  
2. 搜索并选择“Application Insights”。  
3. 在首选订阅下，创建使用“ASP.NET Web 应用程序”作为“应用程序类型”的资源。
4. 创建 Application Insights 资源后，将其打开并记下检测密钥。

![Application Insights 概览和检测密钥](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>步骤 2. 将新的 ClaimType 定义添加到信任框架扩展文件

打开初学者包中的扩展文件，向 `<BuildingBlocks>` 节点添加以下元素。 文件名通常为 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`。

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>步骤 3. 添加使用 Application Insights 提供程序的新技术配置文件

可以将技术配置文件视为 Azure AD B2C 的标识体验框架中的函数。 此示例定义五个技术配置文件，以打开会话和发布事件：

| 技术配置文件 | 任务 |
| ----------------- | -----|
| AzureInsights-Common | 创建要在所有 Azure-Insights 技术配置文件中包括的通用参数集 | 
| JourneyContextForInsights | 在 Application Insights 中打开会话并发送关联 ID |
| AzureInsights-SignInRequest | 在收到登录请求后创建包含一组声明的 `SignIn` 事件 | 
| AzureInsights-UserSignup | 当用户在注册/登录旅程中触发注册选项时创建 UserSignup 事件 | 
| AzureInsights-SignInComplete | 在将令牌发送到信赖方应用程序后记录成功完成了身份验证 | 

将配置文件添加到初学者包中的扩展文件的方法是将这些元素添加到 `<ClaimsProviders>` 节点。  文件名通常为 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`。

> [!IMPORTANT]
> 将 `ApplicationInsights-Common` 技术配置文件中的检测密钥更改为 Application Insights 资源提供的 GUID。

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>步骤 4. 为 Application Insights 添加技术配置文件，作为现有用户旅程中的业务流程步骤

调用 `JournyeContextForInsights`（作为业务流程步骤 1）：

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

调用 `Azure-Insights-SignInRequest`（作为业务流程步骤 2），对是否已收到登录/注册请求进行跟踪：

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

添加调用 `Azure-Insights-UserSignup` 的新步骤后，再立即执行 `SendClaims` 业务流程步骤。 当用户在注册/登录旅程中选择注册按钮时，会触发此步骤。

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

在 `SendClaims` 业务流程步骤后，立即调用 `Azure-Insights-SignInComplete`。 此步骤反映成功完成的旅程。

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 添加新的业务流程步骤以后，请按顺序对这些步骤重新编号，不要跳过从 1 到 N 的任何整数。


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>步骤 5。 在 Application Insights 中上传修改的扩展文件、运行策略并查看事件

保存并上传新的信任框架扩展文件。 然后，通过应用程序调用信赖方策略，或者在 Azure AD B2C 界面中使用 `Run Now`。 数秒后，事件就会出现在 Application Insights 中。

1. 在 Azure Active Directory 租户中打开 **Application Insights** 资源。
2. 选择“使用情况” > “事件”。
3. 将“期间”设置为“过去一小时”，将“截止时间”设置为“3 分钟”。  可能需要选择“刷新”才能查看结果。

![Application Insights USAGE-Events 边栏选项卡](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>后续步骤

根据需要向用户旅程添加声明类型和事件。 下面是一个列表，其中包含可能使用其他声明解析程序的声明

### <a name="culture-specific-claims"></a>特定于区域性的声明

```xml
Referenced using: {Culture:One of the property names below}
```

| 声明 | 定义 | 示例 |
| ----- | -----------| --------|
| LanguageName | 语言的两字母 ISO 代码 | en |
| RegionName | 区域的两字母 ISO 代码 | 美国 |
| RFC5646 | RFC5646 语言代码 | en-US |
| LCID   | 语言代码的 LCID | 1033 |

### <a name="policy-specific-claims"></a>特定于策略的声明

```xml
Referenced using {Policy:One of the property names below}
```

| 声明 | 定义 | 示例 |
| ----- | -----------| --------|
| TrustFrameworkTenantId | trustframework 租户 ID | 不适用 |
| RelyingPartyTenantId | 信赖方的租户 ID | 不适用 |
| PolicyId | 策略的策略 ID | 不适用 |
| TenantObjectId | 策略的租户对象 ID | 不适用 |

### <a name="openid-connect-specific-claims"></a>OpenID Connect 特定的声明

```xml
Referenced using {OIDC:One of the property names below}
```

| 声明 | OpenIdConnect 参数 | 示例 |
| ----- | ----------------------- | --------|
| Prompt | prompt | 不适用 |
| LoginHint |  login_hint | 不适用 |
| DomainHint | domain_hint | 不适用 |
|  MaxAge | max_age | 不适用 |
| ClientId | client_id | 不适用 |
| 用户名 | login_hint | 不适用 |
| 密码 | domain_hint | 不适用 |
|  资源 | resource| 不适用 |
| AuthenticationContextReferences | acr_values | 不适用 |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>OIDC 和 OAuth2 请求中包括的非协议参数

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

可以将 OIDC 或 OAuth2 请求中包括的任何参数名称映射到用户旅程中的某个声明， 然后可将其记录到事件中。 例如，来自应用程序的请求可能包括名为 `app_session`、`loyalty_number` 或 `any_string` 的查询字符串参数。

下面是来自应用程序的示例请求：

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
然后，可将 `Input Claim` 元素添加到 Application Insights 事件，以添加声明：

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>其他系统声明

必须先将某些系统声明添加到声明包中，然后才能将其记录为事件。 必须先以业务流程步骤或验证技术配置文件方式调用技术配置文件 `SimpleUJContext`，然后才能使用这些声明。

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```


