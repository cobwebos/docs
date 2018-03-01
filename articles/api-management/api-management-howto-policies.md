---
title: "Azure API 管理中的策略 | Microsoft Docs"
description: "了解如何在 API 管理创建、编辑和配置策略。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 54fbba197f6609731ffaf3ff15143a28e70a955f
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="policies-in-azure-api-management"></a>Azure API 管理中的策略

在 Azure API 管理 (APIM) 中，策略是一项强大的系统功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 流行的语句包括从 XML 到 JSON 的格式转换，并调用速率限制来限制从一名开发人员的传入调用。 许多策略开箱即用。

策略在网关内部应用，该网关位于 API 使用者和托管 API 之间。 该网关接收所有请求，并通常将其原封不动地转发到基础 API。 但是策略可以将更改应用于入站的请求和出站响应。

在任何 API 管理策略中，策略表达式可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流][Control flow]和[设置变量][Set variable]策略）基于策略表达式。 有关详细信息，请参阅[高级策略][Advanced policies]和[策略表达式][Policy expressions]。

## <a name="sections"> </a>了解策略配置

策略定义是一个简单的 XML 文档，用于描述一个入站和出站语句序列。 可以直接在定义窗口中编辑 XML。 右侧提供语句的列表，同时启用适用于当前范围的语句并突出显示。

单击启用的语句会在定义视图中的光标位置添加相应的 XML。 

> [!NOTE]
> 如果无法启用要添加的策略，请确保为该策略设置的范围是正确的。 每个策略语句都设计有特定的使用范围，需在特定的策略部分使用。 若要查看某个策略的策略部分和范围，请参阅[策略参考][Policy Reference]中该策略的“用法”部分。
> 
> 

配置划分为 `inbound`、`backend`、`outbound` 和 `on-error`。 指定的策略语句系列按请求和响应顺序执行。

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

如果在处理请求的过程中出错，则会忽略 `inbound`、`backend` 或 `outbound` 部分的其余步骤，跳到 `on-error` 部分执行相关语句。 将策略语句置于 `on-error` 部分以后，即可使用 `context.LastError` 属性查看错误、使用 `set-body` 策略检查和自定义错误响应，以及配置发生错误时的应对措施。 错误代码可针对内置步骤，也可针对在处理策略语句的过程中会发生的错误。 有关详细信息，请参阅 [Error handling in API Management policies](https://msdn.microsoft.com/library/azure/mt629506.aspx)（API 管理策略中的错误处理）。

## <a name="scopes"> </a>如何配置策略

有关如何配置策略的信息，请参阅[设置或编辑策略](set-edit-policies.md)。

## <a name="policy-reference"></a>策略参考

请参阅[策略参考](api-management-policy-reference.md)了解政策说明完整列表及其设置。

## <a name="policy-samples"></a>策略示例

请参阅[策略示例](policy-samples.md)获取更多代码示例。

## <a name="examples"></a>示例

### <a name="apply-policies-specified-at-different-scopes"></a>应用在不同范围指定的策略

如果在全局级别有一个策略并且为 API 配置了一个策略，则只要使用该特定 API，这两种策略都会被应用。 API 管理允许通过基础元素实现组合策略声明的确定性排序。 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

在上述示例策略定义中，`cross-domain` 语句会在执行任何更高版本的策略前执行，之后是 `find-and-replace` 策略。 

### <a name="restrict-incoming-requests"></a>限制传入的请求

要添加新的语句以限制到指定 IP 地址的入站请求，请将光标置于 `inbound` XML 元素的内容中，然后单击“限制调用方 IP”语句。

![限制策略][policies-restrict]

这会将 XML 代码片段添加到 `inbound` 元素，提供如何配置该语句的指导。

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

要限制入站请求并接受来自 IP 地址 1.2.3.4 的那些，请修改 XML，如下所示：

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
