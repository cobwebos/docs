---
title: "创建虚拟网络 - Azure CLI | Microsoft Docs"
description: "快速了解如何使用 Azure CLI 创建虚拟网络。 虚拟网络能让多种不同类型的 Azure 资源互相私下通信。"
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
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b2fc8a622549a9858c6c769a7e648fe07a3d01c1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>使用 Azure CLI 创建虚拟网络

本文将介绍如何创建虚拟网络。 创建虚拟网络后，向虚拟网络中部署两个虚拟机，并让它们互相私下通信。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 要查找已安装的版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。 所有 Azure 资源均在 Azure 位置（或区域）中创建。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 命令创建虚拟网络。 以下示例创建名为“myVirtualNetwork”的默认虚拟网络，它具有一个名为“default”的子网。 由于未指定位置，因此 Azure 会在与资源组相同的位置中创建虚拟网络。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

创建虚拟网络后，返回的部分信息如下所示。

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

所有虚拟网络均分配有一个或多个地址前缀。 由于创建虚拟网络时未指定地址前缀，因此 Azure 默认定义了 10.0.0.0/16 地址空间。 CIDR 表示法中指定了此地址空间。 地址空间 10.0.0.0/16 包含 10.0.0.0-10.0.255.254。

返回的另一部分信息是命令中指定的 default 子网的地址前缀 10.0.0.0/24。 虚拟网络包含零个或多个子网。 该命令创建了名为“default”的单个子网，但子网未指定任何地址前缀。 如果虚拟网络或子网未指定地址前缀时，默认情况下，Azure 会定义 10.0.0.0/24 作为第一个子网的地址前缀。 结果，子网虽然包含 10.0.0.0-10.0.0.254，但可用的地址仅限 10.0.0.4-10.0.0.254，因为 Azure 保留了每个子网中的前四个地址 (0-3) 和最后一个地址。

## <a name="create-virtual-machines"></a>创建虚拟机

虚拟网络能让几种类型的 Azure 资源互相私下通信。 虚拟机是一种能部署到虚拟网络的资源类型。 在虚拟网络中创建两个虚拟机，以便在稍后的步骤中验证和了解虚拟机在虚拟网络中互相通信的原理。

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建虚拟机。 以下示例创建一个名为“myVm1”的虚拟机： 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 `--no-wait` 选项会在后台创建虚拟机，因此可继续执行下一步。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure 会在“myVirtualNetwork”虚拟网络的“default”子网中自动创建虚拟机，因为虚拟网络存在于资源组中，并且命令中未指定任何虚拟网络或子网。 创建期间，Azure DHCP 自动将 10.0.0.4 分配到虚拟机，因为它是“default”子网中的第一个可用地址。 虚拟机创建的位置必须与虚拟网络在同一位置。 虽然在本文中虚拟机在同一资源组，但实际上并无此要求。

创建第二个虚拟机。 默认情况下，Azure 还是在“default”子网中创建此虚拟机。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

创建虚拟机需花费几分钟的时间。 创建虚拟机后，Azure CLI 会返回类似以下示例的输出： 

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

在示例中，可以看到 privateIpAddress 是 10.0.0.5。 Azure DHCP 自动将 10.0.0.5 分配到虚拟机，因为它是“default”子网中下一个可用地址。 记下 publicIpAddress。 在稍后的步骤中会使用此地址通过 Internet 访问虚拟机。 公共 IP 地址不是从虚拟网络或子网地址前缀分配的。 公共 IP 地址是从[分配给各 Azure 区域的地址池](https://www.microsoft.com/download/details.aspx?id=41653)分配的。 虽然 Azure 知晓虚拟机分配的公共 IP 地址，但在虚拟机上运行的操作系统却不知道分配给它的任何公共 IP 地址。

## <a name="connect-to-a-virtual-machine"></a>连接到虚拟机

使用以下命令创建与 myVm2 虚拟机的 SSH 会话。 将 `<publicIpAddress>` 替换为虚拟机的公共 IP 地址。 在上面的示例中，IP 地址是 40.68.254.142。

```bash 
ssh <publicIpAddress>
```

## <a name="validate-communication"></a>验证通信

使用以下命令来确认从 myVm2 与 myVm1 进行的通信：

```bash
ping myVm1 -c 4
```

从 10.0.0.4 接收了四个回复。 可以从 myVm2 与 myVm1 进行通信，因为两个虚拟机都具有“default”子网分配的私有 IP 地址。 可以通过主机名 ping，因为 Azure 将自动为虚拟网络中的所有主机提供 DNS 名称解析。

使用以下命令确认到 Internet 的出站通信：

```bash
ping bing.com -c 4
```

从 bing.com 接收了四个回复。默认情况下，虚拟网络中的任意虚拟机都可以与 Internet 进行出站通信。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。 退出 SSH 会话，返回 VM，然后删除资源.。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

本文将使用一个子网和两个虚拟机来部署默认虚拟网络。 要了解如何使用多个子网创建自定义虚拟网络并执行基本管理任务，请继续参阅教程的创建和管理自定义虚拟网络部分。


> [!div class="nextstepaction"]
> [创建和管理自定义虚拟网络](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
