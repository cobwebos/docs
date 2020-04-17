---
title: 使用 Azure 虚拟机缩放集进行自动实例修复
description: 了解如何在规模集中为 VM 实例配置自动修复策略
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: 8e73ef75b3313656b45d29270d9996c3ad17c630
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538063"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 虚拟机缩放集的自动实例修复

为 Azure 虚拟机扩展集启用自动实例修复有助于通过维护一组正常实例实现应用程序的高可用性。 如果发现比例集中的实例与[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测器](../load-balancer/load-balancer-custom-probe-overview.md)报告的那样不正常，则此功能通过删除不正常的实例并创建新实例来替换它，自动执行实例修复。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自动实例修复的要求

**为规模集启用应用程序运行状况监视**

规模集应启用实例的应用程序运行状况监视。 这可以使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测器](../load-balancer/load-balancer-custom-probe-overview.md)来完成。 一次只能启用其中一个。 应用程序运行状况扩展或负载均衡器探测在虚拟机实例上配置的应用程序终结点以确定应用程序运行状况状态。 此运行状况由规模集协调器用于监视实例运行状况并在需要时执行修复。

**配置终结点以提供运行状况**

在启用自动实例修复策略之前，请确保缩放集实例已配置应用程序终结点以发出应用程序运行状况状态。 当实例在此应用程序终结点上返回状态 200 （OK） 时，该实例将标记为"正常"。 在所有其他情况下，实例标记为"不正常"，包括以下方案：

- 当虚拟机实例中没有配置应用程序终结点以提供应用程序运行状况时
- 当应用程序终结点配置不正确时
- 无法访问应用程序终结点时

对于标记为"不正常"的实例，自动修复由比例集触发。 在启用自动修复策略之前，请确保应用程序终结点配置正确，以避免在配置终结点时意外进行实例修复。

**启用单个放置组**

此功能当前仅适用于作为单个放置组部署的规模集。 属性*单放置组*应设置为*true，* 以便将缩放设置为使用自动实例修复功能。 了解有关[放置组](./virtual-machine-scale-sets-placement-groups.md#placement-groups)的更多。

**API 版本**

计算 API 版本 2018-10-01 或更高版本支持自动修复策略。

**对资源或订阅移动的限制**

启用自动修复功能时，缩放集当前不支持资源或订阅移动。

**对服务结构规模集的限制**

服务结构缩放集当前不支持此功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自动实例修复如何工作？

自动实例修复功能依赖于对规模集中的各个实例的运行状况监视。 缩放集中的 VM 实例可以配置为使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测器](../load-balancer/load-balancer-custom-probe-overview.md)发出应用程序运行状况。 如果发现实例不正常，则缩放集通过删除不正常的实例并创建新实例来替换它来执行修复操作。 最新的虚拟机规模集模型用于创建新实例。 此功能可以使用*自动修复策略*对象在虚拟机缩放集模型中启用。

### <a name="batching"></a>批处理

自动实例修复操作分批执行。 在任意给定时间，通过自动修复策略修复比例集中的实例不超过 5%。 这有助于避免同时删除和重新创建大量实例（如果同时发现不正常）。

### <a name="grace-period"></a>宽限期

当实例由于在规模集上执行的 PUT、PATCH 或 POST 操作（例如重新映像、重新部署、更新等）而通过状态更改操作时，则该实例上的任何修复操作仅在等待宽限期后执行。 宽限期是允许实例返回到正常状态的时间量。 宽限期在状态更改完成后开始。 这有助于避免任何过早或意外的维修操作。 对于比例集中中任何新创建的实例（包括由于修复操作而创建的实例），宽限期都适用。 宽限期以 ISO 8601 格式以分钟为单位指定，可以使用属性 *"自动修复策略.gracePeriod"* 进行设置。 宽限期的范围为 30 分钟到 90 分钟，默认值为 30 分钟。

### <a name="suspension-of-repairs"></a>暂停维修 

