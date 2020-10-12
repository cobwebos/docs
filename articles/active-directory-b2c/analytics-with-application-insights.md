---
title: 跟踪 Application Insights 的用户行为
titleSuffix: Azure AD B2C
description: 了解如何使用自定义策略从 Azure AD B2C 用户旅程中启用 Application Insights 的事件日志。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385971"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>使用 Application Insights 在 Azure Active Directory B2C 中跟踪用户行为

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) 支持使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到 [Application Insights](../azure-monitor/app/app-insights-overview.md)。  使用 Application Insights 技术配置文件，你可以获取用户旅程的详细自定义事件日志，以便执行以下操作：

* 洞察用户行为。
* 排查自己在开发或生产过程中的策略问题。
* 衡量性能。
* 通过 Application Insights 创建通知。

## <a name="how-it-works"></a>工作原理

[Application Insights](application-insights-technical-profile.md)技术配置文件从 Azure AD B2C 中定义事件。 此配置文件可指定事件的名称、将要记录的声明以及检测密钥。 若要发布事件，请将技术配置文件添加为 [用户旅程](userjourneys.md)中的业务流程步骤。

Application Insights 可以使用关联 ID 来记录用户会话，以便统一事件。 Application Insights 可以在数秒内提供事件和会话，并提供许多可视化工具、导出工具和分析工具。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

将 Azure AD B2C 与 Application Insights 配合使用时，只需创建资源并获取检测密钥。 有关信息，请参阅 [创建 Application Insights 资源](../azure-monitor/app/create-new-resource.md)

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在顶部菜单中选择 " **目录 + 订阅** " 筛选器，然后选择包含你的订阅的目录，确保你正在使用包含你的 Azure 订阅的目录。 此租户不是 Azure AD B2C 租户。
3. 选择 Azure 门户左上角的“创建资源”，然后搜索并选择“Application Insights”********。
4. 单击“创建”。
5. 输入此资源的名称****。
6. 在“应用程序类型”下，选择“ASP.NET web 应用程序”********。
7. 对于资源组，选择现有的组，或输入新组的名称****。
8. 单击“创建”。
4. 创建 Application Insights 资源后，将其打开，展开“Essentials”并复制检测密钥****。

![Application Insights 概览和检测密钥](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>定义声明

声明在 Azure AD B2C 策略执行过程中提供数据的临时存储。 [声明架构](claimsschema.md)是发出声明的位置。

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到 ClaimsSchema 元素。 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>新增技术配置文件

可以将技术配置文件视为 Azure AD B2C 的标识体验框架中的函数。 此表定义技术配置文件，这些文件用于打开会话和发布事件。

| 技术配置文件 | 任务 |
| ----------------- | -----|
| AppInsights-Common | 要包括在所有 Azure Insights 技术配置文件中的一组通用参数。 |
| AppInsights-SignInRequest | `SignInRequest`收到登录请求时，记录包含一组声明的事件。 |
| AppInsights-UserSignUp | `UserSignUp`当用户在注册/登录旅程中触发注册选项时记录事件。 |
| AppInsights-SignInComplete | 将 `SignInComplete` 令牌发送到信赖方应用程序时，记录身份验证成功完成后的事件。 |

将配置文件添加到初学者包中的 TrustFrameworkExtensions.xml 文件**。 将以下元素添加到 ClaimsProviders 元素****：

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> 将 `AppInsights-Common` 技术配置文件中的检测密钥更改为 Application Insights 资源提供的 GUID。

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>添加技术配置文件，作为业务流程步骤

调用 `AppInsights-SignInRequest`（作为业务流程步骤 2），对是否已收到登录/注册请求进行跟踪：

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

添加调用 `AppInsights-UserSignup` 的新步骤后，再立即执行**`SendClaims` 业务流程步骤。 当用户在注册/登录旅程中选择注册按钮时，会触发此步骤。

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

在 `SendClaims` 业务流程步骤后，立即调用 `AppInsights-SignInComplete`。 此步骤演示成功完成的过程。

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 添加新的业务流程步骤以后，请按顺序对这些步骤重新编号，不要跳过从 1 到 N 的任何整数。


## <a name="upload-your-file-run-the-policy-and-view-events"></a>上传文件，运行策略，并查看事件

保存并上传 TrustFrameworkExtensions.xml 文件**。 然后，通过应用程序调用信赖方策略，或者在 Azure 门户中使用“立即运行”。**** 数秒后，事件就会出现在 Application Insights 中。

1. 打开 Azure Active Directory 租户中的 **Application Insights** 资源。
2. 选择 "**使用情况**  >  **事件**"。
3. 将“期间”设置为“过去一小时”，将“截止时间”设置为“3 分钟”。****************  可能需要选择“刷新”才能查看结果。****

![Application Insights USAGE-Events 边栏选项卡](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>可有可无收集更多数据

根据需要向用户旅程添加声明类型和事件。 您可以使用 [声明解析](claim-resolver-overview.md) 程序或任何字符串声明类型，通过将 **输入声明** 元素添加到 Application Insights 事件或 AppInsights-Common 技术配置文件来添加声明。

- ClaimTypeReferenceId 是对声明类型的引用****。
- PartnerClaimType 是 Azure Insights 中显示的属性的名称****。 使用语法 `{property:NAME}`，其中 `NAME` 是要添加到该事件的属性。
- DefaultValue 可使用任何字符串值或声明解析程序****。

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>后续步骤

- 在 IEF 参考中详细了解 [Application Insights](application-insights-technical-profile.md) 技术配置文件。 
