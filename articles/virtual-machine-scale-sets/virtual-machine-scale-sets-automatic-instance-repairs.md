---
title: Azure 虚拟机规模集的自动实例修复
description: 了解如何为规模集中的 VM 实例配置自动修复策略
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 9e2b15eceff9bca4cee960fa462eb5148e3716dd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197040"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集的自动实例修复

为 Azure 虚拟机规模集启用自动实例修复有助于通过维护一组正常运行的实例来实现应用程序的高可用性。 如果发现规模集在[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)报告时不正常，则此功能会通过删除不正常的实例并创建一个新实例来替换它来自动执行实例修复。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自动实例修复的要求

**启用规模集的应用程序运行状况监视**

规模集应对实例启用应用程序运行状况监视。 可以使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)来完成此操作。 一次只能启用其中一个。 应用程序运行状况扩展或负载均衡器探测对虚拟机实例上配置的应用程序终结点进行 ping 操作，以确定应用程序的运行状况状态。 此运行状况状态由规模集 orchestrator 用于监视实例运行状况，并在需要时执行修复。

**配置终结点以提供运行状况状态**

启用自动实例修复策略之前，请确保规模集实例具有配置的应用程序终结点，以发出应用程序运行状况状态。 当实例在此应用程序终结点上返回状态200（"正常"）时，该实例将被标记为 "正常"。 在所有其他情况下，实例被标记为 "不正常"，包括以下方案：

- 在虚拟机实例中未配置可提供应用程序运行状况状态的应用程序终结点时
- 当应用程序终结点配置不正确时
- 当应用程序终结点不可访问时

对于标记为 "不正常" 的实例，规模集会触发自动修复。 请确保在启用自动修复策略之前正确配置了应用程序终结点，以便在配置终结点时避免意外的实例修复。

**启用单个放置组**

此功能当前仅可用于部署为单个放置组的规模集。 对于规模集，应将 " *singlePlacementGroup* " 属性设置为 " *true* " 以使用自动实例修复功能。 详细了解[放置组](./virtual-machine-scale-sets-placement-groups.md#placement-groups)。

**API 版本**

计算 API 版本2018-10-01 或更高版本支持自动修复策略。

**对资源或订阅的限制**

启用自动修复功能时，当前不支持对规模集进行资源或订阅移动。

**Service fabric 规模集限制**

Service fabric 规模集当前不支持此功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自动实例修复如何工作？

自动实例修复功能依赖于规模集中单个实例的运行状况监视。 规模集中的 VM 实例可以配置为使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)发出应用程序运行状况状态。 如果发现实例不正常，则规模集通过删除不正常的实例并创建一个新实例来替换它来执行修复操作。 最新的虚拟机规模集模型用于创建新的实例。 可以使用*automaticRepairsPolicy*对象在虚拟机规模集模型中启用此功能。

### <a name="batching"></a>批处理

自动实例修复操作以批处理的顺序执行。 在任意给定时间，规模集中的实例数不能超过5%，只能通过自动修复策略进行修复。 如果在同一时间发现不正常，这有助于避免同时删除和重新创建大量实例。

### <a name="grace-period"></a>宽限期

当实例由于对规模集（例如重置映像、重新部署、更新等）执行 PUT、PATCH 或 POST 操作而经历状态更改操作时，只有在等待宽限期后，才会执行该实例上的任何修复操作。 宽限期是指允许实例返回到正常状态的时间量。 宽限期在状态更改完成后开始。 这有助于避免任何过早或意外的修复操作。 宽限期适用于规模集中的任何新创建的实例（包括作为修复操作的结果而创建的实例）。 宽限期以 ISO 8601 格式指定，可使用*gracePeriod*属性进行设置。 宽限期范围为30分钟到90分钟，默认值为30分钟。

### <a name="suspension-of-repairs"></a>暂停修复 

虚拟机规模集提供了在需要时临时挂起自动实例修复的功能。 虚拟机规模集的实例视图中属性*orchestrationServices*下的*serviceState*自动修复显示了自动修复的当前状态。 如果选择将规模集加入自动修复，则将参数*serviceState*的值设置为 "*正在运行*"。 如果为规模集挂起了自动修复，则参数*serviceState*将设置为 "已*挂起*"。 如果在规模集上定义*automaticRepairsPolicy* ，但未启用自动修复功能，则参数*serviceState*将设置为 "*未运行*"。

