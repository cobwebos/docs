---
title: 用于创建多个相同托管磁盘的 VHD 快照 (Linux) - PowerShell
description: Azure PowerShell 脚本示例 - 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 8a80056ed3368f6a2a5c4ad7fa31424524e824b2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079921"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-linux"></a>使用 PowerShell 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘 (Linux)

此脚本在相同或不同订阅的存储帐户中从 VHD 文件创建快照。 使用此脚本将专用 VHD（未通用化/未进行 sysprep）导入到某快照，然后使用该快照在短时间内创建多个相同的托管磁盘。 同时使用它将数据 VHD 导入到某快照，然后使用该快照在短时间内创建多个托管磁盘。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令在不同订阅中从 VHD 创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | 创建用于磁盘创建的磁盘配置。 包括存储类型、位置、存储父 VHD 的存储帐户的资源 ID 以及父 VHD 的 VHD URI。 |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | 使用磁盘配置、磁盘名称和作为参数传递的资源组名称创建磁盘。 |

## <a name="next-steps"></a>后续步骤

[从快照创建托管磁盘](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Linux VM 文档](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
