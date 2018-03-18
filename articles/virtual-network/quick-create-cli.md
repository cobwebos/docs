---
title: "创建 Azure 虚拟网络 - Azure CLI | Microsoft Docs"
description: "快速了解如何使用 Azure CLI 创建虚拟网络。 虚拟网络能让 Azure 资源（例如虚拟机）彼此之间私下通信以及与 Internet 进行通信。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 46fec48720c817072ce838dd2e4c07725be5a7fe
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>使用 Azure CLI 创建虚拟网络

虚拟网络能让 Azure 资源（例如虚拟机 (VM)）彼此之间私下通信以及与 Internet 进行通信。 本文将介绍如何创建虚拟网络。 创建虚拟网络后，将两个 VM 部署到该虚拟网络中。 然后从 Internet 连接到其中一个 VM，并与另一个 VM 私下通信。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.28 或更高版本。 要查找已安装的版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 


## <a name="create-a-virtual-network"></a>创建虚拟网络

在创建虚拟网络之前，必须创建一个资源组用于包含该虚拟网络。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例创建名为“myVirtualNetwork”的默认虚拟网络，它具有一个名为“default”的子网：

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM：

### <a name="create-the-first-vm"></a>创建第一个 VM

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 `--no-wait` 选项会在后台创建 VM，因此可继续执行下一步。 以下示例创建名为 *myVm1* 的 VM：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>创建第二个 VM

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

创建 VM 需要几分钟时间。 创建 VM 后，Azure CLI 会返回类似以下示例的输出： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

记下 publicIpAddress。 在下一步中，此地址用于从 Internet 连接到 VM。

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

在以下命令中将 `<publicIpAddress>` 替换为 *myVm2* VM 的公共 IP 地址，然后输入以下命令：

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>VM 之间进行私下通信

若要确认 *myVm2* VM 和 *myVm1* VM 之间的私下通信，请输入以下命令：

```bash
ping myVm1 -c 4
```

从 10.0.0.4 接收了四个回复。

退出与 *myVm2* VM 的 SSH 会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除：

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

在本文中，已创建了默认虚拟网络和两个 VM。 你从 Internet 连接到了其中一个 VM，然后该 VM 与另一个 VM 之间进行了私下通信。 若要了解有关虚拟网络设置的详细信息，请参阅[管理虚拟网络](manage-virtual-network.md)。 

默认情况下，Azure 允许虚拟机之间进行不受限制的私下通信，但仅允许从 Internet 到 Linux VM 的入站 SSH 会话。 若要了解如何允许或限制进出 VM 的不同类型的网络通信，请转到下一个教程。

> [!div class="nextstepaction"]
> [筛选网络流量](virtual-networks-create-nsg-arm-cli.md)
