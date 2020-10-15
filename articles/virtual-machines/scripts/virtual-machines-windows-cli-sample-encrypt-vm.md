---
title: Azure CLI Script 示例 - 加密 Windows VM
description: Azure CLI 脚本示例 - 加密 Windows VM
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0ea6bc61aae45386d2a7b90a32f66ad5de342107
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494530"
---
# <a name="encrypt-a-windows-virtual-machine-in-azure"></a>在 Azure 中加密 Windows 虚拟机

此脚本创建安全的 Azure Key Vault、加密密钥、Azure Active Directory 服务主体和 Windows 虚拟机 (VM)。 然后使用 Key Vault 和服务主体凭据中的加密密钥对 VM 进行加密。

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

此脚本使用以下命令创建资源组、Azure 密钥保管库、服务主体、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az keyvault create](/cli/azure/keyvault) | 创建 Azure Key Vault，存储加密密钥等安全数据。 |
| [az keyvault key create](/cli/azure/keyvault/key) | 在 Key Vault 中创建加密密钥。 |
| [az ad sp create-for-rbac](/cli/azure/ad/sp) | 创建 Azure Active Directory 服务主体，安全地进行身份验证并控制对加密密钥的访问。 |
| [az keyvault set-policy](/cli/azure/keyvault) | 设置对 Key Vault 的权限，授予服务主体访问加密密钥的权限。 |
| [az vm create](/cli/azure/vm) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az vm encryption enable](/cli/azure/vm/encryption) | 使用服务主体凭据和加密密钥对 VM 进行加密。 |
| [az vm encryption show](/cli/azure/vm/encryption) | 显示 VM 加密过程的状态。 |
| [az group delete](/cli/azure/vm/extension) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%windows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
