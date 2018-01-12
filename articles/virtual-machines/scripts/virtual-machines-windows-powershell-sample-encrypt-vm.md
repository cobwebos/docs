---
title: "Azure PowerShell 脚本示例 - 加密 Windows VM | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 加密 Windows VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: iainfou
ms.openlocfilehash: f405cdaf61d6aaafa8568a9d7f21614071285c17
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>使用 Azure PowerShell 加密 Windows 虚拟机

此脚本创建安全的 Azure Key Vault、加密密钥、Azure Active Directory 服务主体和 Windows 虚拟机 (VM)。 然后使用来自 Key Vault 和服务主体凭据的加密密钥对 VM 进行加密。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) | 创建 Azure Key Vault 来存储安全的数据，例如加密密钥。 |
| [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) | 在 Key Vault 中创建加密密钥。 |
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | 创建 Azure Active Directory 服务主体来安全地进行身份验证并控制对加密密钥的访问。 |
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | 设置对 Key Vault 的权限来向服务主体授予对加密密钥的访问权限。 |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和网络安全组。 此命令还将打开端口 80，并设置管理凭据。 |
| [Get-AzureRmKeyVault](/powershell/module/azurerm.keyvault/get-azurermkeyvault) | 获取关于 Key Vault 的必需信息 |
| [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) | 使用服务主体凭据和加密密钥在 VM 上启用加密。 |
| [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) | 显示 VM 加密过程的状态。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
