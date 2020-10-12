---
title: 教程 - 配置地区筛选 WAF 策略 - Azure Front Door
description: 本教程介绍如何创建地区筛选 WAF 策略并将该策略与现有的 Front Door 前端主机相关联。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324019"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>教程：如何为 Front Door 设置地区筛选 WAF 策略
本教程介绍如何使用 Azure PowerShell 创建简单的地区筛选策略并将该策略与现有的 Front Door 前端主机相关联。 此示例地区筛选策略会阻止除美国之外的所有其他国家/地区的请求。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 定义地区筛选匹配条件。
> - 将地区筛选匹配条件添加到规则。
> - 将规则添加到策略。
> - 将 WAF 策略链接到 FrontDoor 前端主机。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件
* 在开始设置地区筛选策略之前，请设置 PowerShell 环境并创建 Front Door 配置文件。
* 遵照以下文档中的说明创建 Front Door：[快速入门：创建 Front Door 配置文件](quickstart-create-front-door.md)。

## <a name="define-geo-filtering-match-condition"></a>定义地区筛选匹配条件

创建一个示例匹配条件，在创建匹配条件时使用 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 选择不是来自“美国”的请求。 [此处](front-door-geo-filtering.md)提供了用于国家/地区映射的双字母国家/地区代码。

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>将地区筛选匹配条件添加到包含“操作”和“优先级”的规则

使用 [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) 根据匹配条件、操作和优先级创建 CustomRule 对象 `nonUSBlockRule`。  一个 CustomRule 可以有多个 MatchCondition。  在此示例中，“操作”设置为“阻止”，“优先级”设置为 1（最高优先级）。

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>将规则添加到策略
使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，在包含该 Front Door 配置文件的指定资源组中，使用 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 创建包含 `nonUSBlockRule` 的 `geoPolicy` 策略对象。 必须为地区筛选策略提供唯一名称。 

以下示例使用资源组名称 FrontDoorQS_rg0，并假设已按照以下文章中的说明创建了 Front Door 配置文件：[快速入门：创建 Front Door](quickstart-create-front-door.md)。 在下面的示例中，请将策略名称 *geoPolicyAllowUSOnly* 替换为唯一的策略名称。

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>将 WAF 策略链接到 Front Door 前端主机
将 WAF 策略对象链接到现有的 Front Door 前端主机并更新 Front Door 属性。 

为此，请先使用 [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) 检索 Front Door 对象。 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
接下来，使用 [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) 将前端 WebApplicationFirewallPolicyLink 属性设置为 `geoPolicy` 的 resourceId。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 只需设置 WebApplicationFirewallPolicyLink 属性一次，即可将 WAF 策略链接到 Front Door 前端主机。 后续策略更新会自动应用到前端主机。

## <a name="clean-up-resources"></a>清理资源

在上述步骤中，已配置了关联到 WAF 策略的地区筛选规则。 然后，将策略链接到 Front Door 的前端主机。 如果不再需要地区筛选规则或 WAF 策略，则必须先取消策略与前端主机之间的关联，然后才能删除 WAF 策略。

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="取消 WAF 策略关联":::

## <a name="next-steps"></a>后续步骤

若要了解如何为 Front Door 配置 Web 应用程序防火墙，请继续学习下一教程。

> [!div class="nextstepaction"]
> [Web 应用程序防火墙和 Front Door](front-door-waf.md)
