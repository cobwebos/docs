---
title: "在 Azure 中创建 VHD 快照 | Microsoft Docs"
description: "了解如何创建 Azure VM 的副本用作备份或用于解决问题。"
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 9f773a8dfe772864fc9fc437052ac766a87623d1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
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

请确保已安装最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令进行安装。

```
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](/powershell/azure/overview)。


1. 设置一些参数。 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. 获取要复制的 VHD 磁盘。

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. 创建快照配置。 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. 拍摄快照。

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
如果计划使用快照创建托管磁盘并将其附加到需要高性能的 VM，请使用 New-AzureRmSnapshot 命令中的参数 `-AccountType Premium_LRS`。 此参数将创建快照，以便将其存储为高级托管磁盘。 高级托管磁盘比标准托管磁盘费用高。 因此使用该参数前，请确保确实需要使用高级托管磁盘。

## <a name="next-steps"></a>后续步骤

通过从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 有关详细信息，请参阅[从快照创建 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) 示例。
