---
title: "Azure 虚拟网络和 Linux 虚拟机 | Microsoft Docs"
description: "教程 - 使用 Azure CLI 管理 Azure 虚拟网络和 Linux 虚拟机"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 1ac628b606a198bb437c02d00467d48462c34334
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>使用 Azure CLI 管理 Azure 虚拟网络和 Linux 虚拟机

本教程介绍了如何在虚拟网络中创建多个虚拟机 (VM)，以及如何在虚拟机之间配置网络连接。 完成后，可从适用于 SSH 的端口 22 和适用于 HTTP 连接的端口 80 上的 Internet 访问“前端”VM。 具有 MySQL 数据库的“后端”VM 将被隔离，且只能从端口 3306 上的前端 VM 访问。

可使用最新版 [Azure CLI 2.0](/cli/azure/install-azure-cli) 完成本教程中的步骤。

## <a name="create-vm-and-vnet"></a>创建 VM 和 VNet

Azure 虚拟网络 (VNet) 是你自己的网络在云中的表示形式。 VNet 是对专用于你的订阅的 Azure 云进行的逻辑隔离。 在 VNet 中，可发现子网、连接到这些子网的规则，以及从 VM 到子网的连接。 Azure CLI 可用于轻松创建支持 VM 访问所需的网络相关资源。 

需使用 az group create 创建一个资源组，才可创建任何其他 Azure 资源。 以下示例在“westus”位置创建名为“myRGNetwork”的资源组：

```azurecli
az group create --name myRGNetwork --location westus
```

使用 Azure CLI 创建虚拟机时，同时自动创建其所需的网络资源。 使用 [az vm create](https://docs.microsoft.com/cli/azure/vm#create) 创建 myFrontendVM 及其支持的网络资源：

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

创建 VM 后，请记下公共 IP 地址。 本教程的后续步骤中将使用此地址：

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

已创建以下网络资源：

- myFrontendVMNSG - 用于保护到 myFrontendVM 的传入流量的网络安全组。
- myVMPublicIP - 实现通过 Internet 访问 myFrontendVM 的公共 IP 地址。
- myVMVMNic - 提供用于 myFrontendVM 的网络连接的虚拟网络接口。
- myVMVNET - myFrontendVM连接到的虚拟网络。

## <a name="install-web-server"></a>安装 Web 服务器

创建与 myFrontendVM 的 SSH 连接。 将示例 IP 地址替换为 VM 的公共 IP 地址：

```bash
ssh 40.68.254.142
```

运行以下命令安装 NGINX：

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

关闭 SSH 会话：

```bash
exit
```

## <a name="manage-internet-traffic"></a>管理 Internet 通信

网络安全组 (NSG) 包含一系列安全规则，这些规则可允许或拒绝流向连接到 VNet 的资源的网络流量。 NSG 可与子网或附加到 VM 的单个 NIC 相关联。 使用 NSG 规则打开或关闭通过端口到 VM 的访问。 创建 myFrontendVM 后，会自动打开入站端口 22 进行 SSH 连接。

使用 [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) 打开 myFrontendVM 上的端口 80：

```azurecli
az vm open-port --resource-group myRGNetwork --name myFrontendVM --port 80
```

现可浏览 VM 的公共 IP 地址以查看 NGINX 站点。

![NGINX 默认站点](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>管理内部流量

也可使用 NSG 配置 VM 的内部通信。 本部分介绍如何在网络中创建附加子网，并向其分配 NSG 以允许端口 3306 上 myFrontendVM 到 myBackendVM 的连接。 然后，在创建子网时将其分配给 VM。

使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) 添加名为“myBackendNSG”的网络安全组。 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

设置一个端口，使 myFrontendVM 和 myBackendVM 在 VNet 中相互通信。 使用 [az network rule create](/cli/azure/network/rule#create) 添加仅允许流量从 myVMSubnet 流入 myBackendSubnet 的 NSG 规则：

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>添加后端子网

子网是 VNet 的子资源，可帮助定义使用 IP 地址前缀在 CIDR 块中定义地址空间的段。 可以将 NIC 添加到子网，并连接到 VM，以便为各种工作负荷提供连接。

使用 [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) 将 myBackEndSubnet 添加到 myFrontendVMVNet：

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>创建后端 VM

通过 [az vm create](/cli/azure/vm#create) 使用 myBackendSubnet 创建 myBackendVM：

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>安装数据库

在本教程中，会将私钥从开发 VM 复制到 myFrontendVM。 在生产环境中，建议在创建 VM 时创建专门用于 VM 的密钥，而不是使用 --generate-ssh-keys。 

后端 VM 旨在不被公开访问。 本部分介绍如何使用 SSH 登录到 myFrontendVM，然后使用 SSH 从 myFrontendVM 登录到 myBackendVM。

将示例 IP 地址替换为 myFrontendVM 的公共 IP 地址：

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

创建与 myFrontendVM 的 SSH 连接。 将示例 IP 地址替换为 myFrontendVM 的公共 IP 地址：

```bash
ssh 40.68.254.142
```

从 myFrontendVM 中连接到 myBackendVM：

```bash
ssh myBackendVM
```

运行以下命令安装 MySQL：

```bash
sudo apt-get -y install mysql-server
```

按照说明设置 MySQL。

关闭 SSH 会话：

```bash
exit
```

安装 MySQL 以显示应用程序在 myBackendVM 上的安装方式，但本教程中并未使用。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建并保护与虚拟机相关的 Azure 网络。 前往下一教程以了解使用 Azure 安全中心监视 VM 的相关信息。

[管理虚拟机安全](./tutorial-azure-security.md)
