---
title: 将托管磁盘的 VHD 导出/复制到其他区域的帐户中 (Windows) - PowerShell
description: Azure PowerShell 脚本示例 - 将托管磁盘的 VHD 导出/复制到相同或不同区域中的存储帐户
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: d76fda02f94f85126214de27d88079977824098d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320126"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell-windows"></a>使用 PowerShell 将托管磁盘的 VHD 导出/复制到不同区域中的存储帐户 (Windows)

此脚本将托管磁盘的 VHD 导出到不同区域中的存储帐户。 它首先生成托管磁盘的 SAS URI，然后使用该 SAS URI 将基础 VHD 复制到不同区域中的存储帐户。 使用此脚本将托管磁盘复制到另一区域以进行区域扩展。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令生成托管磁盘的 SAS URI，然后使用该 SAS URI 将基础 VHD 复制到存储帐户。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Grant-AzDiskAccess](/powershell/module/az.compute/grant-azdiskaccess) | 为托管磁盘生成 SAS URI，该 SAS URI 用于将基础 VHD 复制到存储帐户。 |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | 使用帐户名和密钥创建存储帐户上下文。 此上下文可用于对存储帐户执行读/写操作。 |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | 将快照的基础 VHD 复制到存储帐户 |

## <a name="next-steps"></a>后续步骤

[从 VHD 创建托管磁盘](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[从托管磁盘创建虚拟机](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
