---
title: 在 Azure 中使用 PowerShell 调整 Windows VM 的大小 | Microsoft Docs
description: 使用 Azure Powershell 调整在 Resource Manager 部署模型中创建的 Windows 虚拟机大小。
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 09a20892770ada7c67c43c20f3c4f36b5a76f09d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="resize-a-windows-vm"></a>调整 Windows VM 大小
本文介绍如何使用 Azure Powershell 调整在 Resource Manager 部署模型中创建的 Windows VM 大小。

完成创建虚拟机 (VM) 后，可以通过更改 VM 大小来扩展或缩减 VM。 在某些情况下，必须先解除分配 VM。 如果新大小在当前托管 VM 的硬件群集上不可用，则可能会出现这种情况。

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>调整不在可用性集中的 Windows VM 大小
1. 列出托管 VM 的硬件群集上可用的 VM 大小。 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. 如果列出了所需大小，运行以下命令即可调整 VM 大小。 如果未列出所需大小，请转到步骤 3。
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. 如果未列出所需大小，运行以下命令即可解除分配 VM、调整其大小，并重新启动 VM。
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -Name $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> 解除分配 VM 会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>调整可用性集中的 Windows VM 大小
如果可用性集中 VM 的新大小在当前托管 VM 的硬件群集上不可用，则需要解除分配可用性集中的所有 VM 来调整 VM 大小。 完成调整一个 VM 的大小后，可能还需要更新可用性集中其他 VM 的大小。 若要调整可用性集中 VM 的大小，请执行以下步骤。

1. 列出托管 VM 的硬件群集上可用的 VM 大小。
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. 如果列出了所需大小，运行以下命令即可调整 VM 大小。 如果未列出所需大小，请转到步骤 3。
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. 如果未列出所需大小，继续执行以下步骤即可解除分配可用性集中的所有 VM、调整 VM 大小，并重新启动它们。
4. 停止可用性集中的所有 VM。
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. 调整可用性集中 VM 的大小并重新启动。
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>后续步骤
* 若要提高伸缩性，请运行多个 VM 实例并进行横向扩展。有关详细信息，请参阅[自动缩放虚拟机规模集中的 Windows 计算机](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)。

