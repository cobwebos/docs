---
title: "为 Linux VM 设置 Azure Key Vault | Microsoft 文档"
description: "如何使用 CLI 2.0 设置用于 Azure Resource Manager 虚拟机的 Key Vault。"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: 2cc9b4c978e9a4deb0c8443c4b0f9e301a7cf492
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>如何使用 Azure CLI 2.0 为虚拟机设置 Key Vault

在 Azure Resource Manager 堆栈中，密码/证书被建模为 Key Vault 所提供的资源。 若要了解有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/key-vault-whatis.md) 为了让 Key Vault 能与 Azure Resource Manager VM 搭配使用，必须将 Key Vault 上的 *EnabledForDeployment* 属性设置为 true。 本文说明如何通过 Azure CLI 2.0 设置用于 Azure 虚拟机 (VM) 的 Key Vault。 还可以使用 [Azure CLI 1.0](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。

若要执行这些步骤，需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

## <a name="create-a-key-vault"></a>创建密钥保管库
使用 [az keyvault create](/cli/azure/keyvault#create) 创建密钥保管库并分配部署策略。 以下示例在 `myResourceGroup` 资源组中创建名为 `myKeyVault` 的密钥保管库：

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>更新用于 VM 的 Key Vault
使用 [az keyvault update](/cli/azure/keyvault#update) 在现有的密钥保管库上设置部署策略。 以下命令在 `myResourceGroup` 资源组中更新名为 `myKeyVault` 的密钥保管库：

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>使用模板设置密钥保管库
使用模板时，必须将 Key Vault 资源的 `enabledForDeployment` 属性设置为 `true`，如下所示：

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>后续步骤
有关使用模板创建 Key Vault 时可以配置的其他选项，请参阅[创建密钥保管库](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)。
