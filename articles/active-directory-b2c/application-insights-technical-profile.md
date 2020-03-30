---
title: 在自定义策略中定义应用程序见解技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure 活动目录 B2C 中的自定义策略中定义应用程序见解技术配置文件。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108570"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中定义应用程序见解技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活动目录 B2C（Azure AD B2C）支持使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到[应用程序见解](../azure-monitor/app/app-insights-overview.md)。  借助应用见解技术配置文件，您可以获取用户旅程的详细和自定义事件日志，以便：

* 洞察用户行为。
* 排查自己在开发或生产过程中的策略问题。
* 衡量性能。
* 通过 Application Insights 创建通知。


## <a name="protocol"></a>协议

"**Name****协议"** 元素的名称属性需要设置为`Proprietary`。 **处理程序**属性必须包含 Azure AD B2C 用于应用程序见解的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例显示了常见的应用程序见解技术配置文件。 其他应用程序见解技术配置文件包括 Azure Insights 通用以利用其配置。  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>输入声明

**InputClaims**元素包含要发送到应用程序见解的声明列表。 您还可以将声明的名称映射到您希望显示在应用程序见解中的名称。 下面的示例演示如何向应用程序见解发送遥测数据。 事件的属性通过语法`{property:NAME}`添加，其中 NAME 是添加到事件的属性。 默认值可以是静态值，也可以是由受支持[的声明解析器](claim-resolver-overview.md)之一解析的值。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaims转换**元素可能包含**一个输入声明转换**元素的集合，这些元素用于修改输入声明或在发送到应用程序见解之前生成新声明。

## <a name="persist-claims"></a>保存声明

不使用持久声明元素。

## <a name="output-claims"></a>输出声明

不使用"输出声明"和"输出声明转换"元素。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”元素。


## <a name="metadata"></a>元数据

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| InstrumentationKey| 是 | 应用程序见解[检测密钥](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)，用于记录事件。 | 
| DeveloperMode| 否 | 指示是否已启用开发人员模式的布尔。 可能的值：`true`或`false`（默认值）。 此元数据控制事件如何缓冲。 在事件卷最少的开发环境中，启用开发人员模式会导致事件立即发送到应用程序见解。|  
|禁用遥测 |否 |指示是否应启用遥测的布尔。 可能的值：`true`或`false`（默认值）。| 


## <a name="next-steps"></a>后续步骤

- [创建应用程序见解资源](../azure-monitor/app/create-new-resource.md)
- 了解如何[使用应用程序见解跟踪 Azure 活动目录 B2C 中的用户行为](analytics-with-application-insights.md)