虚拟机规模集提供了在需要时临时挂起自动实例修复的功能。 虚拟机规模集实例中属性*业务流程服务*下的自动修复*服务服务状态*显示自动修复的当前状态。 当选择缩放集进行自动修复时，参数*服务状态*的值设置为 *"正在运行*"。 当缩放集的自动修复挂起时，参数*服务状态*设置为*挂起*。 如果在比例集中定义了*自动修复策略*，但未启用自动修复功能，则参数*服务状态*设置为 *"不运行*"。

如果新创建的用于替换规模集中的不正常实例即使在重复执行维修操作后仍不正常，则作为安全措施，平台将*服务状态*更新为"*挂起*"。 通过将自动修复*的服务状态*值设置为 *"正在运行*"，可以再次恢复自动修复。 有关[查看和更新秤集自动维修策略的服务状态](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy)的部分提供了详细说明。 

自动实例修复过程的工作方式如下：

1. [应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)在规模集中的每个虚拟机内对应用程序终结点进行 ping，以获取每个实例的应用程序运行状况状态。
2. 如果终结点响应状态为 200 （OK），则实例将标记为"正常"。 在所有其他情况下（包括终结点无法访问），实例标记为"不正常"。
3. 当发现实例不正常时，缩放集通过删除不正常的实例并创建新实例来替换它来触发修复操作。
4. 实例修复分批执行。 在任何给定时间，将修复规模集中总实例的 5%。 如果比例集的实例少于 20 个实例，则一次为一个不正常的实例执行修复。
5. 上述过程将继续，直到修复比例集中的所有不正常实例。

## <a name="instance-protection-and-automatic-repairs"></a>实例保护和自动修复

如果比例集中的实例通过应用其中一个[保护策略](./virtual-machine-scale-sets-instance-protection.md)受到保护，则不会对该实例执行自动修复。 这同时适用于保护策略：*防止扩展*和*防止缩放集*操作。 

## <a name="terminatenotificationandautomaticrepairs"></a>终止通知和自动修复

如果在规模集中启用[了终止通知](./virtual-machine-scale-sets-terminate-notification.md)功能，则在自动修复操作期间，删除不正常的实例将遵循终止通知配置。 终止通知通过 Azure 元数据服务发送 （ 计划的事件 ）， 并且实例删除在配置的延迟超时期间延迟。 但是，创建新实例以替换不正常的实例不会等待延迟超时完成。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>创建新比例集时启用自动修复策略

对于在创建新比例集时启用自动修复策略，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应正确配置应用程序终结点以用于缩放集实例，以避免在配置终结点时触发意外修复。 对于新创建的缩放集，任何实例修复仅在等待宽限期后执行。 要在比例集中启用自动实例修复，请使用虚拟机缩放集模型中的*自动修复策略*对象。

### <a name="azure-portal"></a>Azure 门户
 
在创建新比例集时，以下步骤启用自动修复策略。
 
1. 转到**虚拟机缩放集**。
1. 选择 **+ 添加**以创建新的比例集。
1. 转到 **"运行状况"** 选项卡。 
1. 找到 **"运行状况**"部分。
1. 启用**监视器应用程序运行状况**选项。
1. 找到 **"自动维修策略"** 部分。
1. **打开****"自动修复"** 选项。
1. 在**宽限期（分钟）** 中，以分钟指定宽限期，允许的值在 30 到 90 分钟之间。 
1. 创建新比例集后，选择 **"查看 + 创建**"按钮。

### <a name="rest-api"></a>REST API

下面的示例演示如何在比例集模型中启用自动实例修复。 使用 API 版本 2018-10-01 或更高版本。

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

