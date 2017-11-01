---
title: "使用 Azure PowerShell 自动缩放虚拟机规模集 | Microsoft Docs"
description: "如何使用 Azure PowerShell 为虚拟机规模集创建自动缩放规则"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 1fbfbbc79a415af5e874c304412854849e134eb7
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>使用 Azure PowerShell 自动缩放虚拟机规模集
创建规模集时，可定义想运行的 VM 实例数。 若应用程序需要更改，可自动增加或减少 VM 实例数。 通过自动缩放功能，可随客户需求的改变而进行调整，或在应用的整个生命周期内响应应用程序性能更改。

这篇文章演示了如何使用 Azure PowerShell （其在规模集中监视 VM 实例性能）创建自动缩放规则。 这些缩放规则根据性能指标增加或减少 VM 实例数。 也可以使用 [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) 或在 [Azure 门户](virtual-machine-scale-sets-autoscale-portal.md)中完成这些步骤。


## <a name="prerequisites"></a>先决条件
需要现有虚拟机规模集，才能创建自动缩放规则。 可使用 [Azure 门户](virtual-machine-scale-sets-portal-create.md)、[Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell) 或 [Azure CLI 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli) 创建规模集。

若要更轻松地创建自动缩放规则，请为规模集定义几个变量。 以下示例为 myResourceGroup 资源组和美国东部区域内名为 myScaleSet 的规模集定义变量。 使用 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) 获取订阅 ID。 如果帐户关联了多个订阅，则仅返回第一个订阅。 按照如下所示，调整名称和订阅 ID：

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>创建规则，以便自动横向扩展
如果应用程序需求提高，规模集中 VM 实例上的负载将会增大。 如果这种负载增大持续稳定，而不只是短暂的需求，那么可以配置自动缩放规则来增加规模集中的 VM 实例数。 创建这些 VM 实例及部署应用程序后，规模集会开始通过负载均衡器将流量分配到这些实例和应用程序。 可以控制要监视的指标（例如 CPU 或磁盘）、应用程序负载必须处于给定阈值内的时间，以及要添加到规模集的 VM 实例数。

平均 CPU 负载大于 70% 持续了 5 分多钟时，请使用 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) 创建规则增加规模集中的 VM 实例数。 触发规则时，VM 实例数增加 20%。 在 VM 实例数少的规模集中，可保留 `-ScaleActionScaleType` 不变，仅指定 `-ScaleActionValue` 增加一至两个实例。 在有大量 VM 实例的规模集中，增加 10% 或 20% 的 VM 实例可能更合适。

此规则使用以下参数：

| 参数               | 说明                                                                                                         | 值          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | 监视和应用规模集操作的性能指标。                                                   | CPU 百分比 |
| *-TimeGrain*            | 为进行而收集指标分析的频率。                                                                   | 1 分钟       |
| *-MetricStatistic*      | 定义如何聚合已收集的指标以便分析。                                                | 平均值        |
| *-TimeWindow*           | 比较指标与阈值之前监视的时长。                                   | 10 分钟      |
| *-Operator*             | 用于比较指标数据和阈值的运算符。                                                     | 大于   |
| *-Threshold*            | 使自动缩放规则触发操作的值。                                                      | 70%            |
| *-ScaleActionDirection* | 定义应用规则时，规模集应横向扩展还是横向缩减。                                             | 增加       |
| *–ScaleActionScaleType* | 表明 VM 实例数应该增加一定的百分比。                                 | 百分比更改 |
| *-ScaleActionValue*     | 规则触发时，应更改 VM 实例的百分比。                                            | 20             |
| *-ScaleActionCooldown*  | 为使自动缩放操作有时间生效，再次应用规则前需要等待的时间。 | 5 分钟      |

以下示例将创建名为 myRuleScaleOut 的对象，用于保存此横向扩展规则。 -MetricResourceId 使用以前为订阅 ID、资源组名称和规模集名称定义的变量：

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>创建规则，以便自动横向缩减
在夜间或周末，应用程序需求可能会降低。 如果这种负载降低在一段时间内持续稳定，可以配置自动缩放规则来减少规模集中的 VM 实例数。 这种横向缩减操作可以减少运行规模集所需的成本，因为只运行满足当前需求所需的实例数。

平均 CPU 负载小于 30% 持续了 10 多分钟时，请使用 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) 创建规则减少规模集中的 VM 实例数。 触发规则时，VM 实例数减少 20%。以下示例将创建名为 myRuleScaleDown 的对象，用于保存此横向扩展规则。 -MetricResourceId 使用以前为订阅 ID、资源组名称和规模集名称定义的变量：

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>定义自动缩放配置文件
若要将自动缩放规则与规模集相关联，请创建一个配置文件。 自动缩放配置文件定义了默认值、最小规模集容量和最大规模集容量，并与自动缩放规则关联。 使用 [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile) 创建自动缩放配置文件。 以下示例设置了默认值，以及最小容量 2 个 VM 实例、最大容量 10 个 VM。 然后附加前几步中创建的横向扩展和横向缩减规则：

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>将自动缩放规则应用于规模集
最后一步是将自动缩放配置文件应用于规模集。 随后，规模集便能根据应用程序需求自动进行横向扩展或缩减。 使用 [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) 应用自动缩放配置文件，如下所示：

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>监视规模集中的实例数
若要查看 VM 实例的数量与状态，使用 [Get AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM) 查看规模集中的实例列表。 状态指示是随规模集自动横向扩展而预配 VM 实例，还是随规模集自动横向缩减而取消预配 VM 实例。 以下示例查看 myResourceGroup 资源组中 myScaleSet 规模集的 VM 实例状态：

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>按计划自动缩放
前述示例通过 CPU 使用情况等基本主机指标自动横向扩展或缩减规模集。 还可以根据计划创建自动缩放规则。 通过这些基于计划的规则，可在应用程序需求按预期增加（如核心工作时间）之前自动增加 VM 实例数，在预期需求减少时（例如周末）自动减少实例数。

若要根据计划而非主机指标创建自动缩放规模集，请使用 Azure 门户。 当前不能使用 Azure PowerShell 创建基于计划的规则。


## <a name="next-steps"></a>后续步骤
本文详细介绍了如何使用自动缩放规则来进行横向缩放，即增加或减少规模集中的 VM 实例数。 还可进行纵向缩放，即增大或减小 VM 实例的大小。 有关详细信息，请参阅[虚拟机规模集的纵向自动缩放](virtual-machine-scale-sets-vertical-scale-reprovision.md)。

有关如何管理 VM 实例的信息，请参阅[使用 Azure PowerShell 管理虚拟机规模集](virtual-machine-scale-sets-windows-manage.md)。

若要了解如何在触发自动缩放规则时生成警报，请参阅[在 Azure Monitor 中使用自动缩放操作发送电子邮件和 Webhook 警报通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)。 还可以[在 Azure Monitor 中使用审核日志发送电子邮件和 Webhook 警报通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)。
