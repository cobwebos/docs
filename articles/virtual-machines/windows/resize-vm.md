---
title: 在 Azure 中使用 PowerShell 调整 Windows VM 的大小 | Microsoft Docs
description: 使用 Azure Powershell 调整在 Resource Manager 部署模型中创建的 Windows 虚拟机大小。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 8854f694ce067aaa80a159430a9f48440bcdd95a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701864"
---
# <a name="resize-a-windows-vm"></a>调整 Windows VM 大小

本文介绍如何使用 Azure Powershell 将虚拟机切换到不同的[虚拟机大小](sizes.md)。

完成创建虚拟机 (VM) 后，可以通过更改 VM 大小来扩展或缩减 VM。 在某些情况下，必须先解除分配 VM。 如果新大小在当前托管 VM 的硬件群集上不可用，则可能会出现这种情况。

如果虚拟机使用高级存储，请确保选择 **s** 版本的大小以获得高级存储支持。 例如，选择 Standard_E4**s**_v3，而不是 Standard_E4_v3。

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>调整不在可用性集中的 Windows VM 大小

设置一些变量。 将值替换为自己的信息。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出托管 VM 的硬件群集上可用的 VM 大小。 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果列出了所需大小，运行以下命令即可重设虚拟机大小。 如果未列出所需大小，请转到步骤 3。
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```

如果未列出所需大小，运行以下命令即可解除分配虚拟机、重设其大小，并重新启动虚拟机。 将 **<newVMsize>** 替换为所需大小。
   
```powershell
Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> 解除分配 VM 会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>调整可用性集中的 Windows VM 大小

如果可用性集中 VM 的新大小在当前托管 VM 的硬件群集上不可用，则需要解除分配可用性集中的所有 VM 来调整 VM 大小。 完成调整一个 VM 的大小后，可能还需要更新可用性集中其他 VM 的大小。 若要调整可用性集中 VM 的大小，请执行以下步骤。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

列出托管 VM 的硬件群集上可用的 VM 大小。 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

如果列出了所需大小，运行以下命令即可调整 VM 大小。 如果未列出所需大小，请转到下一部分。
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```
    
如果未列出所需大小，继续执行以下步骤即可解除分配可用性集中的所有 VM、重设 VM 大小，并重新启动它们。

停止可用性集中的所有 VM。
   
```powershell
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

调整可用性集中 VM 的大小并重新启动。
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>后续步骤

若要提高伸缩性，请运行多个 VM 实例并进行横向扩展。有关详细信息，请参阅[自动缩放虚拟机规模集中的 Windows 计算机](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)。

