---
title: 在订阅的存储帐户中基于 VHD 文件创建托管磁盘 - PowerShell 示例
description: Azure PowerShell 脚本示例 - 基于 VHD 文件在同一或不同订阅中的存储帐户中创建托管磁盘
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1d743d840afc4505cdc39b258b21856b6c3e7aa9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322643"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>使用 PowerShell 基于 VHD 文件在同一或不同订阅中的存储帐户中创建托管磁盘

此脚本在相同或不同订阅的存储帐户中从 VHD 文件创建托管磁盘。 使用此脚本将专用（未经过通用化/sysprep 处理）的 VHD 导入到托管 OS 磁盘以创建虚拟机。 同时，使用它将数据 VHD 导入到托管数据磁盘。 

请勿在短时间内从一个 VHD 文件创建多个相同的托管磁盘。 若要从 VHD 文件创建托管磁盘，需创建该 VHD 文件的 blob 快照，然后再使用快照创建托管磁盘。 一分钟内只可创建一个 blob 快照，此限制会导致磁盘创建失败。 若要避免此限制，请[从 VHD 文件创建托管快照](virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)，然后使用托管快照在短时间内创建多个托管磁盘。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令在不同订阅中从 VHD 创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | 创建用于磁盘创建的磁盘配置。 它包括存储父 VHD 的存储帐户的存储类型、位置和资源 ID，以及父 VHD 的 VHD URI。 |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | 使用磁盘配置、磁盘名称和作为参数传递的资源组名称创建磁盘。 |

## <a name="next-steps"></a>后续步骤

[通过将托管磁盘附加为 OS 磁盘来创建虚拟机](virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
