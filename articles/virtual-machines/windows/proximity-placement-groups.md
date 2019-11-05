---
title: 对 Windows Vm 使用邻近位置组
description: 了解如何在 Azure 中创建和使用 Windows 虚拟机的邻近性放置组。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171218"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>使用 PowerShell 将 Vm 部署到邻近位置组


若要尽可能降低 Vm，请尽可能降低延迟，然后将它们部署到[邻近的放置组](co-location.md#proximity-placement-groups)。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


## <a name="create-a-proximity-placement-group"></a>创建邻近放置组
使用 [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet 创建邻近放置组。 

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

## <a name="list-proximity-placement-groups"></a>列出邻近放置组

可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet 列出所有邻近放置组。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>创建 VM

使用[new-azvm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)创建 vm 时，请使用 `-ProximityPlacementGroup $ppg.Id` 引用邻近位置组 ID 来在邻近位置组中创建 vm。

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
你还可以在邻近布局组中创建可用性集。 使用与[AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) cmdlet 相同的 `-ProximityPlacementGroup` 参数来创建可用性集，并且也将在同一邻近布局组中创建可用性集中创建的所有 vm。

## <a name="scale-sets"></a>规模集

你还可以在邻近布局组中创建规模集。 使用与[AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)相同的 `-ProximityPlacementGroup` 参数来创建规模集，并且所有实例都将在相同的邻近布局组中创建。

## <a name="next-steps"></a>后续步骤

也可使用 [Azure CLI](../linux/proximity-placement-groups.md) 创建邻近放置组。
