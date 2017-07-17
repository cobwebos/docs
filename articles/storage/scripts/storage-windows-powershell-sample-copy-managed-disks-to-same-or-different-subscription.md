---
title: "Azure PowerShell 脚本示例 - 将托管磁盘复制（移动）到同一或不同订阅 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 将托管磁盘复制（移动）到同一或不同订阅"
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
ms.date: 06/06/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 4dc83821a84fa8721c07a8660adeced26a1709f9
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# 使用 PowerShell 将托管磁盘复制到同一订阅或不同订阅
<a id="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell" class="xliff"></a>

此脚本在同一订阅或不同订阅中创建现有托管磁盘的副本。 将在与父托管磁盘相同的区域中创建新磁盘。   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## 示例脚本
<a id="sample-script" class="xliff"></a>

[!code-powershell[main](../../../powershell_scripts/storage/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "复制托管磁盘")]


## 脚本说明
<a id="script-explanation" class="xliff"></a>

此脚本使用以下命令并使用源托管磁盘的 Id 在目标订阅中创建新托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | 创建用于创建磁盘的磁盘配置。 它包括父磁盘的资源 Id 以及与父磁盘的位置相同的位置。  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | 使用作为参数传递的磁盘配置、磁盘名称和资源组名称创建磁盘。 |


## 后续步骤
<a id="next-steps" class="xliff"></a>

[基于托管磁盘创建虚拟机](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
