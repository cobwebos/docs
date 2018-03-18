---
title: "创建包含多个子网的 Azure 虚拟网络 - Azure CLI | Microsoft Docs"
description: "了解如何使用 Azure CLI 创建包含多个子网的虚拟网络。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>使用 Azure CLI 创建包含多个子网的虚拟网络

虚拟网络能让几种类型的 Azure 资源与 Internet 通信以及互相私下通信。 在虚拟网络中创建多个子网即可对网络进行划分，以便筛选或控制子网之间的通信流。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建子网
> * 测试虚拟机之间的网络通信

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli.md)。 

## <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例使用地址前缀 *10.0.0.0/16* 创建一个名为 *myVirtualNetwork* 的虚拟网络。 该命令使用地址前缀 *10.0.0.0/24* 创建一个名为 *Public* 的子网。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

由于前一命令未指定位置，Azure 将在 *myResourceGroup* 资源组所在的同一位置创建虚拟网络。 **地址前缀**和**子网前缀**以 CIDR 表示法指定。 指定的地址前缀包括 IP 地址 10.0.0.0-10.0.255.254。 指定的子网前缀必须在为虚拟网络定义的地址前缀内。 Azure DHCP 从子网地址前缀将 IP 地址分配给子网中部署的资源。 Azure 只将地址 10.0.0.4-10.0.0.254 分配给 **Public** 子网中部署的资源，因为 Azure 会在每个子网中保留前四个地址（10.0.0.0-10.0.0.3，用于此示例中的子网）和最后一个地址（10.0.0.255，用于此示例中的子网）。

## <a name="create-a-subnet"></a>创建子网

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 创建子网。 以下示例使用地址前缀 *10.0.1.0/24* 在 *myVirtualNetwork* 虚拟网络中创建一个名为 *Private* 的子网。 地址前缀必须在为虚拟网络定义的地址前缀内，并且不能与虚拟网络中的任何其他子网的地址前缀重叠。

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

