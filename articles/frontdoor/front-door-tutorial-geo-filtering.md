---
title: 教程 - 为 Azure Front Door 服务配置地区筛选 Web 应用程序防火墙策略
description: 本教程介绍如何创建简单的地区筛选策略并将该策略与现有的 Front Door 前端主机相关联
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: 8a1fb0c3270d4899f05190fb1745075584f613ab
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357164"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>如何为 Front Door 设置地区筛选 WAF 策略
本教程介绍如何使用 Azure PowerShell 创建简单的地区筛选策略并将该策略与现有的 Front Door 前端主机相关联。 此示例地区筛选策略会阻止除美国之外的所有其他国家/地区的请求。

如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
在开始设置地区筛选策略之前，请设置 PowerShell 环境并创建 Front Door 配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 遵照页面上的说明使用 Azure 凭据登录，并安装 Az PowerShell 模块。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用交互式登录对话框连接到 Azure
```
Connect-AzAccount
Install-Module -Name Az
```
确保已安装 PowerShellGet 最新版本。 运行下面的命令，重新打开 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>创建 Front Door 配置文件
遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建 Front Door 配置文件](quickstart-create-front-door.md)。

## <a name="define-geo-filtering-match-condition"></a>定义地区筛选匹配条件

创建一个示例匹配条件，该条件选择不是来自“US”的请求。创建匹配条件时，请在参数中使用 [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject)。 [此处](front-door-geo-filtering.md)提供了用于国家/地区映射的双字母国家/地区代码。

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>将地区筛选匹配条件添加到包含“操作”和“优先级”的规则

使用 [New-AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorcustomruleobject) 根据匹配条件、操作和优先级创建 CustomRule 对象 `nonUSBlockRule`。  一个 CustomRule 可以有多个 MatchCondition。  在此示例中，“操作”设置为“阻止”，“优先级”设置为 1（最高优先级）。

```
$nonUSBlockRule = New-AzFrontDoorCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>将规则添加到策略
使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，在包含该 Front Door 配置文件的指定资源组中，使用 [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) 创建包含 `nonUSBlockRule` 的 `geoPolicy` 策略对象。 必须为地区策略提供唯一名称。 

以下示例使用资源组名称 *myResourceGroupFD1*，并假设已遵照以下文章中的说明创建了 Front Door 配置文件：[快速入门：创建 Front Door](quickstart-create-front-door.md)。 在下面的示例中，请将策略名称 *geoPolicyAllowUSOnly* 替换为唯一的策略名称。

```
$geoPolicy = New-AzFrontDoorFireWallPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>将 WAF 策略链接到 Front Door 前端主机
将 WAF 策略对象链接到现有的 Front Door 前端主机并更新 Front Door 属性。 

为此，请先使用 [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) 检索 Front Door 对象。 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

接下来，使用 [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) 将前端 WebApplicationFirewallPolicyLink 属性设置为 `geoPolicy` 的 resourceId。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 只需设置 WebApplicationFirewallPolicyLink 属性一次，即可将 WAF 策略链接到 Front Door 前端主机。 后续策略更新会自动应用到前端主机。

## <a name="next-steps"></a>后续步骤

- 了解[通过 Front Door 实现的应用程序层安全性](front-door-application-security.md)。
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
