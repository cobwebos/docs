---
title: Azure PowerShell 脚本示例 - 通过将托管磁盘附加为 OS 磁盘来创建 VM | Microsoft Docs
description: Azure PowerShell 脚本示例 - 通过将托管磁盘附加为 OS 磁盘来创建 VM
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: f0a1184daa41a30bb0c47764a3a47af68a6a9251
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442805"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>通过将现有托管 OS 磁盘与 PowerShell 配合使用来创建虚拟机

此脚本通过将现有托管磁盘附加为 OS 磁盘来创建虚拟机。 在前面的方案中使用此脚本：
* 基于从不同订阅中的托管磁盘复制的现有托管 OS 磁盘创建 VM
* 基于从专用 VHD 文件创建的现有托管磁盘创建 VM 
* 基于从快照创建的现有托管 OS 磁盘创建 VM 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令获取托管磁盘属性，将托管磁盘附加到新 VM 并创建 VM。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/Get-AzDisk) | 基于磁盘的名称和资源组获取磁盘对象。 返回的磁盘对象的 ID 属性用于将磁盘附加到新 VM |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | 创建 VM 配置。 此配置包括 VM 名称、操作系统和管理凭据等信息。 在创建 VM 期间将使用此配置。 |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | 使用磁盘的 ID 属性将托管磁盘作为 OS 磁盘附加到新虚拟机 |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。 |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | 创建网络接口。 |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 创建虚拟机。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |

对于市场映像，使用 [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) 设置计划信息。

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Linux VM 文档](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
