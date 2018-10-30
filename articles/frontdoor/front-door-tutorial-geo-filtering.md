---
title: 教程 - 在 Azure Front Door 服务的域中配置地区筛选 | Microsoft Docs
description: 本教程介绍如何创建简单的地区筛选策略并将该策略与现有的 Front Door 前端主机相关联
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: c4032f7c33cec7b7a7864ccff07a05b87c945949
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988574"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>如何为 Front Door 设置地区筛选策略
本教程介绍如何使用 Azure PowerShell 创建简单的地区筛选策略并将该策略与现有的 Front Door 前端主机相关联。 此示例地区筛选策略会阻止除美国之外的所有其他国家/地区的请求。

## <a name="1-set-up-your-powershell-environment"></a>1.设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 按照页面中的说明，使用 Azure 凭据登录并安装 AzureRM。
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 支持即将推出。

在安装 Front Door 模块之前，请确保安装最新版本的 PowerShellGet。 运行下面的命令，重新打开 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

安装 AzureRM.FrontDoor 模块。 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2.定义地区筛选匹配条件
首先创建一个示例匹配条件，该条件选择不是来自“美国”的请求。 请参阅 PowerShell [指南](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject)，了解创建匹配条件时的参数。 [此处](/Protection/GeoFiltering)提供用于国家/地区映射的双字母国家/地区代码。

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3.将地区筛选匹配条件添加到包含“操作”和“优先级”的规则

然后，根据匹配条件、操作和优先级创建 CustomRule 对象 `nonUSBlockRule`。  一个 CustomRule 可以有多个 MatchCondition。  在此示例中，“操作”设置为“阻止”，“优先级”设置为 1（最高优先级）。

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

请参阅 PowerShell [指南](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject)，了解创建 CustomRuleObject 时的参数。

## <a name="4-add-rules-to-a-policy"></a>4.向策略添加规则
此步骤创建一个 `geoPolicy` 策略对象，其中包含指定资源组中的来自上一步骤的 `nonUSBlockRule`。 使用 `Get-AzureRmResourceGroup` 查找 ResourceGroupName $resourceGroup。

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

请参阅 PowerShell [指南](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy)，了解创建策略时的参数。

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5.将策略链接到 Front Door 前端主机
最后的步骤是将保护策略对象链接到现有的 Front Door 前端主机并更新 Front Door 属性。 首先检索 Front Door 对象，方法是：先使用 [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor)，接着将其前端 WebApplicationFirewallPolicyLink 属性设置为 `geoPolicy` 的 resourceId。

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

使用以下[命令](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor)更新 Front Door 对象。

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 只需将 WebApplicationFirewallPolicyLink 属性设置一次，即可将保护策略链接到 Front Door 前端主机。 后续策略更新会自动应用到前端主机。

## <a name="next-steps"></a>后续步骤

- 了解[通过 Front Door 实现的应用程序层安全性](front-door-application-security.md)。
- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
