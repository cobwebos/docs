---
title: 使用 PowerShell 更换 Azure VM 的 OS 磁盘 | Microsoft Docs
description: 使用 PowerShell 更改 Azure 虚拟机使用的操作系统磁盘。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: b213db38dade06e5015227494fa77b2f465ba1d9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089047"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>使用 PowerShell 更改 Azure VM 使用的 OS 磁盘

如果当前具备 VM，但希望将磁盘更换为备份磁盘或其他 OS 磁盘，可使用 Azure PowerShell 更换 OS 磁盘。 无需删除和重新创建 VM。 甚至可在另一资源组中使用托管磁盘，只要该磁盘尚未使用。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

需要停止/取消分配 VM，然后才可将该托管磁盘的资源 ID 替换为其他托管磁盘的资源 ID。

请确保 VM 大小和存储类型与要附加的磁盘兼容。 例如，如果要使用的磁盘位于高级存储中，则 VM 需要能使用高级存储（如 DS 系列大小）。 

使用 [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) 获取资源组中的磁盘列表

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
如果知道要使用的磁盘的名称，请将其设置为该 VM 的 OS 磁盘。 此示例停止/取消分配名为 myVM 的 VM，并将名为 newDisk 的磁盘分配为新的 OS 磁盘。 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**后续步骤**

要创建磁盘副本，请参阅[拍摄磁盘快照](snapshot-copy-managed-disk.md)。