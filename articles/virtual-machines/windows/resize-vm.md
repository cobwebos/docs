---
title: 调整 Azure 中 Windows VM 的大小
description: 更改用于 Azure 虚拟机的 VM 大小。
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: sizes
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 4860422fefb5a95fe41912b4898f02867f7b3aeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87832240"
---
# <a name="resize-a-windows-vm"></a>调整 Windows VM 的大小

本文介绍如何将 VM 切换到不同的 [VM 大小](../sizes.md)。

完成创建虚拟机 (VM) 后，可以通过更改 VM 大小来扩大或缩小 VM。 在某些情况下，必须先解除分配 VM。 如果新大小在当前托管 VM 的硬件群集上不可用，则可能会出现这种情况。

如果虚拟机使用高级存储，请确保选择 **s** 版本的大小以获得高级存储支持。 例如，选择 Standard_E4**s**_v3，而不是 Standard_E4_v3。

## <a name="use-the-portal"></a>使用门户

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在虚拟机所对应的页。
1. 在左侧菜单中，选择“大小”。
1. 从可用大小列表中选取新大小，然后选择“重设大小”。


如果虚拟机当前正在运行，更改其大小会导致其重启。 停止虚拟机可能会显示其他大小。

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>使用 PowerShell 重设不在可用性集中的 VM 的大小

设置一些变量。 将值替换为自己的信息。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出托管 VM 的硬件群集上可用的 VM 大小。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果列出了所需大小，运行以下命令即可重设虚拟机大小。 如果未列出所需大小，请转到步骤 3。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

如果未列出所需大小，运行以下命令即可解除分配虚拟机、重设其大小，并重新启动虚拟机。 将 **\<newVMsize>** 替换为所需大小。
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> 解除分配 VM 会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>使用 PowerShell 重设可用性集中的 VM 的大小

如果可用性集中 VM 的新大小在当前托管 VM 的硬件群集上不可用，则需要解除分配可用性集中的所有 VM 以调整 VM 大小。 已调整一个 VM 的大小后，可能还需要更新可用性集中其他 VM 的大小。 若要调整可用性集中 VM 的大小，请执行以下步骤。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出托管 VM 的硬件群集上可用的 VM 大小。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果列出了所需大小，请运行以下命令来调整 VM 的大小。 如果未列出所需大小，请转到下一部分。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
如果未列出所需大小，继续执行以下步骤即可解除分配可用性集中的所有 VM、重设 VM 大小，并重新启动它们。

停止可用性集中的所有 VM。
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

调整可用性集中 VM 的大小并重新启动 VM。
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

## <a name="next-steps"></a>后续步骤

若要提高可伸缩性，请运行多个 VM 实例并进行横向扩展。有关详细信息，请参阅 [自动缩放虚拟机规模集中的 Windows 计算机](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)。