---
title: "在 Azure 中创建具有多个 NIC 的 Linux VM | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 或 Resource Manager 模板创建具有多个 NIC 的 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 61d50f0abce0fb5c8d0b82652b488d9b79978ca8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>如何在 Azure 中创建具有多个网络接口卡的 Linux 虚拟机
可以在 Azure 中创建附有多个虚拟网络接口 (NIC) 的虚拟机 (VM)。 一种常见方案是为前端和后端连接使用不同子网，或为监视或备份解决方案使用一个专用网络。 本文详细介绍如何创建具有多个 NIC 的 VM，以及如何在现有 VM 中添加或删除 NIC。 有关详细信息（包括如何在自己的 Bash 脚本中创建多个 NIC），请阅读 [deploying multi-NIC VMs](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md)（部署具有多个 NIC 的 VM）。 不同的 [VM 大小](sizes.md)支持不同数目的 NIC，因此请相应地调整 VM 的大小。

本文详述了如何使用 Azure CLI 2.0 创建具有多个 NIC 的 VM。 还可以使用 [Azure CLI 1.0](multiple-nics-nodejs.md) 执行这些步骤。


## <a name="create-supporting-resources"></a>创建支持资源
安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 myResourceGroup、mystorageaccount 和 myVM。

首先，使用 [az group create](/cli/azure/group#create) 创建资源组。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#create) 创建虚拟网络。 以下示例创建一个名为 myVnet 的虚拟网络和一个名为 mySubnetFrontEnd 的子网：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#create) 为后端通信流创建子网。 以下示例创建名为 mySubnetBackEnd 的子网：

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

使用 [az network nsg create](/cli/azure/network/nsg#create) 创建网络安全组。 以下示例创建名为“myNetworkSecurityGroup”的网络安全组：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>创建和配置多个 NIC
使用 [az network nic create](/cli/azure/network/nic#create) 创建两个 NIC。 以下示例创建两个连接到网络安全组的 NIC（名为 myNic1 和 myNic2），其中一个 NIC 连接到每个子网：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>创建 VM 并附加 NIC
创建 VM 时，指定使用 `--nics` 创建的 NIC。 还需要谨慎选择 VM 的大小。 可添加到 VM 的 NIC 数目有限制。 详细了解 [Linux VM 大小](sizes.md)。 

使用 [az vm create](/cli/azure/vm#create) 创建 VM。 以下示例创建一个名为 myVM 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>将 NIC 添加到 VM
之前的步骤创建了具有多个 NIC 的 VM。 还可使用 Azure CLI 2.0 将 NIC 添加到现有 VM。 不同的 [VM 大小](sizes.md)支持不同数目的 NIC，因此请相应地调整 VM 的大小。 如果需要，可[调整 VM 的大小](change-vm-size.md)。

使用 [az network nic create](/cli/azure/network/nic#create) 创建另一 NIC。 以下示例创建一个名为 myNic3 的 NIC，该 NIC 连接到后端子网和之前步骤中创建的网络安全组：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

要将 NIC 添加到现有 VM，请先使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配 VM。 以下示例解除分配名为 myVM 的 VM ：


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

使用 [az vm nic add](/cli/azure/vm/nic#add) 添加 NIC。 以下示例将 myNic3 添加到 myVM：

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

使用 [az vm start](/cli/azure/vm#start) 启动 VM：

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>从 VM 中删除 NIC
若要从现有 VM 删除 NIC，请先使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配 VM。 以下示例解除分配名为 myVM 的 VM ：

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

使用 [az vm nic remove](/cli/azure/vm/nic#remove) 删除 NIC。 以下示例从 myVM 删除 myNic3：

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

使用 [az vm start](/cli/azure/vm#start) 启动 VM：

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>使用 Resource Manager 模板创建多个 NIC
Azure Resource Manager 模板使用声明性 JSON 文件来定义环境。 可以阅读 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md)。 资源管理器模板可让你在部署期间创建资源的多个实例，例如，创建多个 NIC。 使用 *copy* 指定要创建的实例数：

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

阅读有关[使用 *copy* 创建多个实例](../../resource-group-create-multiple.md)的详细信息。 

也可以使用 `copyIndex()` 并在资源名称中追加一个数字，来创建 `myNic1`、`myNic2`，等等。下面显示了追加索引值的示例：

```json
"name": "[concat('myNic', copyIndex())]", 
```

可以阅读[使用 Resource Manager 模板创建多个 NIC](../../virtual-network/virtual-network-deploy-multinic-arm-template.md) 的完整示例。


## <a name="configure-guest-os-for-multiple-nics"></a>为多个 NIC 配置来宾 OS
将多个 NIC 添加到一个 Linux VM 时，需要创建路由规则。 这些规则允许此 VM 发送和接收属于特定 NIC 的流量。 否则，所定义的默认路由无法正确处理属于 eth1 等的流量。

若要纠正此路由问题，请首先向 /etc/iproute2/rt_tables 添加两个路由表，如下所示：

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

要使更改长久有效并在网络堆栈激活期间应用，请编辑 /etc/sysconfig/network-scipts/ifcfg-eth0 和 /etc/sysconfig/network-scipts/ifcfg-eth1。 将行“NM_CONTROLLED=yes”更改为“NM_CONTROLLED=no”。 如不执行此步骤，则不会自动应用其他规则/路由。
 
然后扩展路由表。 假设已进行了如下设置：

*路由*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*接口*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

然后，创建以下文件，并对每个文件添加相应的规则和路由：

- /etc/sysconfig/network-scripts/rule-eth0

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- /etc/sysconfig/network-scripts/route-eth0

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- /etc/sysconfig/network-scripts/rule-eth1

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- /etc/sysconfig/network-scripts/route-eth1

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

若要应用更改，请重启网络服务，如下所示：

```bash
systemctl restart network
```

路由规则现已准备就绪，可根据需要连接到任一接口。


## <a name="next-steps"></a>后续步骤
尝试创建具有多个 NIC 的 VM 时，请查看 [Lnux VM 大小](sizes.md)。 注意每个 VM 大小支持的 NIC 数目上限。 
