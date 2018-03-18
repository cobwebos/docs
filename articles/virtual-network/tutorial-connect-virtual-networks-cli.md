---
title: "使用虚拟网络对等互连连接虚拟网络 - Azure CLI | Microsoft Docs"
description: "了解如何使用虚拟网络对等互连连接虚拟网络。"
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
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>通过 Azure CLI 使用虚拟网络对等互连连接虚拟网络

可以使用虚拟网络对等互连将虚拟网络互相连接。 将虚拟网络对等互连后，两个虚拟网络中的资源将能够以相同的延迟和带宽相互通信，就像这些资源位于同一个虚拟网络中一样。 本文介绍如何创建两个虚拟网络并将其对等互连。 学习如何：

> [!div class="checklist"]
> * 创建两个虚拟网络
> * 创建虚拟网络间的对等互连
> * 测试对等互连

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-virtual-networks"></a>创建虚拟网络

创建虚拟网络之前，必须为虚拟网络创建资源组以及本文中创建的所有其他资源。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例使用地址前缀 *10.0.0.0/16* 创建一个名为 *myVirtualNetwork1* 的虚拟网络。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

使用地址前缀 *10.1.0.0/16* 创建一个名为 *myVirtualNetwork2* 的虚拟网络。 该地址前缀不与 *myVirtualNetwork1* 虚拟网络的地址前缀重叠。 不能对等互连地址前缀重叠的虚拟网络。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>将虚拟网络对等互连

对等互连是在虚拟网络 ID 之间建立的，因此必须先使用 [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) 获取每个虚拟网络的 ID，并将 ID 存储在变量中。

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

使用 [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) 创建从 *myVirtualNetwork1* 到 *myVirtualNetwork2* 的对等互连。 如果未指定 `--allow-vnet-access` 参数，将建立对等互连，但没有通信可以通过它进行。

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

在前一个命令执行后返回的输出中，可以看到 **peeringState** 为“已启动”。 对等互连将保持“已启动”状态，直到你创建从 *myVirtualNetwork2* 到 *myVirtualNetwork1* 的对等互连。 创建从 *myVirtualNetwork2* 到 *myVirtualNetwork1* 的对等互连。 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

在前一个命令执行后返回的输出中，可以看到 **peeringState** 为“已连接”。 Azure 还将 *myVirtualNetwork1-myVirtualNetwork2* 对等互连的对等互连状态更改为“已连接”。 使用 [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) 确认 *myVirtualNetwork1-myVirtualNetwork2* 对等互连的对等互连状态是否已更改为“已连接”。

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

在两个虚拟网络中的对等互连的 **peeringState** 为“已连接”之前，在一个虚拟网络中的资源无法与另一个虚拟网络中的资源通信。 

对等互连在两个虚拟网络之间进行，但不可传递。 因此，举例来说，如果还想要将 *myVirtualNetwork2* 对等互连到 *myVirtualNetwork3*，则需要另外在虚拟网络 *myVirtualNetwork2* 和 *myVirtualNetwork3* 创建对等互连。 即使 *myVirtualNetwork1* 已与 *myVirtualNetwork2* 对等互连，也仅当 *myVirtualNetwork1* 也与 *myVirtualNetwork3* 对等互连时，*myVirtualNetwork1* 中的资源才能访问 *myVirtualNetwork3* 中的资源。 

将生产虚拟网络对等互连之前，建议全面了解[对等互连概述](virtual-network-peering-overview.md)、[管理对等互连](virtual-network-manage-peering.md)和[虚拟网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 尽管本文介绍了同一订阅和位置中两个虚拟网络之间的对等互连，但也可将[不同区域](#register)和[不同 Azure 订阅](create-peering-different-subscriptions.md#cli)中的虚拟网络对等互连。 还可以使用对等互连创建[中心辐射型网络设计](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

## <a name="test-peering"></a>测试对等互连

若要通过对等互连测试不同虚拟网络中的虚拟机之间的网络通信，请将虚拟机部署到每个子网，然后在虚拟机之间进行通信。 

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 以下示例在 *myVirtualNetwork1* 虚拟网络中创建一个名为“myVm1”的虚拟机。 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 `--no-wait` 选项会在后台创建虚拟机，因此可继续执行下一步。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

Azure 会自动分配 10.0.0.4 作为虚拟机的专用 IP 地址，因为 10.0.0.4 是 *myVirtualNetwork1* 的 *Subnet1* 中的第一个可用 IP 地址。 

在 *myVirtualNetwork2* 虚拟网络中创建虚拟机。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

创建虚拟机需花费几分钟的时间。 创建虚拟机后，Azure CLI 会显示类似以下示例的信息： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

在示例输出中，可以看到 privateIpAddress 是 10.1.0.4。 Azure DHCP 会自动将 10.1.0.4 分配给虚拟机，因为它是 *myVirtualNetwork2* 的 *Subnet1* 中的第一个可用地址。 记下 publicIpAddress。 在稍后的步骤中会使用此地址通过 Internet 访问虚拟机。

### <a name="test-virtual-machine-communication"></a>测试虚拟机通信

使用以下命令创建与 myVm2 虚拟机的 SSH 会话。 将 `<publicIpAddress>` 替换为虚拟机的公共 IP 地址。 在前一示例中，公共 IP 地址为 *13.90.242.231*。

```bash 
ssh <publicIpAddress>
```

对 *myVirtualNetwork1* 中的虚拟机进行 ping 操作。

```bash 
ping 10.0.0.4 -c 4
```

将收到四个回复。 如果按虚拟机的名称 (*myVm1*) 而不是其 IP 地址进行 ping 操作，ping 操作会失败，因为 *myVm1* 是未知的主机名。 Azure 的默认名称解析在同一虚拟网络中的虚拟机之间可以正常工作，但在不同虚拟网络中的虚拟机之间无法正常工作。 若要跨虚拟网络解析名称，必须[部署自己的 DNS 服务器](virtual-networks-name-resolution-for-vms-and-role-instances.md)或使用 [Azure DNS 专用域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

关闭与 *myVm2* 虚拟机的 SSH 会话。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>注册全局虚拟网络对等互连预览版**

在同一区域中的虚拟网络之间建立对等互连的功能已推出正式版。 在不同区域的虚拟网络之间建立对等互连目前处于预览版状态。 有关可用区域，请参阅[虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)。 若要跨区域在虚拟网络之间建立对等互连，必须先通过完成以下步骤（在要对等互连的每个虚拟网络所在的订阅中执行）来注册预览版：

1. 输入以下命令，针对预览版进行注册：

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. 输入以下命令，确认已针对预览版进行了注册：

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  对于这两个订阅，如果在输入前一个命令后收到的 **RegistrationState** 输出为“已注册”之前，尝试将不同区域中的虚拟网络对等互连，则对等互连将失败。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用虚拟网络对等互连连接两个网络。 可以通过 VPN [将自己的计算机连接到虚拟网络](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，并可与虚拟网络或对等虚拟网络中的资源进行交互。

继续学习可重用脚本的脚本示例，以完成虚拟网络文章中涉及的许多任务。

> [!div class="nextstepaction"]
> [虚拟网络脚本示例](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
