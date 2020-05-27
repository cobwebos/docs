---
title: 虚拟机规模集的邻近放置组预览版
description: 了解如何在 Azure 中为 Windows 虚拟机规模集创建和使用邻近放置组。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: 8e455d4d016f97a466838c07fc7af2422f72cc00
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727091"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>预览版：使用 PowerShell 创建和使用邻近放置组

为了使 VM 尽可能接近，以尽可能降低延迟，应该在[邻近放置组](co-location.md#preview-proximity-placement-groups)中部署规模集。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源的物理位置彼此接近。 邻近放置组适用于要求低延迟的工作负荷。

> [!IMPORTANT]
> 邻近放置组目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 预览版的邻近放置组在以下区域中不可用：日本东部、澳大利亚东部和印度中部。


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


## <a name="create-a-scale-set"></a>创建规模集

使用 [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) 创建规模集时，可使用 `-ProximityPlacementGroup $ppg.Id` 引用邻近放置组 ID，以便在邻近放置组中创建规模集。

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

可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) 查看放置组中的实例。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>后续步骤

还可以使用 [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) 创建邻近放置组。
