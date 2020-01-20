---
title: 虚拟机规模集的邻近位置组预览
description: 了解如何在 Azure 中为 Windows 虚拟机规模集创建和使用邻近放置组。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273622"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>预览：使用 PowerShell 创建和使用邻近感应布局组

要尽可能地降低 Vm，实现尽可能低的延迟，应将规模集部署在[邻近的放置组](co-location.md#preview-proximity-placement-groups)中。

邻近性放置组是一种逻辑分组，用于确保 Azure 计算资源的物理位置彼此接近。 邻近性放置组适用于要求低延迟的工作负荷。

> [!IMPORTANT]
> 邻近性放置组目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 预览期间，不会在这些区域中使用邻近位置组：**日本东部**、**澳大利亚东部**和**印度中部**。


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


## <a name="create-a-scale-set"></a>创建规模集

当使用 [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) 来创建规模集时, 请使用 `-ProximityPlacementGroup $ppg.Id` 引用邻近位置组 ID 在邻近位置组中创建一个规模。

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

可以使用[AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)在放置组中查看实例。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>后续步骤

你还可以使用[Azure CLI](../virtual-machines/linux/proximity-placement-groups.md)来创建邻近位置组。
