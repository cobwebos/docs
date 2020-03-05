---
title: Azure 虚拟机规模集的自动实例修复
description: 了解如何为规模集中的 VM 实例配置自动修复策略
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274809"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>预览： Azure 虚拟机规模集的自动实例修复

为 Azure 虚拟机规模集启用自动实例修复有助于通过维护一组正常运行的实例来实现应用程序的高可用性。 如果发现规模集在[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)报告时不正常，则此功能会通过删除不正常的实例并创建一个新实例来替换它来自动执行实例修复。

> [!NOTE]
> 提供此预览功能不带服务级别协议，不建议用于生产工作负荷。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自动实例修复的要求

**选择启用自动实例修复预览**

使用 REST API 或 Azure PowerShell 选择启用自动实例修复预览。 这些步骤将注册你的订阅以获取预览功能。 请注意，这只是使用此功能所需的一次性设置。 如果你的订阅已注册为自动实例修复预览版，则无需再次注册。 

使用 REST API 

1. 使用功能注册功能[-注册](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. 等待几分钟，将*状态*更改为 "*已注册*"。 你可以使用以下 API 来确认这一点。

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. *状态*更改为 "已*注册*" 后，运行以下。

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

使用 Azure PowerShell

1. 使用 cmdlet [register-azurermresourceprovider](/powershell/module/azurerm.resources/register-azurermresourceprovider)注册功能，后跟[register-azurermproviderfeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. 等待几分钟，让*RegistrationState*更改为 "*已注册*"。 你可以使用以下 cmdlet 来确认这一点。

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 响应应如下所示。

| 名                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | 已注册              |

3. *RegistrationState*更改为 "*已注册*" 后，运行以下 cmdlet。

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**启用规模集的应用程序运行状况监视**

规模集应对实例启用应用程序运行状况监视。 可以使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)来完成此操作。 一次只能启用其中一个。 应用程序运行状况扩展或负载均衡器探测对虚拟机实例上配置的应用程序终结点进行 ping 操作，以确定应用程序的运行状况状态。 此运行状况状态由规模集 orchestrator 用于监视实例运行状况，并在需要时执行修复。

**配置终结点以提供运行状况状态**

启用自动实例修复策略之前，请确保规模集实例具有配置的应用程序终结点，以发出应用程序运行状况状态。 当实例在此应用程序终结点上返回状态200（"正常"）时，该实例将被标记为 "正常"。 在所有其他情况下，实例被标记为 "不正常"，包括以下方案：

- 在虚拟机实例中未配置可提供应用程序运行状况状态的应用程序终结点时
- 当应用程序终结点配置不正确时
- 当应用程序终结点不可访问时

对于标记为 "不正常" 的实例，规模集会触发自动修复。 请确保在启用自动修复策略之前正确配置了应用程序终结点，以便在配置终结点时避免意外的实例修复。

**启用单个放置组**

此预览版目前仅适用于部署为单个放置组的规模集。 对于规模集，应将 " *singlePlacementGroup* " 属性设置为 " *true* " 以使用自动实例修复功能。 详细了解[放置组](./virtual-machine-scale-sets-placement-groups.md#placement-groups)。

**API 版本**

计算 API 版本2018-10-01 或更高版本支持自动修复策略。

**对资源或订阅的限制**

在此预览版中，在启用自动修复策略时，当前不支持对规模集进行资源或订阅移动。

**Service fabric 规模集限制**

Service fabric 规模集当前不支持此预览功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自动实例修复如何工作？

自动实例修复功能依赖于规模集中单个实例的运行状况监视。 规模集中的 VM 实例可以配置为使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)发出应用程序运行状况状态。 如果发现实例不正常，则规模集通过删除不正常的实例并创建一个新实例来替换它来执行修复操作。 可以使用*automaticRepairsPolicy*对象在虚拟机规模集模型中启用此功能。

### <a name="batching"></a>批处理

自动实例修复操作以批处理的顺序执行。 在任意给定时间，规模集中的实例数不能超过5%，只能通过自动修复策略进行修复。 如果在同一时间发现不正常，这有助于避免同时删除和重新创建大量实例。

### <a name="grace-period"></a>宽限期

当实例由于对规模集（例如重置映像、重新部署、更新等）执行 PUT、PATCH 或 POST 操作而经历状态更改操作时，只有在等待宽限期后，才会执行该实例上的任何修复操作。 宽限期是指允许实例返回到正常状态的时间量。 宽限期在状态更改完成后开始。 这有助于避免任何过早或意外的修复操作。 宽限期适用于规模集中的任何新创建的实例（包括作为修复操作的结果而创建的实例）。 宽限期以 ISO 8601 格式指定，可使用*gracePeriod*属性进行设置。 宽限期范围为30分钟到90分钟，默认值为30分钟。

自动实例修复过程如下所示：

1. [应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)ping 规模集中每个虚拟机内的应用程序终结点，以获取每个实例的应用程序运行状况状态。
2. 如果终结点使用状态200（"正常"）进行响应，则实例将被标记为 "正常"。 在所有其他情况下（包括终结点不可访问时），该实例将被标记为 "不正常"。
3. 如果发现实例不正常，则规模集会通过删除不正常的实例并创建一个新实例来替换它来触发修复操作。
4. 实例修复以批处理的顺序执行。 在任意给定时间，规模集中的总实例数不能超过5%。 如果规模集的实例数少于20个，则每次对一个不正常的实例进行修复。
5. 在修复规模集中的所有不正常实例之前，上述过程将继续。

## <a name="instance-protection-and-automatic-repairs"></a>实例保护和自动修复

如果规模集中的实例通过应用 "通过 *[规模集保护保护" 操作保护策略](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* 来保护，则不会对该实例执行自动修复。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>创建新规模集时启用自动修复策略

若要在创建新规模集时启用自动修复策略，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应为规模集实例正确配置应用程序终结点，以避免在配置终结点时触发意外修复。 对于新创建的规模集，仅在等待宽限期的持续时间后，才会执行任何实例修复。 若要在规模集中启用自动实例修复，请在虚拟机规模集模型中使用*automaticRepairsPolicy*对象。

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

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>更新现有规模集时启用自动修复策略

在现有规模集中启用自动修复策略之前，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应为规模集实例正确配置应用程序终结点，以避免在配置终结点时触发意外修复。 若要在规模集中启用自动实例修复，请在虚拟机规模集模型中使用*automaticRepairsPolicy*对象。

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

## <a name="troubleshoot"></a>故障排除

**启用自动修复策略失败**

如果在 "properties" 类型的对象上出现 "BadRequest" 错误，并显示消息 "找不到成员 ' automaticRepairsPolicy '"，请检查用于虚拟机规模集的 API 版本。 此功能需要 API 版本2018-10-01 或更高版本。

**即使启用策略，也不会修复实例**

实例可能处于宽限期。 这是在执行修复之前，在对实例进行任何状态更改后要等待的时间量。 这是为了避免任何过早或意外的修复。 完成实例的宽限期后，应执行修复操作。

**查看规模集实例的应用程序运行状况状态**

你可以使用虚拟机规模集中实例的[获取实例视图 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)来查看应用程序运行状况状态。 使用 Azure PowerShell，你可以将 cmdlet [AzVmssVM](/powershell/module/az.compute/get-azvmssvm)与 *-InstanceView*标志一起使用。 应用程序运行状况在属性*vmHealth*下提供。

## <a name="next-steps"></a>后续步骤

了解如何为规模集配置[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)。
