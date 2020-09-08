---
title: 从快照创建托管磁盘 - PowerShell 示例
description: Azure PowerShell 脚本示例 - 从快照创建托管磁盘
services: virtual-machines
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: f3a890083e4763bcff95a1361bed69907b80e033
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322645"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>使用 PowerShell 从快照创建托管磁盘 

此脚本基于快照创建托管磁盘。 可以使用它基于 OS 和数据磁盘的快照还原虚拟机。 基于相应的快照创建 OS 和数据托管磁盘，然后通过附加托管磁盘创建新的虚拟机。 还可以通过附加基于快照创建的数据磁盘来还原现有 VM 的数据磁盘。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令基于快照创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | 获取快照属性。  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | 创建用于磁盘创建的磁盘配置。 包括父快照的资源 ID、与父快照位置相同的位置以及存储类型。  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | 使用磁盘配置、磁盘名称和作为参数传递的资源组名称创建磁盘。 |


## <a name="next-steps"></a>后续步骤

[从托管磁盘创建虚拟机](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
