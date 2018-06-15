---
title: 在 Azure 中创建 VHD 快照 | Microsoft Docs
description: 了解如何创建 Azure VM 的副本用作备份或用于解决问题。
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 7d45fd749fea4036d944d740541d8b8607553835
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658149"
---
# <a name="create-a-snapshot"></a>创建快照

创建 OS 或数据磁盘 VHD 的快照作为备份，或用于解决 VM 问题。 快照是 VHD 的完整只读副本。 

## <a name="use-azure-portal-to-take-a-snapshot"></a>使用 Azure 门户拍摄快照 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 首先在左上角单击“创建资源”并搜索“快照”。
3. 在“快照”边栏选项卡中，单击“创建”。
4. 输入快照的“名称”。
5. 选择现有的[资源组](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或键入新资源组的名称。 
6. 选择 Azure 数据中心的位置。  
7. 对于**源磁盘**，选择要获取其快照的托管磁盘。
8. 选择用于存储快照的“帐户类型”。 建议使用 **Standard_LRS**，除非需要将其存储在高性能磁盘上。
9. 单击“创建”。

## <a name="use-powershell-to-take-a-snapshot"></a>使用 PowerShell 拍摄快照

以下步骤演示如何使用 [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet 获取要复制的 VHD 磁盘、创建快照配置以及创建磁盘的快照。 

开始前，请确保具有最新版本的 AzureRM.Compute PowerShell 模块。 本文需要 5.7.0 版本或更高版本的 AzureRM 模块。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。

设置一些参数。 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

获取 VM。

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

创建快照配置。 在此示例中，我们将创建 OS 磁盘的快照。

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> 如果希望将快照存储在具有区域复原能力的存储中，需要在支持[可用性区域](../../availability-zones/az-overview.md)的区域中创建该快照并包括 `-SkuName Standard_ZRS` 参数。   

   
拍摄快照。

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>后续步骤

通过从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 有关详细信息，请参阅[从快照创建 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) 示例。
