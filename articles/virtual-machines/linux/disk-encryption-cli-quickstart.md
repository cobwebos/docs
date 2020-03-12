---
title: 使用 Azure CLI 创建和加密 Linux VM
description: 本快速入门介绍如何使用 Azure CLI 创建和加密 Linux 虚拟机
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 14b6f07ead59f3d86b80489460956dd37fb7378b
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970698"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>快速入门：使用 Azure CLI 创建和加密 Linux VM

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门演示如何使用 Azure CLI 创建和加密 Linux 虚拟机 (VM)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例创建一个名为 *myVM* 的 VM。

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

创建 VM 和支持资源需要几分钟时间。 以下示例输出表明 VM 创建操作已成功。

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>创建为加密密钥配置的密钥保管库

Azure 磁盘加密将其加密密钥存储在 Azure 密钥保管库中。 使用 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 创建密钥保管库。 要使密钥保管库能够存储加密密钥，请使用 --enabled-for-disk-encryption 参数。

> [!Important]
> 每个密钥保管库必须有一个在 Azure 中唯一的名称。 在下面的示例中，将 <your-unique-keyvault-name> 替换为你选择的名称。

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>加密虚拟机

使用 [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest) 加密 VM，为 --disk-encryption-keyvault 参数提供唯一的密钥保管库名称。

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

稍后，进程将返回“加密请求已被接受。 请使用 'show' 命令监视进度”。 "show" 命令是 [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)。

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

启用加密后，你将在返回的输出中看到以下内容：

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和密钥保管库，可以使用 [az group delete](/cli/azure/group) 命令将其删除。 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个虚拟机，创建了一个启用加密密钥的密钥保管库，并对 VM 进行了加密。  请继续学习下一篇文章，详细了解 Linux VM 的 Azure 磁盘加密。

> [!div class="nextstepaction"]
> [Azure 磁盘加密概述](disk-encryption-overview.md)