可以使用[New-AzVmsConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 创建新比例集时启用自动实例修复功能。 此示例脚本使用配置文件浏览比例集和相关资源的创建：[创建完整的虚拟机规模集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以通过将参数*启用"自动修复"* 和 *"自动修复宽限期"* 添加到用于创建比例集的配置对象来配置自动实例修复策略。 下面的示例启用宽限期为 30 分钟的功能。

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

下面的示例启用自动修复策略，同时使用*[az vms 创建](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* 创建新的缩放集。 首先创建资源组，然后创建一个新的规模集，自动修复策略宽限期设置为 30 分钟。

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
  --automatic-repairs-period 30
```

上述示例使用现有的负载均衡器和运行状况探测器来监视实例的应用程序运行状况状态。 如果您更喜欢使用应用程序运行状况扩展进行监视，则可以创建比例集，配置应用程序运行状况扩展，然后使用*az vmss 更新*启用自动实例修复策略，如下一节所述。

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>更新现有规模集时启用自动修复策略

在现有比例集中启用自动修复策略之前，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应正确配置应用程序终结点以用于缩放集实例，以避免在配置终结点时触发意外修复。 要在比例集中启用自动实例修复，请使用虚拟机缩放集模型中的*自动修复策略*对象。

更新现有比例集的模型后，请确保将最新模型应用于比例的所有实例。 请参阅有关如何使 VM 与[最新的比例集模型](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)更新的说明。

### <a name="azure-portal"></a>Azure 门户

您可以通过 Azure 门户修改现有规模集的自动修复策略。 
 
1. 转到现有虚拟机规模集。
1. 在左侧菜单中的 **"设置"** 下，选择 **"运行状况"和"修复**"。
1. 启用**监视器应用程序运行状况**选项。
1. 找到 **"自动维修策略"** 部分。
1. **打开****"自动修复"** 选项。
1. 在**宽限期（分钟）** 中，以分钟指定宽限期，允许的值在 30 到 90 分钟之间。 
1. 完成后，选择“保存”****。 

### <a name="rest-api"></a>REST API

下面的示例启用宽限期为 40 分钟的策略。 使用 API 版本 2018-10-01 或更高版本。

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

使用[Update-AzVms](/powershell/module/az.compute/update-azvmss) cmdlet 修改现有规模集中自动实例修复功能的配置。 下面的示例将宽限期更新为 40 分钟。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下面是使用*[az vms 更新](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* 更新更新现有规模集的自动实例修复策略的示例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>查看和更新自动实例修复策略的服务状态

### <a name="rest-api"></a>REST API 

使用 API 版本 2019-12-01 或更高版本的虚拟机规模设置[获取实例视图](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview)，以查看*服务状态*，以便根据属性*业务流程服务*进行自动修复。 

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

在虚拟机规模集中使用 API 版本 2019-12-01 或更高版本的*SetOrchestraServiceState* API 来设置自动修复的状态。 选择缩放集进入自动修复功能后，可以使用此 API 暂停或恢复缩放集的自动修复。 

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

使用[获取实例视图](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view)cmdlet 查看*服务状态*以进行自动实例修复。 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

使用[集式-编排-服务状态](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state)cmdlet 更新*服务状态*以进行自动实例修复。 选择缩放集进入自动修复功能后，您可以使用此 cmdlet 暂停或恢复缩放集的自动修复。 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

使用带有参数*实例视图*[的 Get-AzVms](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) cmdlet 查看*服务状态*以进行自动实例修复。

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

使用 Set-AzVmsOrchestraServiceServiceServiceScmdlet 更新*服务状态*以进行自动实例修复。 选择缩放集进入自动修复功能后，您可以使用此 cmdlet 暂停或恢复缩放集的自动修复。

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>疑难解答

**无法启用自动修复策略**

如果收到"BadRequest"错误，其中显示一条消息，指出"在类型'属性'的对象上找不到成员'自动修复策略'"，请检查用于虚拟机规模集的 API 版本。 此功能需要 API 版本 2018-10-01 或更高版本。

**即使启用策略，实例也不会修复**

实例可能处于宽限期。 这是执行修复之前实例上任何状态更改后等待的时间量。 这是为了避免任何过早或意外的维修。 实例的宽限期完成后，应执行修复操作。

**查看缩放集实例的应用程序运行状况**

您可以将[获取实例视图 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)用于虚拟机规模集中的实例以查看应用程序运行状况状态。 使用 Azure PowerShell，可以将 cmdlet [Get-AzVmsVM](/powershell/module/az.compute/get-azvmssvm)与 *-实例视图*标志一起使用。 应用程序运行状况状态在属性*vmHealth*下提供。

在 Azure 门户中，您还可以查看运行状况状态。 转到现有比例集，从左侧的菜单中选择**实例**，然后查看"**运行状况状态**"列，了解每个比例集实例的运行状况。 

## <a name="next-steps"></a>后续步骤

了解如何为规模集配置[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载平衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)。
