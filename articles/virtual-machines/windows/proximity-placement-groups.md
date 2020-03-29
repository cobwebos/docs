---
title: 电源外壳：使用接近放置组
description: 了解如何使用 Azure PowerShell 创建和使用邻近放置组。
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208519"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>使用 PowerShell 将 VM 部署到邻近放置组


要尽可能接近 VM，实现尽可能低的延迟，应将它们部署在[接近放置组中](co-location.md#proximity-placement-groups)。

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

在使用[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)创建`-ProximityPlacementGroup $ppg.Id`VM 时，在邻近放置组中创建 VM，用于引用接近放置组 ID。

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

您可以使用["获取-邻近放置组](/powershell/module/az.compute/get-azproximityplacementgroup)"在放置组中看到 VM。

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>将现有 VM 移动到邻近放置组

您还可以将现有 VM 添加到邻近放置组。 您需要首先停止\去分配 VM，然后更新 VM 并重新启动。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>将现有 VM 移出邻近放置组

要从邻近放置组中删除 VM，需要首先停止\取消分配 VM，然后更新 VM 并重新启动。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>可用性集
您还可以在邻近放置组中创建可用性集。 使用与`-ProximityPlacementGroup`[New-Az可用性设置](/powershell/module/az.compute/new-azavailabilityset)cmdlet 相同的参数来创建可用性集，并且在可用性集中创建的所有 VM 也将在同一接近放置组中创建。

要将现有可用性集添加到接近放置组，首先需要停止可用性集中的所有 VM。 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>将现有可用性集移动到邻近放置组

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>将现有可用性集移出邻近放置组

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>规模集

您还可以在邻近放置组中创建比例集。 使用与`-ProximityPlacementGroup`[New-AzVms](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss)相同的参数创建比例集，并将在同一邻近放置组中创建所有实例。


要向邻近放置组添加或删除现有比例集，首先需要停止比例集。 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>将现有比例集移动到接近放置组

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>将设置的现有比例移出邻近放置组

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

也可使用 [Azure CLI](../linux/proximity-placement-groups.md) 创建邻近放置组。
