---
title: 用于创建多个相同托管磁盘的 VHD 快照 (Windows) - PowerShell
description: Azure PowerShell 脚本示例 - 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 0625c968a3c60d38ca2bbe2f13318ccd85d61a61
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082369"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>使用 PowerShell 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘 (Windows)

此脚本在相同或不同订阅的存储帐户中从 VHD 文件创建快照。 使用此脚本将专用 VHD（未通用化/未进行 sysprep）导入到某快照，然后使用该快照在短时间内创建多个相同的托管磁盘。 同时使用它将数据 VHD 导入到某快照，然后使用该快照在短时间内创建多个托管磁盘。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>后续步骤

[从快照创建托管磁盘](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[通过将托管磁盘附加为 OS 磁盘来创建虚拟机](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
