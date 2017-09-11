---
title: "Azure PowerShell 脚本示例 - 将快照作为 VHD 导出/复制到不同区域中的存储帐户 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 将快照作为 VHD 导出/复制到不同区域中的存储帐户"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a6bd0686842282ccd7ce0c31bb0152beb30bea66
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>使用 PowerShell 将托管快照作为 VHD 导出/复制到不同区域中的存储帐户

此脚本将托管快照导出到不同区域中的存储帐户。 它首先生成快照的 SAS URI，然后使用该 SAS URI 将快照复制到不同区域中的存储帐户。 可以使用此脚本在不同区域中维护托管磁盘的副本以备用于灾难恢复。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "复制快照")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令生成托管快照的 SAS URI 并使用该 SAS URI 将快照复制到一个存储帐户。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Grant-AzureRmSnapshotAccess](/powershell/module/azurerm.compute/New-AzureRmDisk) | 生成快照的 SAS URI，该 SAS URI 用来将快照复制到存储帐户。 |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | 使用帐户名称和密钥创建存储帐户上下文。 可以使用此上下文对存储帐户执行读取/写入操作。 |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | 将快照的基础 VHD 复制到存储帐户 |

## <a name="next-steps"></a>后续步骤

[基于 VHD 创建托管磁盘](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[基于托管磁盘创建虚拟机](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../../app-service-web/app-service-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
