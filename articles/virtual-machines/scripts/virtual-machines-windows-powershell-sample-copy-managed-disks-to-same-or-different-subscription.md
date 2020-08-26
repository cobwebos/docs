---
title: 将托管磁盘复制到订阅 (Windows) - PowerShell 示例
description: Azure PowerShell 脚本示例 - 将托管磁盘复制或移动到同一或不同订阅
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 6f191eaf5c0a02f77d267999c307d34e083b6cdf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069186"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell-windows"></a>使用 PowerShell 将托管磁盘复制到同一订阅或不同订阅 (Windows)

此脚本在相同或不同订阅中创建现有托管磁盘的副本。 在父托管磁盘所在的区域中创建新磁盘。   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令，通过源托管磁盘的 ID 在目标订阅中创建新的托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | 创建用于磁盘创建的磁盘配置。 包括父磁盘的资源 ID 以及与父磁盘位置相同的位置。  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | 使用磁盘配置、磁盘名称和作为参数传递的资源组名称创建磁盘。 |


## <a name="next-steps"></a>后续步骤

[从托管磁盘创建虚拟机](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
