---
title: "Azure CLI Script 示例 - 加密 Windows VM | Microsoft Docs"
description: "Azure CLI Script 示例 - 加密 Windows VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 7e1e9a69d226b03e6722c2d19b7a091cfe4e71c7
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2017
---
# <a name="encrypt-a-windows-virtual-machine-in-azure"></a>加密 Azure 中的 Windows 虚拟机

此脚本创建安全的 Azure Key Vault、加密密钥、Azure Active Directory 服务主体和 Windows 虚拟机 (VM)。 然后使用来自 Key Vault 和服务主体凭据的加密密钥对 VM 进行加密。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_windows_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Azure Key Vault、服务主体、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | 创建 Azure Key Vault 来存储安全的数据，例如加密密钥。 |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key#az_keyvault_key_create) | 在 Key Vault 中创建加密密钥。 |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp#az_ad_sp_create_for_rbac) | 创建 Azure Active Directory 服务主体来安全地进行身份验证并控制对加密密钥的访问。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | 设置对 Key Vault 的权限来向服务主体授予对加密密钥的访问权限。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_enable) | 使用服务主体凭据和加密密钥在 VM 上启用加密。 |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_show) | 显示 VM 加密过程的状态。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%windows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