在部署 Azure 虚拟网络和子网供生产用之前，建议你全面了解地址空间[注意事项](manage-virtual-network.md#create-a-virtual-network)和[虚拟网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 将资源部署到子网后，如果对虚拟网络和子网进行某些更改（例如更改地址范围），则可能需要重新部署子网中部署的现有 Azure 资源。

## <a name="test-network-communication"></a>测试网络通信

虚拟网络能让几种类型的 Azure 资源与 Internet 进行通信以及彼此之间私下通信。 虚拟机是一种能部署到虚拟网络的资源类型。 在虚拟网络中创建两个虚拟机，以便在稍后的步骤中测试其与 Internet 的通信。

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 以下示例在 *Public* 子网中创建一个名为“myVmWeb”的虚拟机。 `--no-wait` 参数支持 Azure 在后台中执行命令，因此可以继续执行下一个命令。 为了简化本教程，将使用密码。 在生产部署中通常使用密钥。 如果使用密钥，还必须配置 SSH 代理转发才能完成剩余步骤。 有关 SSH 代理转发的详细信息，请参阅 SSH 客户端的文档。 将以下命令中的 `<replace-with-your-password>` 替换为所选的密码。

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

Azure 会自动分配 10.0.0.4 作为虚拟机的专用 IP 地址，因为 10.0.0.4 是 *Public* 子网中的第一个可用 IP 地址。 

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
创建虚拟机需花费几分钟的时间。 创建虚拟机后，Azure CLI 会返回类似以下示例的输出： 

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

在示例输出中，可以看到 privateIpAddress 是 10.0.1.4。 Azure 已创建[网络接口](virtual-network-network-interface.md)，已将其附加到虚拟机，已为网络接口分配专用 IP 地址和 **macAddress**。 Azure DHCP 自动将 10.0.1.4 分配到网络接口，因为它是“Private”子网中的第一个可用 IP 地址。 专用 IP 和 MAC 地址将保持分配给网络接口，直到删除网络接口。 

记下 publicIpAddress。 在稍后的步骤中会使用此地址通过 Internet 访问虚拟机。 尽管虚拟机不需要分配有公用 IP 地址，但默认情况下 Azure 仍会为你创建的每个虚拟机分配一个公用 IP 地址。 若要通过 Internet 与虚拟机进行通信，必须为虚拟机分配公用 IP 地址。 所有虚拟机都可以与 Internet 进行出站通信，无论是否为虚拟机分配了公用 IP 地址。 若要详细了解 Azure 中的出站 Internet 连接，请参阅 [Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

本文中创建的虚拟机具有一个[网络接口](virtual-network-network-interface.md)，该网络接口具有一动态分配给它的 IP 地址。 部署 VM 后，可以[添加多个公用 IP 地址和专用 IP 地址，或将 IP 地址分配方法更改为“静态”](virtual-network-network-interface-addresses.md#add-ip-addresses)。 可以[添加网络接口](virtual-network-network-interface-vm.md#vm-add-nic)，但其数目不能超过创建虚拟机时选择的 [VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)所允许的限制。 还可以为 VM [启用单根 I/O 虚拟化 (SR-IOV)](create-vm-accelerated-networking-cli.md)，但前提是创建的 VM 的 VM 大小支持该功能。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>虚拟机之间的通信以及虚拟机与 Internet 的通信

使用以下命令创建与 myVmMgmt 虚拟机的 SSH 会话。 将 `<publicIpAddress>` 替换为虚拟机的公共 IP 地址。 在前一示例中，公共 IP 地址为 *13.90.242.231*。 当系统提示输入密码时，输入在[创建虚拟机](#create-virtual-machines)中输入的密码。

```bash 
ssh azureuser@<publicIpAddress>
```

由于安全原因，通常会限制虚拟网络中可远程连接到的虚拟机数。 在本教程中，*myVmMgmt* 虚拟机用于管理虚拟网络中的 *myVmWeb* 虚拟机。 从 *myVmMgmt* 虚拟机使用以下命令通过 SSH 连接到 *myVmWeb* 虚拟机：

```bash 
ssh azureuser@myVmWeb
```

若要从 *myVmWeb* 虚拟机与 *myVmMgmt* 虚拟机进行通信，请在命令提示符下输入以下命令：

```
ping -c 4 myvmmgmt
```

收到的输出类似于以下示例输出：
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
可以看到 *myVmMgmt* 虚拟机的地址是 10.0.1.4。 10.0.1.4 是在上一步中将 *myVmMgmt* 虚拟机部署到的 *Private* 子网的地址范围中的第一个可用 IP 地址。  可看到该虚拟机的完全限定域名是 *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*。 尽管该域名的 *hxehizax3z1udjnrx1r4gr30pg* 部分与你的虚拟机域名不同，但域名的其余部分是相同的。 默认情况下，所有 Azure 虚拟机均使用默认 Azure DNS 服务。 虚拟网络中的所有虚拟机均可使用 Azure 的默认 DNS 服务解析同一虚拟网络中所有其他虚拟机的名称。 可以不使用 Azure 的默认 DNS 服务，而使用自己的 DNS 服务器或 Azure DNS 服务的专用域功能。 有关详细信息，请参阅[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)或[对专用域使用 Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

使用以下命令在 *myVmWeb* 虚拟机上安装 nginx Web 服务器：

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

安装 nginx 后，关闭 *myVmWeb* SSH 会话，这会让你处于 *myVmMgmt* 虚拟机的提示符下。 从 *myVmWeb* 虚拟机输入以下命令以检索 nginx 欢迎屏幕。

```bash
curl myVmWeb
```

此时将返回 nginx 欢迎屏幕。

关闭与 *myVmMgmt* 虚拟机的 SSH 会话。

Azure 创建 *myVmWeb* 虚拟机时，也会创建名为 *myVmWebPublicIP* 的公共 IP 地址并将其分配给该虚拟机。 使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 获取 Azure 分配的公共地址。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

在自己的计算机中输入以下命令，并将 `<publicIpAddress>` 替换为从前一命令返回的地址：

```bash
curl <publicIpAddress>
```

如果尝试从自己的计算机对 nginx 欢迎屏幕执行 curl 命令，会失败。 尝试之所以会失败是因为，默认情况下，在部署虚拟机时，Azure 已为每个虚拟机创建网络安全组。 

网络安全组包含安全规则，这些规则可根据端口和 IP 地址允许或拒绝入站和出站网络流量。 Azure 创建的默认网络安全组允许通过同一虚拟网络中资源之间的所有端口进行通信。 对于 Linux 虚拟机，默认网络安全组拒绝通过除 TCP 端口 22 (SSH) 以外的其他所有端口来自 Internet 的所有入站流量。 因此，默认情况下，还可以从 Internet 直接创建与 *myVmWeb* 虚拟机的 SSH 会话，即使你可能不想将端口 22 向 Web 服务器开放。 由于 `curl` 命令通过端口 80 进行通信，之所以从 Internet 通信失败是因为，默认网络安全组中没有允许通过端口 80 的流量的规则。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何部署具有多个子网的虚拟网络。 你还了解了创建 Linux 虚拟机时，Azure 将创建一个网络接口并将它附加到该虚拟机，并且还创建一个只允许通过端口 22 来自 Internet 的流量的网络安全组。 学习下一个教程，了解如何筛选流向子网（而不是流向单个虚拟机）的网络流量。

> [!div class="nextstepaction"]
> [筛选流向子网的网络流量](./virtual-networks-create-nsg-arm-cli.md)
