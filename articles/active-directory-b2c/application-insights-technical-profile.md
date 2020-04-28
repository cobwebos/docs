---
title: 在自定义策略中定义 Application Insights 技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中的自定义策略中定义 Application Insights 技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80108570"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中定义 Application Insights 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）支持通过使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到[Application Insights](../azure-monitor/app/app-insights-overview.md) 。  使用 Application Insights 技术配置文件，你可以获取用户旅程的详细的自定义事件日志，以便执行以下操作：

* 洞察用户行为。
* 排查自己在开发或生产过程中的策略问题。
* 衡量性能。
* 通过 Application Insights 创建通知。


## <a name="protocol"></a>协议

**协议**元素的`Proprietary` **Name**属性需要设置为。 **处理程序**特性必须包含用于 Application Insights Azure AD B2C 使用的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

以下示例显示了通用 Application Insights 技术配置文件。 其他 Application Insights 技术配置文件包括使用其配置的 AzureInsights。  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>输入声明

**InputClaims**元素包含要发送到 Application Insights 的声明的列表。 你还可以将声明名称映射到你希望在 Application Insights 中显示的名称。 下面的示例演示如何将 telemetries 发送到 Application Insights。 事件的属性是通过语法`{property:NAME}`添加的，其中 NAME 是要添加到事件中的属性。 DefaultValue 可以是一个静态值，也可以是由某个受支持的[声明解析](claim-resolver-overview.md)程序解析的值。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations**元素可包含**InputClaimsTransformation**元素的集合，这些元素用于修改输入声明，或在发送到 Application Insights 之前生成新的输入声明。

## <a name="persist-claims"></a>保存声明

不使用 PersistedClaims 元素。

## <a name="output-claims"></a>输出声明

不使用 OutputClaims 和 OutputClaimsTransformations 元素。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”元素。


## <a name="metadata"></a>元数据

| 特性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| InstrumentationKey| 是 | Application Insights[检测密钥](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)，将用于记录事件。 | 
| DeveloperMode| 否 | 一个布尔值，指示是否启用开发人员模式。 可能的值`true` ： `false`或（默认值）。 此元数据控制如何缓冲事件。 在事件量最少的开发环境中，启用开发人员模式将导致事件立即发送到 Application Insights。|  
|DisableTelemetry |否 |指示是否应启用遥测的布尔值。 可能的值`true` ： `false`或（默认值）。| 


## <a name="next-steps"></a>后续步骤

- [创建 Application Insights 资源](../azure-monitor/app/create-new-resource.md)
- 了解如何[使用 Application Insights 在 Azure Active Directory B2C 中跟踪用户行为](analytics-with-application-insights.md)
