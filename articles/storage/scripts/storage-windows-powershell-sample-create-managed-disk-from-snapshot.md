---
title: "Azure PowerShell 脚本示例 - 基于快照创建托管磁盘 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 基于快照创建托管磁盘"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: ab5f047923271b4d2f79e7eb35067295fff18cda
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>使用 PowerShell 基于快照创建托管磁盘

此脚本基于快照创建托管磁盘。 可以使用它基于 OS 和数据磁盘的快照还原虚拟机。 基于相应的快照创建 OS 和数据托管磁盘，然后通过附加托管磁盘创建新的虚拟机。 还可以通过附加基于快照创建的数据磁盘来还原现有 VM 的数据磁盘。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "基于快照创建托管磁盘")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令基于快照创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | 获取快照属性。  |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | 创建用于创建磁盘的磁盘配置。 它包括父快照的资源 Id、与父快照的位置相同的位置，以及存储类型。  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | 使用作为参数传递的磁盘配置、磁盘名称和资源组名称创建磁盘。 |


## <a name="next-steps"></a>后续步骤

[基于托管磁盘创建虚拟机](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
