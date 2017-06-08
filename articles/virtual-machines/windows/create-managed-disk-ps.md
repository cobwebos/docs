---
title: "从 Azure 中的 VHD 创建托管磁盘 | Microsoft Docs"
description: "使用 Resource Manager 部署模型从当前位于 Azure 存储帐户中的 VHD 创建托管磁盘。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 46b7b2a6fc3413e798cbad3e35837d1d52b63a2d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>从存储帐户中的非托管磁盘创建托管磁盘

可以从当前位于 Azure 存储帐户中的现有数据磁盘或操作系统磁盘创建托管磁盘。 也可以创建可用作 VM 的新数据磁盘的空磁盘。 

## <a name="before-you-begin"></a>开始之前
如果使用 PowerShell，请确保使用最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令进行安装。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](/powershell/azure/overview)。


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>从 Azure 存储帐户中的 VHD 创建托管磁盘

在此示例中，从 VHD 创建一个磁盘作为托管磁盘，并将其分配给参数 **$disk1** 以供稍后使用。 

托管磁盘将在**美国西部**地区名为 **myResourceGroup** 的资源组中创建。 该磁盘命名为 **myDisk**，它将从名为 **myDisk.vhd** 的 VHD 文件创建，该文件在之前已上传到名为 **mystorageaccount** 的存储帐户。 托管磁盘将在高级本地冗余存储 (LRS) 中创建。 StandardLRS 和 PremiumLRS 是仅有的可用于托管磁盘的 **-AccountType** 选项。 

1.  设置一些参数

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. 创建数据磁盘。 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>创建空数据磁盘作为管理磁盘

在此示例中，创建一个空数据磁盘作为托管磁盘，并将其分配给参数 **$dataDisk2** 以供稍后使用。 将空数据磁盘附加到正在运行的 VM 且登录到该 VM 后，需要使用 diskmgmt.msc 或[远程使用 WinRM 和一个脚本](attach-disk-ps.md#initialize-the-disk)对其进行初始化。

空数据磁盘将在**美国中西部**地区名为 **myResourceGroup** 的资源组中创建。 该磁盘将命名为 **myEmptyDataDisk**。 空磁盘将在高级本地冗余存储 (LRS) 中创建。 StandardLRS 和 PremiumLRS 是仅有的可用于托管磁盘的 **-AccountType** 选项。

此示例中的磁盘大小为 128 GB，但应选择一个大小，以满足在 VM 上运行的任何应用程序的需要。

1.  设置一些参数

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. 创建数据磁盘。
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>后续步骤    
- 如果已有 VM，可以[附加数据磁盘](attach-disk-portal.md)。

