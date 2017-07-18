---
title: "Azure PowerShell 脚本示例 - 从相同或不同订阅的存储帐户中的 VHD 文件创建托管磁盘 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 基于 VHD 文件在同一或不同订阅中的存储帐户中创建托管磁盘"
services: managed-disks
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 079ab69da3c2cb6cc38f0a766ffeb47ed84c90a3
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>使用 PowerShell 基于 VHD 文件在同一或不同订阅中的存储帐户中创建托管磁盘

此脚本基于 VHD 文件在同一或不同订阅中的存储帐户中创建托管磁盘。 使用此脚本将专用（未经过通用化/sysprep 处理）的 VHD 导入到托管 OS 磁盘以创建虚拟机。 另外，使用它将数据 VHD 导入到托管数据磁盘。 

不要在短时间内基于某个 VHD 文件创建多个相同的托管磁盘。 若要基于 vhd 文件创建托管磁盘，请创建 vhd 文件的 blob 快照，然后使用它创建托管磁盘。 由于存在一分钟内只能创建一个 blob 快照的限制，这将导致磁盘创建失败。 若要避免此限制，请[从 vhd 文件创建托管快照](./../scripts/storage-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)，然后使用托管快照在短时间内创建多个托管磁盘。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "基于 VHD 创建托管磁盘")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令基于某个 VHD 在不同订阅中创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | 创建用于创建磁盘的磁盘配置。 它包括存储父 VHD 的存储帐户的存储类型、位置和资源 ID，以及父 VHD 的 VHD URI。 |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | 使用作为参数传递的磁盘配置、磁盘名称和资源组名称创建磁盘。 |

## <a name="next-steps"></a>后续步骤

[通过将托管磁盘附加为 OS 磁盘创建虚拟机](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
