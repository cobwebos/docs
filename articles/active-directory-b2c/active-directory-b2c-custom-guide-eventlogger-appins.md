---
title: 如何通过 Azure AD B2C 使用 Application Insights 中的事件跟踪用户行为 | Microsoft Docs
description: 有关如何通过 Azure AD B2C 用户旅程使用自定义策略在 Application Insights 中启用事件日志的分步指南 - 预览
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>使用 Application Insights 在 Azure AD B2C 旅程中跟踪用户行为

Azure Active Directory B2C 可以与 Azure Application Insights 很好地配合使用。  它们可以为通过自定义方式创建的用户旅程提供详细的自定义事件日志。  本指南介绍以下操作的入门方式： 
* 了解用户行为
* 排查自己在开发或生产过程中的策略问题
* 衡量性能
* 通过 Application Insights 创建通知

## <a name="how-it-works"></a>工作原理
已向 Azure AD B2C 的标识体验框架添加新的提供程序：`Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`。  它会使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到 Application Insights。  某个技术配置文件会使用此提供程序来定义 B2C 提供的事件。  此配置文件可指定事件的名称、将要记录的声明以及检测密钥。  然后会在自定义用户旅程中将此技术配置文件作为 `orchestration step` 或 `validation technical profile` 添加，以便发布事件。  可以使用相关 ID 来记录用户会话，以便通过 Application Insights 来统一事件。  Application Insights 可以在数秒内提供事件和会话，并提供许多可视化工具、导出工具和分析工具。



## <a name="prerequisites"></a>先决条件
完成[自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。  本指南假定使用的是自定义策略初学者包。  不过，该包不是必需的。



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>步骤 1. 创建 Application Insights 资源并获取检测密钥

Application Insights 是一项强大的工具。 将它与 Azure AD B2C 配合使用时，唯一的要求是创建资源并获取检测密钥。  Application Insights 必须在 [Azure 门户](https://portal.azure.com)中创建。

[Application Insights 的完整文档](https://docs.microsoft.com/azure/application-insights/)

1. 在 Azure 门户的订阅租户中单击`+ Create a resource`。  此租户不是 Azure AD B2C 租户。  
2. 搜索并选择 `Application Insights`。  
3. 在首选订阅下创建 `Application Type` `ASP.NET web application` 资源。
4. 创建 Application Insights 资源后，将其打开并记下 `Instrumentation Key` 

![Application Insights 概览和检测密钥](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>步骤 2. 将新的 ClaimType 定义添加到信任框架扩展文件

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>打开初学者包中的扩展文件，向 `<BuildingBlocks>` 节点添加以下元素。  扩展文件名通常为 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

可以将技术配置文件视为 Azure AD B2C 的标识体验框架中的函数。  在此示例中定义了五个技术配置文件，用于打开会话和发布事件。

| 技术配置文件       | 任务 |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | 常用参数集，可以包含在所有 Azure-Insights 技术配置文件中     | 
| JourneyContextForInsights   | 在 App Insights 中打开会话并发送相关 ID |
| AzureInsights-SignInRequest     | 在收到登录请求后创建包含一组声明的“SignIn”事件      | 
| AzureInsights-UserSignup | 在注册/登录旅程中的用户触发注册选项后创建名为“UserSignup”的事件     | 
| AzureInsights-SignInComplete | 在将令牌发送到信赖方应用程序后记录成功完成了身份验证     | 

将配置文件添加到初学者包中的扩展文件的方法是将这些元素添加到 `<ClaimsProviders>` 节点。  扩展文件名通常为 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> 请将 `ApplicationInsights-Common` 技术配置文件中的 `Instrumentation Key` 更改为 Application Insights 资源提供的 GUID。

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
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
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
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>步骤 4. 为 Application-Insights 添加技术配置文件（充当现有用户旅程中的业务流程步骤）。

调用 `JournyeContextForInsights`（充当业务流程步骤 1）

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

调用 `Azure-Insights-SignInRequest`（充当业务流程步骤 2），对是否已收到登录/注册请求进行跟踪。

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

添加调用 `Azure-Insights-UserSignup` 的新步骤后，再立即执行 `SendClaims` 业务流程步骤。 该新步骤是用户在注册/登录旅程中单击注册按钮后触发的。

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

在 `SendClaims` 业务流程步骤后，立即调用 `Azure-Insights-SignInComplete`。   此步骤会反映成功完成的旅程。

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> 添加新的业务流程步骤以后，请按顺序对这些步骤重新编号，不要跳过任何整数（1 到 N）


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>步骤 5。 在 Application Insights 中上传修改的扩展文件、运行策略并查看事件

保存并上传新的信任框架扩展文件。  然后，通过应用程序调用信赖方策略，或者在 Azure AD B2C 界面中使用`Run Now`。  数秒后事件就会在 Application Insights 中出现。

1. 在 Azure Active Directory 租户中打开 Application Insights 资源。
2. 在`USAGE`子菜单中单击`Events`。
3. 将`During`设置为`Last hour`，将`By`设置为`3 minutes`。  可能需要单击`Refresh`才能查看结果

![Application Insights USAGE-Events 边栏选项卡](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>后续步骤

根据需要向用户旅程添加其他声明类型和事件。  下面是一个列表，其中包含可能使用其他声明解析程序的声明。

### <a name="culture-specific-claims"></a>特定于区域性的声明

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>特定于策略的声明

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>特定于 OpenIDConnect 的声明

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>OIDC 和 OAuth2 请求中包括的非协议参数

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

可以将 OIDC 或 OAuth2 请求中包括的任何参数名称映射到用户旅程中的某个声明，  然后将其记录到事件中。 例如，来自应用程序的请求可能包括名为 `app_session`、`loyalty_number` 或 `any_string` 的查询字符串参数。

来自应用程序的示例请求：
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
然后就可以添加这些声明，方法是使用以下代码将 Input Claim 元素添加到 Application Insights 事件：
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



