---
title: "路由网络流量 - Azure CLI | Microsoft Docs"
description: "了解如何使用 Azure CLI 通过路由表路由网络流量。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>使用 Azure CLI 通过路由表路由网络流量

默认情况下，Azure 自动在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 创建自定义路由的功能非常有用，例如，可以通过防火墙在子网之间路由流量。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建路由表
> * 创建路由
> * 将路由表关联到虚拟网络子网
> * 测试路由
> * 排查路由问题

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-a-route-table"></a>创建路由表

默认情况下，Azure 在虚拟网络中的所有子网之间路由流量。 若要详细了解 Azure 的默认路由，请参阅[系统路由](virtual-networks-udr-overview.md)。 若要覆盖 Azure 的默认路由，可以创建包含路由的路由表，并将该路由表关联到虚拟网络子网。

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

一个路由表包含零个或多个路由。 使用 [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) 在路由表中创建路由。 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

该路由通过 IP 地址为 10.0.2.4 的网络虚拟设备来定向所有发往 10.0.1.0/24 地址前缀的流量。 后续步骤将会创建该网络虚拟设备以及具有指定地址前缀的子网。 该路由覆盖 Azure 的默认路由。默认路由在子网之间直接路由流量。 每个路由指定下一跃点类型。 下一跃点类型指示 Azure 如何路由流量。 在此示例中，下一跃点类型为 *VirtualAppliance*。 若要详细了解 Azure 中的所有可用下一跃点类型及其使用时机，请参阅[下一跃点类型](virtual-networks-udr-overview.md#custom-routes)。

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

可将一个路由表关联到零个或多个子网。 子网可以没有或有一个与其相关联的路由表。 来自子网的出站流量将会根据 Azure 的默认路由，以及已添加到与子网关联的路由表中的任何自定义路由进行路由。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 将 *myRouteTablePublic* 路由表关联到公共子网。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

在部署用于生产的路由表之前，我们建议全面了解 [Azure 中的路由](virtual-networks-udr-overview.md)和 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="test-routing"></a>测试路由

若要测试路由，需要创建一个虚拟机，充当用于路由上一步骤中创建的路由的网络虚拟设备。 创建网络虚拟设备后，可虚拟机部署到公共和专用子网。 然后通过网络虚拟设备将流量从公共子网路由到专用子网。

### <a name="create-a-network-virtual-appliance"></a>创建网络虚拟设备

在上一步骤中，我们创建了将网络虚拟设备指定为下一跃点类型的路由。 运行网络应用程序的虚拟机通常称为网络虚拟设备。 在生产环境中，部署的网络虚拟设备通常是预配置的虚拟机。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 中提供了多个网络虚拟设备。 本文将会创建一个基本的虚拟机。 

使用 [az vm create](/cli/azure/vm#az_vm_create) 在外围网络子网中创建一个网络虚拟设备。 创建虚拟机时，Azure 默认会创建一个公共 IP 地址并将其分配到该虚拟机。 `--public-ip-address ""` 参数指示 Azure 不要创建公共 IP 地址并将其分配到该虚拟机，因为不需要从 Internet 连接到该虚拟机。 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。

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

创建虚拟机需花费几分钟的时间。 在 Azure 完成创建虚拟机并返回有关虚拟机的输出之前，请不要继续下一步。 在生产环境中，部署的网络虚拟设备通常是预配置的虚拟机。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 中提供了多个网络虚拟设备。

对于附加到虚拟机的、用于转发发往任何未分配到网络接口的 IP 地址的流量的每个 Azure [网络接口](virtual-network-network-interface.md)，必须启用 IP 转发。 在外围网络子网中创建网络虚拟设备时，Azure 已自动创建名为 *myVmNvaVMNic* 网络接口，已将该网络接口附加到虚拟机，并且向网络接口分配了专用 IP 地址 *10.0.2.4*。 使用 [az network nic update](/cli/azure/network/nic#az_network_nic_update) 为网络接口启用 IP 转发。

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

在虚拟机中，操作系统或虚拟机中运行的应用程序也必须能够转发网络流量。 在生产环境中部署网络虚拟设备时，该设备通常会筛选、记录数据或执行其他某种功能，然后再转发流量。 但在本文中，操作系统只会转发它收到的所有流量。 使用 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) 在虚拟机的操作系统中启用 IP 转发，以便执行某个命令在操作系统中启用 IP 转发。

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
执行该命令最长需要花费一分钟时间。

### <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个虚拟机，以便可以在后续步骤中验证来自公共子网的流量是否通过网络虚拟设备路由到专用子网。 

使用 [az vm create](/cli/azure/vm#az_vm_create) 在公共子网中创建一个虚拟机。 `--no-wait` 参数支持 Azure 在后台中执行命令，因此可以继续执行下一个命令。 为了简化本文的内容，此处使用了密码。 在生产部署中通常使用密钥。 如果使用密钥，还必须配置 SSH 代理转发。 有关详细信息，请参阅 SSH 客户端的文档。 将以下命令中的 `<replace-with-your-password>` 替换为所选的密码。

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

在专用子网中创建一个虚拟机。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

创建虚拟机需花费几分钟的时间。 创建虚拟机后，Azure CLI 会显示类似以下示例的信息： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
记下 publicIpAddress。 在稍后的步骤中会使用此地址通过 Internet 访问虚拟机。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>通过网络虚拟设备路由流量

使用以下命令创建与 myVmMgmt 虚拟机的 SSH 会话。 将 *<publicIpAddress>* 替换为虚拟机的公共 IP 地址。 在上面的示例中，IP 地址为 *13.90.242.231*。

```bash 
ssh azureuser@<publicIpAddress>
```

当系统提示输入密码时，请输入在[创建虚拟机](#create-virtual-machines)中选择的密码。

使用以下命令在 *myVmMgmt* 虚拟机上安装 traceroute：

```bash 
sudo apt-get install traceroute
```

使用以下命令测试从 *myVmMgmt* 虚拟机发往 *myVmWeb* 虚拟机的网络流量的路由。

```bash
traceroute myvmweb
```

响应类似于以下示例：

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

可以看到，流量直接从 *myVmMgmt* 虚拟机路由到了 *myVmWeb* 虚拟机。 Azure 的默认路由，直接在子网之间路由流量。 

从 *myVmMgmt* 虚拟机使用以下命令通过 SSH 连接到 *myVmWeb* 虚拟机：

```bash 
ssh azureuser@myVmWeb
```

使用以下命令在 *myVmWeb* 虚拟机上安装 traceroute：

```bash 
sudo apt-get install traceroute
```

使用以下命令测试从 *myVmWeb* 虚拟机发往 *myVmMgmt* 虚拟机的网络流量的路由。

```bash
traceroute myvmmgmt
```

响应类似于以下示例：

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
可以看到，第一个跃点为 10.0.2.4，即网络虚拟设备的专用 IP 地址。 第二个跃点为 10.0.1.4，即 *myVmMgmt* 虚拟机的专用 IP 地址。 添加到 *myRouteTablePublic* 路由表并关联到公共子网的路由导致 Azure 通过 NVA 路由流量，而不是直接将流量路由到专用子网。

关闭与 *myVmWeb* 和 *myVmMgmt* 虚拟机建立的 SSH 会话。

## <a name="troubleshoot-routing"></a>排查路由问题

如前面的步骤所述，Azure 会应用默认路由，而你可以选择性地使用自己的路由覆盖默认路由。 有时，无法按预期方式路由流量。 使用 [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) 可以确定两个虚拟机之间的流量的路由方式。 例如，以下命令测试从 *myVmWeb* (10.0.0.4) 虚拟机到 *myVmMgmt* (10.0.1.4) 虚拟机的流量路由：

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
等待片刻后，会返回以下输出：

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

输出中指出，从 *myVmWeb* 到 *myVmMgmt* 的流量的下一跃点 IP 地址为 10.0.2.4（*myVmNva* 虚拟机），下一跃点类型为 *VirtualAppliance*，导致路由的路由表为 *myRouteTablePublic*。

每个网络接口的有效路由是 Azure 默认路由与定义的任何路由的组合。 使用 [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) 查看虚拟机中网络接口的所有有效路由。 例如，若要显示 *myVmWeb* 虚拟机中 *MyVmWebVMNic* 网络接口的有效路由，请输入以下命令：

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

随后将返回所有默认路由，以及在上一步骤中添加的路由。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

在本文中，我们创建了一个路由表并将其关联到了某个子网。 我们还创建了一个网络虚拟设备，用于将流量从公共子网路由到专用子网。 尽管可以在一个虚拟网络中部署多个 Azure 资源，但无法将某些 Azure PaaS 服务的资源部署到虚拟网络。 不过，仍可以限制为只允许来自某个虚拟网络子网的流量访问某些 Azure PaaS 服务的资源。 请继续学习下一篇教程，了解如何限制 Azure PaaS 资源的网络访问。

> [!div class="nextstepaction"]
> [限制 PaaS 资源的网络访问](virtual-network-service-endpoints-configure.md#azure-cli)
