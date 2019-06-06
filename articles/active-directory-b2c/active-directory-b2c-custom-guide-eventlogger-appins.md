---
title: 通过 Azure Active Directory B2C 使用 Application Insights 中的事件跟踪用户行为 | Microsoft Docs
description: 了解如何通过 Azure AD B2C 用户旅程使用自定义策略在 Application Insights 中启用事件日志（预览版）。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0c2f9a2a3d431e2948c7d50541b576b23c3ece6a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507542"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>使用 Application Insights 在 Azure Active Directory B2C 中跟踪用户行为

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

将 Azure Application Insights 与 Azure Active Directory (Azure AD) B2C 配合使用时，可以获得用户情况的详细自定义事件日志。 在本文中，学习如何：

* 洞察用户行为。
* 排查自己在开发或生产过程中的策略问题。
* 衡量性能。
* 通过 Application Insights 创建通知。

## <a name="how-it-works"></a>工作原理

Azure AD B2C 中的标识体验框架包括提供程序 `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`。 它使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到 Application Insights。

某个技术配置文件会使用此提供程序来定义 Azure AD B2C 提供的事件。 此配置文件可指定事件的名称、将要记录的声明以及检测密钥。 然后会在自定义用户旅程中将此技术配置文件作为 `orchestration step` 或 `validation technical profile` 添加，以便发布事件。

Application Insights 可以使用关联 ID 来记录用户会话，以便统一事件。 Application Insights 可以在数秒内提供事件和会话，并提供许多可视化工具、导出工具和分析工具。

## <a name="prerequisites"></a>必备组件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。 本文假设使用的是自定义策略初学者包。 但是，初学者包不是必需的。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

将 Azure AD B2C 与 Application Insights 配合使用时，只需创建资源并获取检测密钥。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure 订阅的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含订阅的目录  。 此租户不是 Azure AD B2C 租户。
3. 选择 Azure 门户左上角的“创建资源”，然后搜索并选择“Application Insights”   。
4. 单击**创建**。
5. 输入此资源的名称  。
6. 在“应用程序类型”下，选择“ASP.NET web 应用程序”   。
7. 对于资源组，选择现有的组，或输入新组的名称  。
8. 单击**创建**。
4. 创建 Application Insights 资源后，将其打开，展开“Essentials”并复制检测密钥  。

![Application Insights 概览和检测密钥](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>添加新的 ClaimType 定义

从初学者包中打开 TrustFrameworkExtensions.xml 文件，然后向 [BuildingBlocks](buildingblocks.md) 元素添加以下元素  ：

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

## <a name="add-new-technical-profiles"></a>新增技术配置文件

可以将技术配置文件视为 Azure AD B2C 的标识体验框架中的函数。 此表定义技术配置文件，这些文件用于打开会话和发布事件。

| 技术配置文件 | 任务 |
| ----------------- | -----|
| AzureInsights-Common | 创建要在所有 Azure-Insights 技术配置文件中包括的通用参数集。 | 
| AzureInsights-SignInRequest | 在收到登录请求后创建包含一组声明的 SignIn 事件。 | 
| AzureInsights-UserSignup | 当用户在注册/登录旅程中触发注册选项时创建 UserSignup 事件。 | 
| AzureInsights-SignInComplete | 在将令牌发送到信赖方应用程序后记录成功完成了身份验证。 | 

将配置文件添加到初学者包中的 TrustFrameworkExtensions.xml 文件  。 将以下元素添加到 ClaimsProviders 元素  ：

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
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

> [!IMPORTANT]
> 将 `ApplicationInsights-Common` 技术配置文件中的检测密钥更改为 Application Insights 资源提供的 GUID。

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>添加技术配置文件，作为业务流程步骤

调用 `Azure-Insights-SignInRequest`（作为业务流程步骤 2），对是否已收到登录/注册请求进行跟踪：

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

添加调用 `Azure-Insights-UserSignup` 的新步骤后，再立即执行  `SendClaims` 业务流程步骤。 当用户在注册/登录旅程中选择注册按钮时，会触发此步骤。

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

在 `SendClaims` 业务流程步骤后，立即调用 `Azure-Insights-SignInComplete`。 此步骤演示成功完成的过程。

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 添加新的业务流程步骤以后，请按顺序对这些步骤重新编号，不要跳过从 1 到 N 的任何整数。


## <a name="upload-your-file-run-the-policy-and-view-events"></a>上传文件，运行策略，并查看事件

保存并上传 TrustFrameworkExtensions.xml 文件  。 然后，通过应用程序调用信赖方策略，或者在 Azure 门户中使用“立即运行”。  数秒后，事件就会出现在 Application Insights 中。

1. 在 Azure Active Directory 租户中打开 **Application Insights** 资源。
2. 选择“使用情况” > “事件”。  
3. 将“期间”设置为“过去一小时”，将“截止时间”设置为“3 分钟”。      可能需要选择“刷新”才能查看结果。 

![Application Insights USAGE-Events 边栏选项卡](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>后续步骤

根据需要向用户旅程添加声明类型和事件。 可以使用[声明解析程序](claim-resolver-overview.md)或任何字符串声明类型，通过向 Application Insights 事件或 AzureInsights-Common 技术配置文件添加 Input Claim 元素来添加声明  。 

- ClaimTypeReferenceId 是对声明类型的引用  。
- PartnerClaimType 是 Azure Insights 中显示的属性的名称  。 使用语法 `{property:NAME}`，其中 `NAME` 是要添加到该事件的属性。 
- DefaultValue 可使用任何字符串值或声明解析程序  。 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

