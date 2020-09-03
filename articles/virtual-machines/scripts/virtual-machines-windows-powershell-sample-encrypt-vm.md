---
title: Azure PowerShell 脚本示例 - 加密 Windows VM
description: Azure PowerShell 脚本示例 - 加密 Windows VM
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 102187d52ec86a7a87223975ae3d1f6bbe9097d7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078769"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>使用 Azure PowerShell 加密 Windows 虚拟机

此脚本创建安全的 Azure Key Vault、加密密钥、Azure Active Directory 服务主体和 Windows 虚拟机 (VM)。 然后使用 Key Vault 和服务主体凭据中的加密密钥对 VM 进行加密。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) | 创建 Azure Key Vault，存储加密密钥等安全数据。 |
| [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) | 在 Key Vault 中创建加密密钥。 |
| [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) | 创建 Azure Active Directory 服务主体，安全地进行身份验证并控制对加密密钥的访问。 |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | 设置对 Key Vault 的权限，授予服务主体访问加密密钥的权限。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和网络安全组。 此命令还将打开端口 80 并设置管理凭据。 |
| [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) | 获取有关 Key Vault 的所需信息 |
| [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) | 使用服务主体凭据和加密密钥对 VM 进行加密。 |
| [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) | 显示 VM 加密过程的状态。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
