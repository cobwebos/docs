---
title: 对 Windows Vm 使用邻近位置组 |Microsoft Docs
description: 了解如何在 Azure 中创建和使用 Windows 虚拟机的邻近性放置组。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 9e9b8dddf7f48fe672a37b0343d215e0735ed760
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302503"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>预览版：使用 PowerShell 将 Vm 部署到邻近位置组


若要尽可能降低 Vm, 请尽可能降低延迟, 然后将它们部署到[邻近的放置组](co-location.md#preview-proximity-placement-groups)。

邻近性放置组是一种逻辑分组, 用于确保 Azure 计算资源的物理位置彼此接近。 邻近性放置组适用于要求低延迟的工作负荷。

> [!IMPORTANT]
> 邻近性放置组目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 预览期间, 不会在这些区域中使用邻近位置组:**日本东部**、**澳大利亚东部**和**印度中部**。


## <a name="create-a-proximity-placement-group"></a>创建邻近放置组
使用[AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet 创建邻近位置组。 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>列出邻近位置组

您可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet 列出所有邻近位置组。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>创建 VM

使用`-ProximityPlacementGroup $ppg.Id` [new-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)创建 vm 时, 请使用引用邻近位置组 ID 在邻近位置组中创建 vm。

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)在放置组中查看 VM。

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>可用性集
你还可以在邻近布局组中创建可用性集。 将同一`-ProximityPlacementGroup`参数与[AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) cmdlet 配合使用来创建可用性集, 并且也将在同一邻近布局组中创建可用性集中创建的所有 vm。

## <a name="scale-sets"></a>规模集

你还可以在邻近布局组中创建规模集。 使用与`-ProximityPlacementGroup` [AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)相同的参数创建规模集, 所有实例都将在相同的邻近布局组中创建。

## <a name="next-steps"></a>后续步骤

你还可以使用[Azure CLI](../linux/proximity-placement-groups.md)来创建邻近位置组。
