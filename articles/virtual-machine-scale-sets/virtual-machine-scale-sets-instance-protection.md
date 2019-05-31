---
title: 实例保护的 Azure 虚拟机规模集实例 |Microsoft Docs
description: 了解如何保护 Azure 虚拟机规模集实例从向内缩放和规模集的操作。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416547"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>实例保护的 Azure 虚拟机规模集实例 （预览版）
Azure 虚拟机规模集启用更好的灵活性，实现通过工作负荷[自动缩放](virtual-machine-scale-sets-autoscale-overview.md)，因此您的基础结构向外扩展时，可以配置，以及何时其规模，在。 规模集还使您能够集中管理、 配置和更新通过不同的 Vm 大量[升级策略](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)设置。 可以在规模集模型上配置更新和新的配置会自动应用到每个规模集实例如果已设置为自动或滚动升级策略。

如你的应用程序处理流量，但可以在想要缩放的其余部分以不同方式处理的特定实例的情况下将实例设置。 例如，某些实例规模集无法执行长时间运行的操作，而不希望这些实例以进行缩放-在操作完成之前。 您可能还专用规模集中执行规模集的其他成员比其他或不同任务的几个实例。 需要不使用规模集中的其他实例修改这些特殊 Vm。 实例保护提供了启用这些和其他应用程序方案的其他控件。

本文介绍如何将应用和使用规模集实例使用不同的实例保护功能。

> [!NOTE]
>实例保护目前处于公共预览状态。 没有参加过程需要使用如下所述的公共预览版功能。 实例保护预览版仅支持 api 版本 2019年-03-01 和使用托管的磁盘的规模集上。

## <a name="types-of-instance-protection"></a>类型的实例保护
规模集提供两种类型的实例保护功能：

-   **保护免受缩小**
    - 通过启用**protectFromScaleIn**刻度上的属性将实例设置
    - 可防止实例启动的自动缩放缩小
    - 用户启动的实例操作 （包括实例删除） 是**未被阻止**
    - 在规模集上启动的操作 （升级、 重置映像、 解除分配，等等） 是**未被阻止**

-   **保护从规模集操作**
    - 通过启用**protectFromScaleSetActions**刻度上的属性将实例设置
    - 可防止实例启动的自动缩放缩小
    - 实例可防止在规模集上启动的操作 （如升级时，重置映像、 解除分配，等等。）
    - 用户启动的实例操作 （包括实例删除） 是**未被阻止**
    - 完整的规模集的删除**未被阻止**

## <a name="protect-from-scale-in"></a>保护免受缩小
实例保护可应用到规模集实例后创建的实例。 保护应用和仅在修改[的实例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)而不是在[规模集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)。

有多种方法可以在下面的示例中所述的规模集实例应用保护，缩小。

### <a name="rest-api"></a>REST API

下面的示例将规模中保护应用于规模集中实例。

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
>使用 API 版本 2019年-03-01 及更高版本才支持实例保护

### <a name="azure-powershell"></a>Azure PowerShell

使用[更新 AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 将缩小保护应用到规模集实例。

下面的示例将规模中保护应用于规模集实例 id 0 中的实例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss 更新](/cli/azure/vmss#az-vmss-update)若要将规模中保护应用到规模集实例。

下面的示例将规模中保护应用于规模集实例 id 0 中的实例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>保护从规模集操作
实例保护可应用到规模集实例后创建的实例。 保护应用和仅在修改[的实例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)而不是在[规模集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)。

保护实例从规模集操作还可防止实例启动的自动缩放缩小。

有多种方式将应用缩放的操作保护上设置规模集实例下面的示例中所述。

### <a name="rest-api"></a>REST API

下面的示例应用到规模集中实例的规模集操作的保护。

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
>使用 API 版本 2019年-03-01 及更高版本，仅支持实例保护。</br>
保护实例从规模集操作还可防止实例启动的自动缩放缩小。 不能指定"protectFromScaleIn": false 时设置"protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

使用[更新 AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 将应用保护扩展到在规模集实例设置的操作。

下面的示例应用中具有实例 ID 0 的规模集实例从规模集操作的保护。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss 更新](/cli/azure/vmss#az-vmss-update)应用到规模集实例从规模集操作的保护。

下面的示例应用中具有实例 ID 0 的规模集实例从规模集操作的保护。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>故障排除
### <a name="no-protectionpolicy-on-scale-set-model"></a>在规模集模型没有 protectionPolicy
实例保护时才适用规模集实例上而不是在规模集模型。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>在规模集实例模型没有 protectionPolicy
默认情况下保护策略不是应用于实例时创建它。

您可以应用到规模集实例后，会创建实例的实例保护。

### <a name="not-able-to-apply-instance-protection"></a>不能将实例保护应用
使用 API 版本 2019年-03-01 及更高版本，仅支持实例保护。 检查所使用的 API 版本并根据需要更新。 您可能还需要更新到最新版本的 PowerShell 或 CLI。

## <a name="next-steps"></a>后续步骤
了解如何在虚拟机规模集上[部署应用程序](virtual-machine-scale-sets-deploy-app.md)。
