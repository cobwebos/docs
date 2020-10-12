---
title: 在自定义策略中定义 Application Insights 技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 Application Insights 技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201406"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中定义 Application Insights 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 支持使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到 [Application Insights](../azure-monitor/app/app-insights-overview.md)。  使用 Application Insights 技术配置文件，你可以获取用户旅程的详细自定义事件日志，以便执行以下操作：

* 洞察用户行为。
* 排查自己在开发或生产过程中的策略问题。
* 衡量性能。
* 通过 Application Insights 创建通知。


## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 **handler** 属性必须包含可供 Azure AD B2C 用于 Application Insights 的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

以下示例显示了常见的 Application Insights 技术配置文件。 其他 Application Insights 技术配置文件包括 AzureInsights-Common，这是为了利用其配置。  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>输入声明

**InputClaims** 元素包含要发送到 Application Insights 的声明的列表。 你还可以将声明名称映射到你希望在 Application Insights 中显示的名称。 以下示例展示了如何将遥测数据发送到 Application Insights。 通过语法 `{property:NAME}` 添加事件的属性，其中的 NAME 是要添加到事件的属性。 DefaultValue 可以是静态值，也可以是由受支持的[声明解析程序](claim-resolver-overview.md)之一解析的值。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations** 元素可以包含用于在将输入声明发送到 Application Insights 之前修改输入声明或者生成新输入声明的 **InputClaimsTransformation** 元素集合。

## <a name="persist-claims"></a>保存声明

不使用 PersistedClaims 元素。

## <a name="output-claims"></a>输出声明

不使用 OutputClaims 和 OutputClaimsTransformations 元素。

## <a name="cryptographic-keys"></a>加密密钥

不使用 CryptographicKeys 元素。


## <a name="metadata"></a>Metadata

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| InstrumentationKey| 是 | Application Insights [检测密钥](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)，将用于记录事件。 | 
| DeveloperMode| 否 | 一个布尔值，指示是否启用开发人员模式。 可能的值：`true` 或 `false`（默认值）。 此元数据控制如何缓冲事件。 在事件量最少的开发环境中，启用开发人员模式会导致系统立即将事件发送到 Application Insights。|  
|DisableTelemetry |否 |一个布尔值，指示是否应启用遥测。 可能的值：`true` 或 `false`（默认值）。| 


## <a name="next-steps"></a>后续步骤

- [创建 Application Insights 资源](../azure-monitor/app/create-new-resource.md)
- 了解如何 [使用 Application Insights 在 Azure Active Directory B2C 中跟踪用户行为](analytics-with-application-insights.md)
