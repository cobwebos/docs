---
title: "Azure PowerShell 脚本示例 - 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘"
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
ms.openlocfilehash: 3db2ba858e5c685bb2b53a4f09423acdcdb10c22
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# 使用 PowerShell 基于 VHD 创建快照以在短时间内创建多个相同的托管磁盘
<a id="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell" class="xliff"></a>

此脚本基于 VHD 文件在同一或不同订阅中的存储帐户中创建快照。 可以使用此脚本将专用（非通用/系统准备的）VHD 导入到某个快照中，然后使用该快照在短时间内创建多个相同的托管磁盘。 另外，还可以使用它将数据 VHD 导入到某个快照中，然后使用该快照在短时间内创建多个托管磁盘。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## 示例脚本
<a id="sample-script" class="xliff"></a>

[!code-powershell[main](../../../powershell_scripts/storage/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "基于 VHD 创建快照")]


## 脚本说明
<a id="script-explanation" class="xliff"></a>

此脚本使用以下命令基于某个 VHD 在不同订阅中创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | 创建用于创建磁盘的磁盘配置。 它包括存储着父 VHD 的存储帐户的存储类型、位置和资源 Id，以及父 VHD 的 VHD URI。 |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | 使用作为参数传递的磁盘配置、磁盘名称和资源组名称创建磁盘。 |

## 后续步骤
<a id="next-steps" class="xliff"></a>

[基于快照创建托管磁盘](./../../storage/scripts/storage-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[通过将托管磁盘附加为 OS 磁盘创建虚拟机](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
