---
title: 从托管磁盘到订阅的快照 (Linux) - PowerShell
description: Azure PowerShell 脚本示例 - 将托管磁盘的快照复制（或移动）到同一或不同订阅
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 54fbdc86ecd035593960eaa57187fbf9e35393fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069318"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-linux"></a>使用 PowerShell 将托管磁盘的快照复制到同一订阅或不同订阅 (Linux)

此脚本会将托管磁盘的快照复制到相同或不同的订阅。 将此脚本用于以下方案：

1. 将高级存储 (Premium_LRS) 中的快照迁移到标准存储（Standard_LRS 或 Standard_ZRS）以降低成本。
1. 将快照从本地冗余存储（Premium_LRS、Standard_LRS）迁移到区域冗余存储（Standard_ZRS），以从 ZRS 存储的更高可靠性中受益。
1. 将快照移到同一区域中的不同订阅，以延长保留时间。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令，通过源快照的 ID 在目标订阅中创建快照。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | 创建用于创建快照的快照配置。 包括父快照的资源 ID 以及与父快照相同的位置。  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azdisk) | 使用快照配置、快照名称和作为参数传递的资源组名称创建快照。 |

## <a name="next-steps"></a>后续步骤

[从快照创建虚拟机](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Linux VM 文档](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
