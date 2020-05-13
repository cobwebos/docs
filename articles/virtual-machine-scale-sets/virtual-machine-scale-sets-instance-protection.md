---
title: 针对 Azure 虚拟机规模集实例的实例保护
description: 了解如何保护 Azure 虚拟机规模集实例，防止对其执行横向缩减操作和规模集操作。
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8c4944da8ffcaa75e6448483918a29809c32830b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124051"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>针对 Azure 虚拟机规模集实例的实例保护

Azure 虚拟机规模集可以通过[自动缩放](virtual-machine-scale-sets-autoscale-overview.md)提高工作负荷的弹性，让你可以配置何时对基础结构进行横向扩展，何时对其进行横向缩减。 规模集还可以让你通过不同的[升级策略](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)设置集中管理、配置和更新大量 VM。 可以针对规模集模型配置更新，此时如果已将升级策略设置为“自动”或“滚动”，则新配置会自动应用到每个规模集实例。

当应用程序处理流量时，有时可能需要对特定规模集实例进行不同于其他实例的处理。 例如，规模集中的某些实例可能正在执行长时间运行的操作，需要在这些操作完成后再横向缩减这些实例。 另外，可能还需要让规模集中的一些实例专门执行其他任务或不同于规模集的其他成员的任务。 这些“特殊的”VM 不能与规模集中的其他实例一起修改。 实例保护提供的其他控制可以为应用程序实现这些方案和其他方案。

本文介绍如何对规模集实例应用和使用不同的实例保护功能。

## <a name="types-of-instance-protection"></a>实例保护类型
规模集提供两类实例保护功能：

-   **防止进行横向缩减**
    - 通过规模集实例上的 **protectFromScaleIn** 属性实现
    - 防止对实例进行通过自动缩放启动的横向缩减
    - **不阻止**用户启动的实例操作（包括实例删除）
    - **不阻止**在规模集上启动的操作（升级、重置映像、解除分配等）

-   **防止进行规模集操作**
    - 通过规模集实例上的 **protectFromScaleSetActions** 属性实现
    - 防止对实例进行通过自动缩放启动的横向缩减
    - 防止对实例执行在规模集上启动的操作（例如升级、重置映像、解除分配等）
    - **不阻止**用户启动的实例操作（包括实例删除）
    - **不阻止**删除整个规模集

## <a name="protect-from-scale-in"></a>防止进行横向缩减
可以在规模集实例创建以后将实例保护应用到这些实例。 只能在[实例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)上应用和修改保护，不能在[规模集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上进行。

可以通过多种方式将横向缩减保护应用到规模集实例，详见以下示例。

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户对规模集中的实例应用横向缩减保护。 不能一次调整多个实例。 对要保护的每个实例重复以下步骤。
 
1. 转到现有虚拟机规模集。
1. 从左侧菜单的“设置”  下选择“实例”  。
1. 选择要保护的实例的名称。
1. 选择“保护策略”  选项卡。
1. 在“保护策略”  边栏选项卡中，选择“防止进行横向缩减”  选项。
1. 选择“保存”。  

### <a name="rest-api"></a>REST API

以下示例将横向缩减保护应用到规模集中的实例。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>只有 API 2019-03-01 及更高版本支持实例保护

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 将横向缩减保护应用到规模集实例。

以下示例将横向缩减保护应用到规模集中实例 ID 为 0 的实例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用 [az vmss update](/cli/azure/vmss#az-vmss-update) 将横向缩减保护应用到规模集实例。

以下示例将横向缩减保护应用到规模集中实例 ID 为 0 的实例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>防止进行规模集操作
可以在规模集实例创建以后将实例保护应用到这些实例。 只能在[实例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)上应用和修改保护，不能在[规模集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上进行。

防止对实例进行规模集操作时，也会防止对实例进行通过自动缩放启动的横向缩减。

可以通过多种方式将规模集操作保护应用到规模集实例，详见以下示例。

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户将“使其不受规模集操作的影响”应用到规模集中的一个实例。 不能一次调整多个实例。 对要保护的每个实例重复以下步骤。
 
1. 转到现有虚拟机规模集。
1. 从左侧菜单的“设置”  下选择“实例”  。
1. 选择要保护的实例的名称。
1. 选择“保护策略”  选项卡。
1. 在“保护策略”  边栏选项卡中，选择“使其不受规模集操作的影响”  选项。
1. 选择“保存”。  

### <a name="rest-api"></a>REST API

以下示例将规模集操作保护应用到规模集中的实例。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>只有 API 2019-03-01 及更高版本支持实例保护。</br>
防止对实例进行规模集操作时，也会防止对实例进行通过自动缩放启动的横向缩减。 设置 "protectFromScaleSetActions": true 时，不能指定 "protectFromScaleIn": false

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 将规模集操作保护应用到规模集实例。

以下示例将规模集操作保护应用到规模集中实例 ID 为 0 的实例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用 [az vmss update](/cli/azure/vmss#az-vmss-update) 将规模集操作保护应用到规模集实例。

以下示例将规模集操作保护应用到规模集中实例 ID 为 0 的实例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>故障排除
### <a name="no-protectionpolicy-on-scale-set-model"></a>不能在规模集模型上设置 protectionPolicy
只能在规模集实例上应用实例保护，不能在规模集模型上应用。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>不能在规模集实例模型上设置 protectionPolicy
默认情况下，保护策略不会在实例创建时应用到实例。

可以在规模集实例创建以后将实例保护应用到这些实例。

### <a name="not-able-to-apply-instance-protection"></a>不能应用实例保护
只有 API 2019-03-01 及更高版本支持实例保护。 请检查所使用的 API 版本和更新是否符合要求。 可能还需要将 PowerShell 或 CLI 更新到最新版本。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
