---
title: 使用 Azure CLI 创建和加密 Windows VM
description: 本快速入门介绍如何使用 Azure CLI 创建和加密 Windows 虚拟机
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 9bbe74bd2f3137443b4e239201c604d9de52582e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245687"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>快速入门：使用 Azure CLI 创建和加密 Windows VM

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门演示如何使用 Azure CLI 创建和加密 Windows Server 2016 虚拟机 (VM)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例创建一个名为 *myVM* 的 VM。 此示例使用 *azureuser* 作为管理用户名，使用 *myPassword12* 作为密码。 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
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
> 每个密钥保管库必须具有唯一的名称。 以下示例创建名为“myKV”  的密钥保管库，但你必须将其命名为不同的名称。

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>加密虚拟机

使用 [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest) 加密 VM，为 --disk-encryption-keyvault 参数提供唯一的密钥保管库名称。

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

可以使用 [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show) 验证 VM 是否已启用加密

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

将在返回的输出中看到以下内容：

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和密钥保管库，可以使用 [az group delete](/cli/azure/group) 命令将其删除。 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个虚拟机，创建了一个启用加密密钥的密钥保管库，并对 VM 进行了加密。  请继续学习下一篇文章，详细了解 IaaS VM 的 Azure 磁盘加密先决条件。

> [!div class="nextstepaction"]
> [Azure 磁盘加密概述](disk-encryption-overview.md)