如果新创建的实例替换了规模集中的不正常的实例，即使在重复执行修复操作后仍保持不正常，则平台会将自动修复的*serviceState*更新为 "*挂起*"。 可以通过将*serviceState*的值设置为 "要*运行*的自动修复" 来再次恢复自动修复。 有关[查看和更新规模集的自动修复策略的服务状态](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy)一节中提供了详细说明。 

自动实例修复过程如下所示：

1. [应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)ping 规模集中每个虚拟机内的应用程序终结点，以获取每个实例的应用程序运行状况状态。
2. 如果终结点使用状态200（"正常"）进行响应，则实例将被标记为 "正常"。 在所有其他情况下（包括终结点不可访问时），该实例将被标记为 "不正常"。
3. 如果发现实例不正常，则规模集会通过删除不正常的实例并创建一个新实例来替换它来触发修复操作。
4. 实例修复以批处理的顺序执行。 在任意给定时间，规模集中的总实例数不能超过5%。 如果规模集的实例数少于20个，则每次对一个不正常的实例进行修复。
5. 在修复规模集中的所有不正常实例之前，上述过程将继续。

## <a name="instance-protection-and-automatic-repairs"></a>实例保护和自动修复

如果规模集中的实例是通过应用其中一个[保护策略](./virtual-machine-scale-sets-instance-protection.md)来保护的，则不会对该实例执行自动修复。 这同时适用于保护策略： "*防止从规模缩小*并*防止规模集*操作"。 

## <a name="terminatenotificationandautomaticrepairs"></a>终止通知和自动修复

如果在规模集上启用了[终止通知](./virtual-machine-scale-sets-terminate-notification.md)功能，则在自动修复操作期间，删除不正常的实例将遵循终止通知配置。 终止通知通过 Azure 元数据服务发送–计划事件–在配置的延迟超时的持续时间内延迟实例删除。 但是，创建新实例来替换不正常的实例不会等待延迟超时完成。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>创建新规模集时启用自动修复策略

若要在创建新规模集时启用自动修复策略，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应为规模集实例正确配置应用程序终结点，以避免在配置终结点时触发意外修复。 对于新创建的规模集，仅在等待宽限期的持续时间后，才会执行任何实例修复。 若要在规模集中启用自动实例修复，请在虚拟机规模集模型中使用*automaticRepairsPolicy*对象。

你还可以使用此[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe)部署负载均衡器运行状况探测的虚拟机规模集，并启用自动实例修复，宽限期为30分钟。

### <a name="azure-portal"></a>Azure 门户
 
以下步骤将在创建新规模集时启用自动修复策略。
 
1. 请参阅**虚拟机规模集**。
1. 选择 " **+ 添加**" 创建新的规模集。
1. 请在 "**运行状况**" 选项卡上执行。 
1. 找到 "**运行状况**" 部分。
1. 启用 "**监视应用程序运行状况**" 选项。
1. 找到**自动修复策略**部分。
1. 启用 **"** **自动修复**" 选项。
1. 在 "**宽限期（分钟）**" 中，指定允许值介于30到90分钟之间的宽限期（分钟）。 
1. 创建新规模集后，请选择 "**查看 + 创建**" 按钮。

### <a name="rest-api"></a>REST API

下面的示例演示如何在规模集模型中启用自动实例修复。 使用 API 2018-10-01 或更高版本。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

