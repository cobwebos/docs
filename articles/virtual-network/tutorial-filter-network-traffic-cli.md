---
title: 筛选网络流量 - Azure CLI | Microsoft Docs
description: 本文介绍如何在 Azure CLI 中使用网络安全组筛选发往子网的网络流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: dfda95d2340d7dd57ac7b4d7ed7b0665c8e9294e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>在 Azure CLI 中使用网络安全组筛选网络流量

可以使用网络安全组来筛选虚拟网络子网的入站和出站网络流量。 网络安全组包含安全规则，这些规则可按 IP 地址、端口和协议筛选网络流量。 安全规则应用到子网中部署的资源。 在本文中，学习如何：

* 创建网络安全组和安全规则
* 创建虚拟网络并将网络安全组关联到子网
* 将虚拟机 (VM) 部署到子网中
* 测试流量筛选器

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 


## <a name="create-a-network-security-group"></a>创建网络安全组

网络安全组包含安全规则。 安全规则指定源和目标。 源和目标可以是应用程序安全组。

### <a name="create-application-security-groups"></a>创建应用程序安全组

首先使用 [az group create](/cli/azure/group#az_group_create) 针对本文中创建的所有资源创建一个资源组。 以下示例在 *eastus* 位置创建一个资源组： 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

使用 [az network asg create](/cli/azure/network/asg#az_network_asg_create) 创建应用程序安全组。 使用应用程序安全组可以分组具有类似端口筛选要求的服务器。 以下示例创建两个应用程序安全组。

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>创建网络安全组

使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 创建网络安全组。 以下示例创建名为 *myNsg* 的网络安全组： 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>创建安全规则

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 创建安全规则。 以下示例创建一个规则，该规则允许通过端口 80 和 443 将来自 Internet 的入站流量发往 *myWebServers* 应用程序安全组：

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

以下示例创建一个规则，该规则允许通过端口 22 将来自 Internet 的入站流量发往 *myMgmtServers* 应用程序安全组：

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

在本文中，将在 Internet 上为 *myAsgMgmtServers* VM 公开 SSH（端口 22）。 在生产环境中，我们建议使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或[专用](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)网络连接来连接到要管理的 Azure 资源，而不要向 Internet 公开端口 22。

## <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例创建名为 *myVirtualNetwork* 的虚拟网络：

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 将子网添加到虚拟网络。 以下示例将名为 *mySubnet* 的子网添加到虚拟网络，并将 *myNsg* 网络安全组关联到该虚拟网络：

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM，以便在后续步骤中可以验证流量筛选。 

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例创建充当 Web 服务器的 VM。 `--asgs myAsgWebServers` 选项导致 Azure 将它为 VM 创建的网络接口设置为 *myAsgWebServers* 应用程序安全组的成员。

指定 `--nsg ""` 选项可防止 Azure 为创建 VM 时创建的网络接口创建默认的网络安全组。 为了简化本文的内容，此处使用了密码。 在生产部署中通常使用密钥。 如果使用密钥，还必须配置 SSH 代理转发才能完成剩余步骤。 有关详细信息，请参阅 SSH 客户端的文档。 将以下命令中的 `<replace-with-your-password>` 替换为所选的密码。

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

创建 VM 需要几分钟时间。 创建 VM 后，将返回以下示例所示的输出： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

记下 publicIpAddress。 在后面的步骤中会使用此地址通过 Internet 访问 VM。  创建充当管理服务器的 VM：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

创建 VM 需要几分钟时间。 创建 VM 后，记下返回的输出中的 **publicIpAddress**。 在后续步骤中，将使用此地址访问 VM。 请 Azure 创建完 VM 之前，请不要继续下一步。

## <a name="test-traffic-filters"></a>测试流量筛选器

使用以下命令来与 *myVmMgmt* VM 建立 SSH 会话。 将 *<publicIpAddress>* 替换为 VM 的公共 IP 地址。 在上面的示例中，IP 地址为 *13.90.242.231*。

```bash 
ssh azureuser@<publicIpAddress>
```

当系统提示输入密码时，输入在[创建 VM](#create-virtual-machines) 中输入的密码。

连接将会成功，因为允许通过端口 22 将入站流量从 Internet 发往已附加到 *myVmMgmt* VM 的网络接口所在的 *myAsgMgmtServers* 应用程序安全组。

使用以下命令通过 SSH 从 *myVmMgmt* VM 连接到 *myVmWeb* VM：

```bash 
ssh azureuser@myVmWeb
```

连接将会成功，因为每个网络安全组中的默认安全规则允许通过虚拟网络中所有 IP 地址之间的所有端口发送流量。 无法通过 SSH 从 Internet 连接到 *myVmWeb* VM，因为 *myAsgWebServers* 的安全规则不允许通过端口 22 发送来自 Internet 的入站流量。

使用以下命令在 *myVmWeb* VM 上安装 nginx Web 服务器：

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

允许 *myVmWeb* VM 向 Internet 发送出站流量以检索 nginx，因为默认安全规则允许发往 Internet 的所有出站流量。 退出 *myVmWeb* SSH 会话。随即会在 *myVmMgmt* VM 的 `username@myVmMgmt:~$` 提示符下退出。 若要从 *myVmWeb* VM 检索 nginx 欢迎屏幕，请输入以下命令：

```bash
curl myVmWeb
```

从 *myVmMgmt* VM 注销。 若要确认是否可以从 Azure 外部访问 *myVmWeb* Web 服务器，请在自己的计算机上输入 `curl <publicIpAddress>`。 连接将会成功，因为允许通过端口 80 将入站流量从 Internet 发往已附加到 *myVmWeb* VM 的网络接口所在的 *myAsgWebServers* 应用程序安全组。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建一个网络安全组并将其关联到虚拟网络子网。 若要详细了解网络安全组，请参阅[网络安全组概述](security-overview.md)和[管理网络安全组](virtual-network-manage-nsg-arm-ps.md)。

默认情况下，Azure 在子网之间路由流量。 你也可以选择通过某个 VM（例如，充当防火墙的 VM）在子网之间路由流量。 若要了解操作方法，请参阅[创建路由表](tutorial-create-route-table-cli.md)。