---
title: 通过 Azure CLI 2.0 使用内部 DNS 进行 VM 名称解析 | Microsoft 文档
description: 如何创建虚拟网络接口卡，以及如何通过 Azure CLI 2.0 在 Azure 上使用内部 DNS 进行 VM 名称解析
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: 8df9035cf4a5e62102353c701625526e211b7282
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>创建虚拟网络接口卡，以及在 Azure 上使用内部 DNS 进行 VM 名称解析
本文介绍了如何通过 Azure CLI 2.0 使用虚拟网络接口卡 (vNic) 和 DNS 标签名称为 Linux VM 设置静态内部 DNS 名称。 还可以使用 [Azure CLI 1.0](static-dns-name-resolution-for-linux-on-azure-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。 静态 DNS 名称用于永久基础结构服务，如本文档所使用的 Jenkins 生成服务器或 Git 服务器。

要求如下：

* [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 公钥和私钥文件](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>快速命令
如果需要快速完成任务，以下部分详细介绍所需的命令。 本文档的余下部分（[从此处开始](#detailed-walkthrough)）提供了每个步骤的更详细信息和上下文。 若要执行这些步骤，需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure 帐户。

先决条件：资源组、虚拟网络和子网、带 SSH 入站的网络安全组。

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>使用静态内部 DNS 名称创建虚拟网络接口卡
使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建 vNic。 `--internal-dns-name` CLI 标志用于设置 DNS 标签，该标签为虚拟网络接口卡 (vNic) 提供静态 DNS 名称。 以下示例创建名为 `myNic` 的 vNic，将其连接到 `myVnet` 虚拟网络，并创建名为 `jenkins` 的内部 DNS 名称记录：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>部署 VM 并连接 vNic
使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 在部署到 Azure 期间，`--nics` 标志将 VNic 连接到 VM。 以下示例使用 Azure 托管磁盘创建名为 `myVM` 的 VM，并附加上一步中名为 `myNic` 的 vNic：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>详细演练

Azure 上的完整持续集成和持续部署 (CiCd) 基础结构需要某些服务器是静态服务器或长期存在的服务器。 建议选择静态的、长期存在的且部署频率极低的资源作为 Azure 资产，例如虚拟网络和网络安全组。 部署虚拟网络后，新部署可以重复使用它，而不会对基础结构产生任何负面影响。 可以稍后添加 Git 存储库服务器或 Jenkins 自动化服务器将 CiCd 提供给此虚拟网络，以用于开发或测试环境。  

内部 DNS 名称仅在 Azure 虚拟网络内可解析。 由于 DNS 名称是内部类，因此它们无法解析到外部 Internet，从而为基础结构提供了附加安全性。

在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`myNic` 和 `myVM`。

## <a name="create-the-resource-group"></a>创建资源组
首先，使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 `westus` 位置创建名为 `myResourceGroup` 的资源组：

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>创建虚拟网络

下一步是构建虚拟网络，以便在其中启动 VM。 该虚拟网络包含本演练的一个子网。 有关 Azure 虚拟网络的详细信息，请参阅[创建虚拟网络](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)。 

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例创建一个名为 `myVnet` 的虚拟网络和名为 `mySubnet` 的子网：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>创建网络安全组
Azure 网络安全组相当于网络层防火墙。 有关网络安全组的详细信息，请参阅[如何在 Azure CLI 中创建 NSG](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 创建网络安全组。 以下示例创建名为 `myNetworkSecurityGroup` 的网络安全组：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>添加入站规则以允许 SSH
使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 为网络安全组添加入站规则。 以下示例创建一个名为 `myRuleAllowSSH` 的规则：

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>将子网与网络安全组相关联
要将子网与网络安全组相关联，请使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)。 以下示例将名为 `mySubnet` 的子网与名为 `myNetworkSecurityGroup` 的网络安全组相关联：

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>创建虚拟网络接口卡和静态 DNS 名称
Azure 非常灵活，但若要使用 DNS 名称进行 VM 名称解析，则需要创建包含 DNS 标签的虚拟网络接口卡 (vNic)。 可通过基础结构生命周期将 VNic 连接到不同的 VM 供用户重复使用，因此 VNic 很重要。 此方法将 vNic 作为静态资源保存，而 VM 可以保存为临时资源。 通过在 VNic 上使用 DNS 标签，我们能够从 VNet 中的其他 VM 启用简单名称解析。 使用可解析名称可使其他 VM 能够通过 DNS 名称 `Jenkins` 或作为 `gitrepo` 的 Git 服务器访问自动化服务器。  

使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建 vNic。 以下示例创建名为 `myNic` 的 vNic，将其连接到名为 `myVnet` 的 `myVnet` 虚拟网络，并创建名为 `jenkins` 的内部 DNS 名称记录：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>将 VM 部署到虚拟网络基础结构
我们现在已有一个虚拟网络和子网、一个充当防火墙的网络安全组（该网络安全组可以通过阻止所有入站流量（用于 SSH 的端口 22 除外）来保护子网）以及一个 vNic。 现在可以在这个现有的网络基础结构内部署一个 VM。

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例使用 Azure 托管磁盘创建名为 `myVM` 的 VM，并附加上一步中名为 `myNic` 的 vNic：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

使用 CLI 标志调用现有资源是为了指示 Azure 将 VM 部署到现有网络中。 再说一遍，部署 VNet 和子网以后，即可将其作为静态资源或永久资源留在 Azure 区域。  

## <a name="next-steps"></a>后续步骤
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
