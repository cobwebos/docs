---
title: "Azure 快速入门 - 创建 VM CLI | Microsoft 文档"
description: "快速了解如何使用 Azure CLI 创建虚拟机。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3ad45837a8c3f0e37e8571fb9b894df53ecb5f56
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建 Linux 虚拟机

Azure CLI 2.0 用于从命令行或脚本创建和管理 Azure 资源。 本指南详细介绍了如何使用 Azure CLI 部署运行 Ubuntu 14.04 LTS 的虚拟机。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="log-in-to-azure"></a>登录 Azure 

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 `westeurope` 位置创建名为 `myResourceGroup` 的资源组。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#create) 命令创建 VM。 

下面的示例创建一个名为 `myVM` 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

创建 VM 后，Azure CLI 将显示类似于以下示例的信息。 记下公共 IP 地址。 此地址用于访问 VM。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

使用以下命令创建与虚拟机的 SSH 会话。 将 IP 地址替换为你的虚拟机的公共 IP 地址。

```bash 
ssh <Public IP Address>
```

## <a name="delete-virtual-machine"></a>删除虚拟机

当不再需要时，可以使用以下命令删除资源组、VM 以及所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[创建具有高可用性的虚拟机教程](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[浏览 VM 部署 CLI 示例](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