使用[AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 创建新的规模集时，可以启用自动实例修复功能。 此示例脚本演示如何使用配置文件创建规模集和关联的资源：[创建一个完整的虚拟机规模集](./scripts/powershell-sample-create-complete-scale-set.md)。 可以通过将参数*EnableAutomaticRepair*和*AutomaticRepairGracePeriod*添加到用于创建规模集的配置对象来配置自动实例修复策略。 以下示例启用此功能，宽限期为30分钟。

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下示例在使用*[az vmss create](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* 创建新规模集时启用自动修复策略。 首先创建一个资源组，然后创建一个将自动修复策略宽限期设置为30分钟的新规模集。

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

上面的示例使用现有的负载均衡器和运行状况探测来监视实例的应用程序运行状况状态。 如果希望改用应用程序运行状况扩展进行监视，可以创建规模集、配置应用程序运行状况扩展，然后使用*az vmss update*启用自动实例修复策略，如下一节中所述。

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>更新现有规模集时启用自动修复策略

在现有规模集中启用自动修复策略之前，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应为规模集实例正确配置应用程序终结点，以避免在配置终结点时触发意外修复。 若要在规模集中启用自动实例修复，请在虚拟机规模集模型中使用*automaticRepairsPolicy*对象。

更新现有规模集的模型后，确保最新的模型应用于该刻度的所有实例。 请参阅有关如何将[vm 引入最新规模集模型](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)的说明。

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户修改现有规模集的自动修复策略。 
 
1. 请参阅现有虚拟机规模集。
1. 在左侧菜单的 "**设置**" 下，选择 "**运行状况和修复**"。
1. 启用 "**监视应用程序运行状况**" 选项。
1. 找到**自动修复策略**部分。
1. 启用 **"** **自动修复**" 选项。
1. 在 "**宽限期（分钟）**" 中，指定允许值介于30到90分钟之间的宽限期（分钟）。 
1. 完成后，选择“保存”****。 

### <a name="rest-api"></a>REST API

以下示例启用宽限期为40分钟的策略。 使用 API 2018-10-01 或更高版本。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

使用[AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 可在现有规模集中修改自动实例修复功能的配置。 下面的示例将宽限期更新为40分钟。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下面是使用*[az vmss update](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* 更新现有规模集的自动实例修复策略的示例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>查看并更新自动实例修复策略的服务状态

### <a name="rest-api"></a>REST API 

对于虚拟机规模集，使用 API 版本2019-12-01 或更高版本的 "[获取实例" 视图](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview)，在属性*orchestrationServices*下查看自动修复的*serviceState* 。 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

在虚拟机规模集上使用 API 版本2019-12-01 或更高版本的*setOrchestrationServiceState* API 设置自动修复的状态。 将规模集选择到自动修复功能后，可以使用此 API 来挂起或恢复规模集的自动修复。 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

使用[get-help](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) cmdlet 查看自动实例修复的*serviceState* 。 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

使用[set-orchestration-service-state](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) serviceState cmdlet 更新自动实例修复的*serviceState* 。 将规模集选择为自动修复功能后，可以使用此 cmdlet 来挂起或恢复规模集的自动修复。 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

结合使用[AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) cmdlet 和参数*InstanceView* ，查看*ServiceState*以进行自动实例修复。

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

使用 AzVmssOrchestrationServiceState cmdlet 更新*serviceState*以进行自动实例修复。 一旦将规模集选择为自动修复功能，就可以使用此 cmdlet 来挂起或恢复规模集的自动修复。

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>疑难解答

**启用自动修复策略失败**

如果在 "properties" 类型的对象上出现 "BadRequest" 错误，并显示消息 "找不到成员 ' automaticRepairsPolicy '"，请检查用于虚拟机规模集的 API 版本。 此功能需要 API 版本2018-10-01 或更高版本。

**即使启用策略，也不会修复实例**

实例可能处于宽限期。 这是在执行修复之前，在对实例进行任何状态更改后要等待的时间量。 这是为了避免任何过早或意外的修复。 完成实例的宽限期后，应执行修复操作。

**查看规模集实例的应用程序运行状况状态**

你可以使用虚拟机规模集中实例的[获取实例视图 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)来查看应用程序运行状况状态。 使用 Azure PowerShell，你可以将 cmdlet [AzVmssVM](/powershell/module/az.compute/get-azvmssvm)与 *-InstanceView*标志一起使用。 应用程序运行状况在属性*vmHealth*下提供。

在 Azure 门户中，还可以查看运行状况状态。 中转到现有规模集，从左侧菜单中选择 "**实例**"，并查看每个规模集实例的运行状况状态的 "**健康状况**" 列。 

## <a name="next-steps"></a>后续步骤

了解如何为规模集配置[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)。
