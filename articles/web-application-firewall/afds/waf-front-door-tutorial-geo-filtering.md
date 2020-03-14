---
title: 为 Azure 前门服务配置异地筛选 web 应用程序防火墙策略
description: 在本教程中，将了解如何创建异地筛选策略并将该策略与现有的前端前端主机关联
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: abcef61d478eccb4e979b60eb845ac8d398a49f9
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135864"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>为前门设置异地筛选 WAF 策略

本教程介绍如何使用 Azure PowerShell 创建简单的地区筛选策略并将该策略与现有的 Front Door 前端主机相关联。 此示例地区筛选策略会阻止除美国之外的所有其他国家/地区的请求。

如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

在开始设置地区筛选策略之前，请设置 PowerShell 环境并创建 Front Door 配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 遵照页面上的说明使用 Azure 凭据登录，并安装 Az PowerShell 模块。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用用于登录的交互式对话连接到 Azure

```
Install-Module -Name Az
Connect-AzAccount
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

按照[快速入门：创建前门配置文件](../../frontdoor/quickstart-create-front-door.md)中所述的说明创建前门配置文件。

## <a name="define-geo-filtering-match-condition"></a>定义地区筛选匹配条件

创建一个示例匹配条件，在创建匹配条件时使用 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 选择不是来自“美国”的请求。 [对于 Azure 前门，域中的地域筛选](waf-front-door-geo-filtering.md)是指国家/地区的双字母国家/地区代码。

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>将地区筛选匹配条件添加到包含“操作”和“优先级”的规则

使用 `nonUSBlockRule`New-AzFrontDoorWafCustomRuleObject[ 根据匹配条件、操作和优先级创建 CustomRule 对象 ](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)。  一个 CustomRule 可以有多个 MatchCondition。  在此示例中，“操作”设置为“阻止”，“优先级”设置为 1（最高优先级）。

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>将规则添加到策略

使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，在包含该 Front Door 配置文件的指定资源组中，使用 `geoPolicy`New-AzFrontDoorWafPolicy`nonUSBlockRule` 创建包含 [ 的 ](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 策略对象。 必须为地区策略提供唯一名称。 

以下示例使用资源组名称*myResourceGroupFD1* ，假设已使用[快速入门：创建前门](../../frontdoor/quickstart-create-front-door.md)文章中提供的说明创建了前门配置文件。 在下面的示例中，请将策略名称 *geoPolicyAllowUSOnly* 替换为唯一的策略名称。

```
$geoPolicy = New-AzFrontDoorWafPolicy `
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

接下来，使用 `geoPolicy`Set-AzFrontDoor[ 将前端 WebApplicationFirewallPolicyLink 属性设置为 ](/powershell/module/az.frontdoor/set-azfrontdoor) 的 resourceId。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 只需设置 WebApplicationFirewallPolicyLink 属性一次，即可将 WAF 策略链接到 Front Door 前端主机。 后续策略更新会自动应用到前端主机。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Web 应用程序防火墙](../overview.md)。
- 了解如何[创建 Front Door](../../frontdoor/quickstart-create-front-door.md)。
