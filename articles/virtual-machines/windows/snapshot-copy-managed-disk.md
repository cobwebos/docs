---
title: 在 Azure 中创建 VHD 的快照
description: 了解如何创建 Azure VM 的副本用作备份或用于解决问题。
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b564e20ca8aa5acd7fbd4ea69ac2b1cd72e66d5e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075341"
---
# <a name="create-a-snapshot"></a>创建快照

快照是虚拟硬盘 (VHD) 的完整只读副本。 可以创建 OS 或数据磁盘 VHD 的快照以用作备份，或用于解决虚拟机 (VM) 问题。

如果要使用快照创建新的 VM，建议在拍摄快照前彻底关闭 VM，以清除正在运行的所有进程。

## <a name="use-the-azure-portal"></a>使用 Azure 门户 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“创建资源”，然后搜索并选择“快照”。
3. 在“快照”窗口，选择“创建”。 此时将显示“创建快照”窗口。
4. 输入快照的“名称”。
5. 选择现有的[资源组](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或键入新资源组的名称。 
6. 选择 Azure 数据中心的**位置**。  
7. 对于**源磁盘**，选择要获取其快照的托管磁盘。
8. 选择用于存储快照的“帐户类型”。 选择“Standard_HDD”，除非需要将快照存储在高性能磁盘上。
9. 选择“创建”。

## <a name="use-powershell"></a>使用 PowerShell

以下步骤演示如何使用 [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet 复制 VHD 磁盘、创建快照配置以及创建磁盘的快照。 

 

1. 设置一些参数： 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. 获取 VM：

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. 创建快照配置。 该示例中，此快照是 OS 磁盘的快照：

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > 如果希望将快照存储在具有区域复原能力的存储中，请在支持[可用性区域](../../availability-zones/az-overview.md)的区域中创建快照并将包含 `-SkuName Standard_ZRS` 参数。   
   
4. 拍摄快照：

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>后续步骤

通过从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 有关详细信息，请参阅[使用 PowerShell 从快照创建 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) 中的示例。
