---
title: Azure 前门中的 Web 应用程序防火墙排除列表 - Azure 门户
description: 本文提供有关 Azure Front 与 Azure 门户一起配置排除列表配置的信息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925925"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>包含前门服务排除列表的 Web 应用程序防火墙 （WAF） 

有时，Web 应用程序防火墙 （WAF） 可能会阻止您希望允许的应用程序的请求。 例如，Active Directory 插入用于身份验证的令牌。 这些令牌可以包含特殊字符，这些字符可能会触发 WAF 规则中的误报。 WAF 排除列表允许你忽略 WAF 评估中的某些请求属性。  可以使用[PowserShell、Azure](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [CLI、Rest](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 门户配置排除列表。 下面的示例显示了 Azure 门户配置。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>使用 Azure 门户配置排除列表
根据**托管规则**可从 WAF 门户访问**管理排除**项

![管理排除](../media/waf-front-door-exclusion/exclusion1.png)
![管理管理exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 排除列表示例：![管理exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

此示例排除*用户*标头字段中的值。 有效的请求可能包括包含触发 SQL 注入规则的字符串*的用户*字段。 在这种情况下，您可以排除*用户*参数，以便 WAF 规则不会计算字段中的任何内容。

可以按名称向排除列表添加以下属性。 不会根据 WAF 规则计算您使用的字段的值，但会计算它们的名称。 排除列表删除了对该字段值的检查。

* 请求标头名称
* 请求 Cookie 名称
* 查询字符串 args 名称
* 请求正文帖子 args 名称

可以指定请求标头、正文、cookie 或查询字符串属性的完全匹配项。  也可以选择指定部分匹配项。 以下运算符是支持的匹配条件：

- **等于**：此运算符用于完全匹配。 例如，要选择名为**bearerToken**的标头，请使用等于运算符，选择器设置为**无记名标记**。
- **开头**：此运算符与以指定选择器值开头的所有字段匹配。
- **结尾**：此运算符与以指定选择器值结尾的所有请求字段匹配。
- **包含**：此运算符与包含指定选择器值的所有请求字段匹配。
- **等于任何**：此运算符匹配所有请求字段。 * 是选择器值。

标头和 Cookie 名称不区分大小写。

可以将排除列表应用于托管规则集中的所有规则、特定规则组的规则或单个规则，如上例所示。 

## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，了解如何查看 WAF 日志。 有关详细信息，请参阅[前门诊断](../afds/waf-front-door-monitor.md)。
