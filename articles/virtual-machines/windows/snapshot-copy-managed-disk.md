---
title: "创建 Azure 托管磁盘的副本用于备份 | Microsoft Docs"
description: "了解如何创建 Azure 托管磁盘的副本以用于备份或解决磁盘问题。"
documentationcenter: 
author: cwatsonMSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 620c9223106c06cf6ea2b80fce87d81ea44d02ca
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>使用托管快照创建一个存储为 Azure 托管磁盘的 VHD 的副本
获取托管磁盘的快照以用于备份，或从快照创建托管磁盘，并将其附加到测试虚拟机进行故障排除。 托管快照是 VM 托管磁盘的完整时间点副本。 它创建 VHD 的只读副本，并且默认将其存储为标准托管磁盘。 有关托管磁盘的详细信息，请参阅 [Azure 托管磁盘概述](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

有关定价的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/managed-disks/)。 

## <a name="before-you-begin"></a>开始之前
如果使用 PowerShell，请确保使用最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令进行安装。

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)。

## <a name="copy-the-vhd-with-a-snapshot"></a>使用快照复制 VHD
使用 Azure 门户或 PowerShell 获取托管磁盘的快照。

### <a name="use-azure-portal-to-take-a-snapshot"></a>使用 Azure 门户拍摄快照 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 首先在左上角单击“新建”并搜索“快照”。
3. 在“快照”边栏选项卡中，单击“创建”。
4. 输入快照的“名称”。
5. 选择现有的[资源组](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或键入新资源组的名称。 
6. 选择 Azure 数据中心的位置。  
7. 对于**源磁盘**，选择要获取其快照的托管磁盘。
8. 选择用于存储快照的“帐户类型”。 建议使用 **Standard_LRS**，除非需要将其存储在高性能磁盘上。
9. 单击“创建” 。

### <a name="use-powershell-to-take-a-snapshot"></a>使用 PowerShell 拍摄快照
以下步骤演示如何使用 AzureRmSnapshot cmdlet<!--Add link to cmdlet when available--> 获取要复制的 VHD 磁盘、创建快照配置以及拍摄磁盘的快照。 

1. 设置一些参数。 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  替换参数值：
  -  “myResourceGroup”替换为 VM 的资源组。
  -  “southeastasia”替换为要存储托管快照的地理位置。 <!---How do you look these up? -->
  -  “ContosoMD_datadisk1”替换为想要复制的 VHD 磁盘的名称。
  -  “ContosoMD_datadisk1_snapshot1”替换为用于新快照的名称。

2. 获取要复制的 VHD 磁盘。

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. 创建快照配置。 

 ```powershell
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. 拍摄快照。

 ```powershell
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
如果计划使用快照创建托管磁盘并将其附加到需要高性能的 VM，请使用 New-AzureRmSnapshot 命令中的参数 `-AccountType Premium_LRS`。 此参数将创建快照，以便将其存储为高级托管磁盘。 高级托管磁盘比标准托管磁盘费用高。 因此使用该参数前，请确保确实需要使用高级托管磁盘。



