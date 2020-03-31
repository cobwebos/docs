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
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274809"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>预览：Azure 虚拟机缩放集的自动实例修复

为 Azure 虚拟机扩展集启用自动实例修复有助于通过维护一组正常实例实现应用程序的高可用性。 如果发现比例集中的实例与[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测器](../load-balancer/load-balancer-custom-probe-overview.md)报告的那样不正常，则此功能通过删除不正常的实例并创建新实例来替换它，自动执行实例修复。

> [!NOTE]
> 此预览版功能不附带服务级别协议，不建议将其用于生产工作负荷。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自动实例修复的要求

**选择加入自动实例修复预览**

使用 REST API 或 Azure PowerShell 选择加入自动实例修复预览。 这些步骤将注册预览功能的订阅。 请注意，这只是使用此功能所需的一次性设置。 如果您的订阅已注册用于自动实例修复预览，则无需再次注册。 

使用 REST API 

1. 使用功能注册功能[- 注册](/rest/api/resources/features/register) 

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

2. 等待几分钟，*让国家*更改为*注册*。 您可以使用以下 API 来确认这一点。

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

3. 一旦*国家*更改为*注册*，然后运行以下内容。

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

使用 Azure PowerShell

1. 使用 cmdlet[寄存器-AzureRm 资源提供程序](/powershell/module/azurerm.resources/register-azurermresourceprovider)注册该功能，后跟[注册 -AzureRm 提供程序功能](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. 等待几分钟，注册*状态*更改为*注册*。 您可以使用以下 cmdlet 来确认这一点。

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 答复应如下。

| 功能名称                           | ProviderName            | 注册国       |
|---------------------------------------|-------------------------|-------------------------|
| 修复VMScale设置实例预览      | Microsoft.Compute       | 已注册              |

3. 一旦*注册状态*更改为*已注册*，然后运行以下 cmdlet。

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**为规模集启用应用程序运行状况监视**

规模集应启用实例的应用程序运行状况监视。 这可以使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测器](../load-balancer/load-balancer-custom-probe-overview.md)来完成。 一次只能启用其中一个。 应用程序运行状况扩展或负载均衡器探测在虚拟机实例上配置的应用程序终结点以确定应用程序运行状况状态。 此运行状况由规模集协调器用于监视实例运行状况并在需要时执行修复。

**配置终结点以提供运行状况**

在启用自动实例修复策略之前，请确保缩放集实例已配置应用程序终结点以发出应用程序运行状况状态。 当实例在此应用程序终结点上返回状态 200 （OK） 时，该实例将标记为"正常"。 在所有其他情况下，实例标记为"不正常"，包括以下方案：

- 当虚拟机实例中没有配置应用程序终结点以提供应用程序运行状况时
- 当应用程序终结点配置不正确时
- 无法访问应用程序终结点时

对于标记为"不正常"的实例，自动修复由比例集触发。 在启用自动修复策略之前，请确保应用程序终结点配置正确，以避免在配置终结点时意外进行实例修复。

**启用单个放置组**

