---
title: Azure 虚拟机规模集实例的实例保护
description: 了解如何通过扩展和规模集操作来保护 Azure 虚拟机规模集实例。
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254113"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 虚拟机规模集实例的实例保护

Azure 虚拟机规模集通过[自动缩放](virtual-machine-scale-sets-autoscale-overview.md)为工作负荷提供更好的弹性，因此，你可以配置你的基础结构何时向外扩展以及何时扩展。 规模集还允许通过不同的[升级策略](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)设置集中管理、配置和更新大量 vm。 如果已将升级策略设置为 "自动" 或 "滚动"，则可以在规模集模型上配置更新，并将新配置自动应用于每个规模集实例。

当你的应用程序处理流量时，可能会出现以下情况：你需要将特定实例视为不同于规模集实例的其余部分。 例如，规模集中的某些实例可能正在执行长时间运行的操作，并且你不希望在操作完成之前对这些实例进行扩展。 您还可以在规模集中专门使用几个实例来执行其他任务或不同于规模集的其他成员。 需要将这些 "特殊" Vm 替换为规模集中的其他实例。 实例保护提供了其他控件来实现应用程序的这些方案和其他方案。

本文介绍如何将不同的实例保护功能应用于规模集实例并使用这些功能。

## <a name="types-of-instance-protection"></a>实例保护类型
规模集提供了两种类型的实例保护功能：

-   **防止缩小**
    - 通过规模集实例的**protectFromScaleIn**属性启用
    - 保护启动的自动缩放实例
    - 用户启动的实例操作（包括实例删除）**未被阻止**
    - **不会阻止**在规模集上启动的操作（升级、重置映像、释放等）

-   **防止规模集操作**
    - 通过规模集实例的**protectFromScaleSetActions**属性启用
    - 保护启动的自动缩放实例
    - 针对规模集上启动的操作（如升级、重置映像、释放等）保护实例
    - 用户启动的实例操作（包括实例删除）**未被阻止**
    - **不会阻止**删除整个规模集

## <a name="protect-from-scale-in"></a>防止缩小
实例保护可以在创建实例后应用于规模集实例。 仅对[实例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)而不是[规模集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)应用和修改保护。

可以通过多种方法在规模集实例上应用扩展保护，如以下示例中所述。

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户向规模集中的实例应用 "缩小" 保护。 不能一次调整多个实例。 为要保护的每个实例重复这些步骤。
 
1. 请参阅现有虚拟机规模集。
1. 从左侧菜单的 "**设置**" 下，选择 "**实例**"。
1. 选择要保护的实例的名称。
1. 选择 "**保护策略**" 选项卡。
1. 在 "**保护策略**" 边栏选项卡中，选择 "**从刻度保护**" 选项。
1. 选择“保存”。 

### <a name="rest-api"></a>REST API

下面的示例向规模集中的实例应用 "缩小保护"。

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
>仅 API 版本2019-03-01 及更高版本支持实例保护

### <a name="azure-powershell"></a>Azure PowerShell

使用[AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 将向规模集实例应用扩展保护。

下面的示例对规模集内实例 ID 为0的实例应用了缩小保护。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss update](/cli/azure/vmss#az-vmss-update)将向规模集实例应用扩展保护。

下面的示例对规模集内实例 ID 为0的实例应用了缩小保护。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>防止规模集操作
实例保护可以在创建实例后应用于规模集实例。 仅对[实例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)而不是[规模集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)应用和修改保护。

通过规模集操作保护实例，还可以防止实例自动缩放启动的缩放。

在规模集实例上应用规模集操作保护有多种方法，如以下示例中所述。

### <a name="azure-portal"></a>Azure 门户

可以通过 Azure 门户向规模集中的实例应用规模集操作保护。 不能一次调整多个实例。 为要保护的每个实例重复这些步骤。
 
1. 请参阅现有虚拟机规模集。
1. 从左侧菜单的 "**设置**" 下，选择 "**实例**"。
1. 选择要保护的实例的名称。
1. 选择 "**保护策略**" 选项卡。
1. 在 "**保护策略**" 边栏选项卡中，选择 "**防止规模集操作**" 选项。
1. 选择“保存”。 

### <a name="rest-api"></a>REST API

下面的示例将对规模集操作的保护应用于规模集中的实例。

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
>仅 API 版本2019-03-01 及更高版本支持实例保护。</br>
通过规模集操作保护实例，还可以防止实例自动缩放启动的缩放。 设置 "protectFromScaleSetActions" 时，不能指定 "protectFromScaleIn"： false

### <a name="azure-powershell"></a>Azure PowerShell

使用[AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 将规模集操作的保护应用于规模集实例。

下面的示例将对规模集操作的保护应用于规模集中实例 ID 为0的实例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss update](/cli/azure/vmss#az-vmss-update)将对规模集操作的保护应用于规模集实例。

下面的示例将对规模集操作的保护应用于规模集中实例 ID 为0的实例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>故障排除
### <a name="no-protectionpolicy-on-scale-set-model"></a>规模集模型上没有 protectionPolicy
实例保护仅适用于规模集实例，而不适用于规模集模型。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>规模集实例模型上没有 protectionPolicy
默认情况下，当创建实例时，保护策略不会应用于该实例。

创建实例后，可以将实例保护应用于规模集实例。

### <a name="not-able-to-apply-instance-protection"></a>无法应用实例保护
仅 API 版本2019-03-01 及更高版本支持实例保护。 检查正在使用的 API 版本，并根据需要进行更新。 你可能还需要将 PowerShell 或 CLI 更新到最新版本。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
