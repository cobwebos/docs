---
title: 路由网络流量 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 通过路由表路由网络流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 871b562fa12b93d1b65e23ca58615d35ef6bb34b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>使用 Azure CLI 通过路由表路由网络流量

默认情况下，Azure 自动在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 创建自定义路由的功能非常有用，例如，可以通过网络虚拟设备 (NVA) 在子网之间路由流量。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建路由表
> * 创建路由
> * 创建包含多个子网的虚拟网络
> * 将路由表关联到子网
> * 创建用于流量路由的 NVA
> * 将虚拟机 (VM) 部署到不同子网
> * 通过 NVA 将从一个子网的流量路由到另一个子网

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-a-route-table"></a>创建路由表

在创建路由表之前，请使用 [az group create](/cli/azure/group#az_group_create) 针对本文中创建的所有资源创建一个资源组。 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

使用 [az network route-table create](/cli/azure/network/route#az_network_route_table_create) 创建路由表。 以下示例创建名为 *myRouteTablePublic* 的路由表。 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>创建路由

使用 [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) 在路由表中创建路由。 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

将路由表关联到子网之前，必须先创建虚拟网络和子网。 使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建包含一个子网的虚拟网络。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 创建两个附加的子网。

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 将 *myRouteTablePublic* 路由表关联到公共子网。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>创建 NVA

NVA 是执行网络功能（如路由、防火墙或 WAN 优化）的 VM。

使用 [az vm create](/cli/azure/vm#az_vm_create) 在 *DMZ* 子网中创建 NVA。 创建 VM 时，Azure 默认会创建一个公共 IP 地址并将其分配到该 VM。 `--public-ip-address ""` 参数指示 Azure 不要创建公共 IP 地址并将其分配到该 VM，因为不需要从 Internet 连接到该 VM。 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

创建 VM 需要几分钟时间。 在 Azure 完成创建 VM 并返回有关 VM 的输出之前，请不要继续下一步。 

要使网络接口能够转发发送给它的、而不是发往其自身 IP 地址的网络流量，必须为该网络接口启用 IP 转发。 使用 [az network nic update](/cli/azure/network/nic#az_network_nic_update) 为网络接口启用 IP 转发。

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

在 VM 中，VM 中运行的操作系统或应用程序也必须能够转发网络流量。 使用 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) 在 VM 的操作系统中启用 IP 转发：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
执行该命令最长需要花费一分钟时间。

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM，以便可以在后续步骤中验证来自公共子网的流量是否通过 NVA 路由到专用子网。 

使用 [az vm create](/cli/azure/vm#az_vm_create) 在公共子网中创建一个 VM。 `--no-wait` 参数支持 Azure 在后台中执行命令，因此可以继续执行下一个命令。 为了简化本文的内容，此处使用了密码。 在生产部署中通常使用密钥。 如果使用密钥，还必须配置 SSH 代理转发。 有关详细信息，请参阅 SSH 客户端的文档。 将以下命令中的 `<replace-with-your-password>` 替换为所选的密码。

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

在专用子网中创建一个 VM。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

创建 VM 需要几分钟时间。 创建 VM 之后，Azure CLI 将显示类似于以下示例的信息： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
记下 publicIpAddress。 在稍后的步骤中会使用此地址通过 Internet 访问 VM。

## <a name="route-traffic-through-an-nva"></a>通过 NVA 路由流量

使用以下命令创建与 *myVmPrivate* VM 的 SSH 会话。 将 *<publicIpAddress>* 替换为 VM 的公共 IP 地址。 在上面的示例中，IP 地址为 *13.90.242.231*。

```bash 
ssh azureuser@<publicIpAddress>
```

当系统提示输入密码时，请输入在[创建虚拟机](#create-virtual-machines)中选择的密码。

使用以下命令在 *myVmPrivate* VM 上安装 traceroute：

```bash 
sudo apt-get install traceroute
```

使用以下命令测试从 *myVmPrivate* VM 发往 *myVmPublic* VM 的网络流量的路由。

```bash
traceroute myVmPublic
```

响应类似于以下示例：

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

可以看到流量从 *myVmPrivate* VM 直接路由到 *myVmPublic* VM。 Azure 的默认路由，直接在子网之间路由流量。 

使用以下命令从 *myVmPrivate* VM 通过 SSH 连接到 *myVmPublic* VM：

```bash 
ssh azureuser@myVmPublic
```

使用以下命令在 *myVmPublic* VM 上安装 traceroute：

```bash 
sudo apt-get install traceroute
```

使用以下命令测试从 *myVmPublic* VM 发往 *myVmPrivate* VM 的网络流量的路由。

```bash
traceroute myVmPrivate
```

响应类似于以下示例：

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
可以看到，第一个跃点为 10.0.2.4，即 NVA 的专用 IP 地址。 第二个跃点为 10.0.1.4，即 *myVmPrivate* VM 的专用 IP 地址。 添加到 *myRouteTablePublic* 路由表并关联到公共子网的路由导致 Azure 通过 NVA 路由流量，而不是直接将流量路由到专用子网。

同时关闭与 *myVmPublic* VM 和 *myVmPrivate* VM 的 SSH 会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

在本文中，我们创建了一个路由表并将其关联到了某个子网。 还创建了一个简单 NVA，用于将流量从公共子网路由到专用子网。 从 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) 部署各种执行网络功能（例如防火墙和 WAN 优化）的预配置 NVA。 在部署用于生产的路由表之前，建议全面了解 [Azure 中的路由](virtual-networks-udr-overview.md)、[管理路由表](manage-route-table.md)和 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

尽管可以在一个虚拟网络中部署多个 Azure 资源，但无法将某些 Azure PaaS 服务的资源部署到虚拟网络。 不过，仍可以限制为只允许来自某个虚拟网络子网的流量访问某些 Azure PaaS 服务的资源。 请继续学习下一篇教程，了解如何限制 Azure PaaS 资源的网络访问。

> [!div class="nextstepaction"]
> [限制 PaaS 资源的网络访问](tutorial-restrict-network-access-to-resources-cli.md)