此预览当前仅适用于作为单个放置组部署的规模集。 属性*单放置组*应设置为*true，* 以便将缩放设置为使用自动实例修复功能。 了解有关[放置组](./virtual-machine-scale-sets-placement-groups.md#placement-groups)的更多。

**API 版本**

计算 API 版本 2018-10-01 或更高版本支持自动修复策略。

**对资源或订阅移动的限制**

作为此预览的一部分，启用自动修复策略时，缩放集当前不支持资源或订阅移动。

**对服务结构规模集的限制**

服务结构缩放集当前不支持此预览功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自动实例修复如何工作？

自动实例修复功能依赖于对规模集中的各个实例的运行状况监视。 缩放集中的 VM 实例可以配置为使用[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测器](../load-balancer/load-balancer-custom-probe-overview.md)发出应用程序运行状况。 如果发现实例不正常，则缩放集通过删除不正常的实例并创建新实例来替换它来执行修复操作。 此功能可以使用*自动修复策略*对象在虚拟机缩放集模型中启用。

### <a name="batching"></a>批处理

自动实例修复操作分批执行。 在任意给定时间，通过自动修复策略修复比例集中的实例不超过 5%。 这有助于避免同时删除和重新创建大量实例（如果同时发现不正常）。

### <a name="grace-period"></a>宽限期

当实例由于在规模集上执行的 PUT、PATCH 或 POST 操作（例如重新映像、重新部署、更新等）而通过状态更改操作时，则该实例上的任何修复操作仅在等待宽限期后执行。 宽限期是允许实例返回到正常状态的时间量。 宽限期在状态更改完成后开始。 这有助于避免任何过早或意外的维修操作。 对于比例集中中任何新创建的实例（包括由于修复操作而创建的实例），宽限期都适用。 宽限期以 ISO 8601 格式以分钟为单位指定，可以使用属性 *"自动修复策略.gracePeriod"* 进行设置。 宽限期的范围为 30 分钟到 90 分钟，默认值为 30 分钟。

自动实例修复过程的工作方式如下：

1. [应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)在规模集中的每个虚拟机内对应用程序终结点进行 ping，以获取每个实例的应用程序运行状况状态。
2. 如果终结点响应状态为 200 （OK），则实例将标记为"正常"。 在所有其他情况下（包括终结点无法访问），实例标记为"不正常"。
3. 当发现实例不正常时，缩放集通过删除不正常的实例并创建新实例来替换它来触发修复操作。
4. 实例修复分批执行。 在任何给定时间，将修复规模集中总实例的 5%。 如果比例集的实例少于 20 个实例，则一次为一个不正常的实例执行修复。
5. 上述过程将继续，直到修复比例集中的所有不正常实例。

## <a name="instance-protection-and-automatic-repairs"></a>实例保护和自动修复

如果比例集中的实例通过应用*["保护从比例集操作"保护策略](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* 进行保护，则不会对该实例执行自动修复。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>创建新比例集时启用自动修复策略

对于在创建新比例集时启用自动修复策略，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应正确配置应用程序终结点以用于缩放集实例，以避免在配置终结点时触发意外修复。 对于新创建的缩放集，任何实例修复仅在等待宽限期后执行。 要在比例集中启用自动实例修复，请使用虚拟机缩放集模型中的*自动修复策略*对象。

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

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>更新现有规模集时启用自动修复策略

在现有比例集中启用自动修复策略之前，请确保满足选择加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 应正确配置应用程序终结点以用于缩放集实例，以避免在配置终结点时触发意外修复。 要在比例集中启用自动实例修复，请使用虚拟机缩放集模型中的*自动修复策略*对象。

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

## <a name="troubleshoot"></a>疑难解答

**无法启用自动修复策略**

如果收到"BadRequest"错误，其中显示一条消息，指出"在类型'属性'的对象上找不到成员'自动修复策略'"，请检查用于虚拟机规模集的 API 版本。 此功能需要 API 版本 2018-10-01 或更高版本。

**即使启用策略，实例也不会修复**

实例可能处于宽限期。 这是执行修复之前实例上任何状态更改后等待的时间量。 这是为了避免任何过早或意外的维修。 实例的宽限期完成后，应执行修复操作。

**查看缩放集实例的应用程序运行状况**

您可以将[获取实例视图 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)用于虚拟机规模集中的实例以查看应用程序运行状况状态。 使用 Azure PowerShell，可以将 cmdlet [Get-AzVmsVM](/powershell/module/az.compute/get-azvmssvm)与 *-实例视图*标志一起使用。 应用程序运行状况状态在属性*vmHealth*下提供。

## <a name="next-steps"></a>后续步骤

了解如何为规模集配置[应用程序运行状况扩展](./virtual-machine-scale-sets-health-extension.md)或[负载平衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)。
