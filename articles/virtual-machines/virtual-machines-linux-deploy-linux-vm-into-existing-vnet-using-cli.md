---
title: "将 Linux VM 部署到现有网络 - Azure CLI | Microsoft 文档"
description: "使用 CLI 将 Linux VM 部署到现有的虚拟网络。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: e64449991bc28427d8f559ed13c3bdf9160488db
ms.openlocfilehash: 93fa2521b81b423d663df6e04ef201839bca2814


---

# <a name="deploy-a-linux-vm-into-an-existing-azure-virtual-network-using-the-cli"></a>使用 CLI 将 Linux VM 部署到现有 Azure 虚拟网络

本文说明如何使用 CLI 标志将 VM 部署到现有的虚拟网络 (VNet)。  要求如下：

- [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)
- [SSH 公钥和私钥文件](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>快速命令

如果需要快速完成任务，以下部分详细介绍所需的命令。 本文档的余下部分（[从此处开始](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#detailed-walkthrough)）提供了每个步骤的更详细信息和上下文。

前提条件： 资源组、VNet、将 SSH 入站的 NSG、子网。 将任何示例替换为你自己的设置。

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>将 VM 部署到 VNet、NSG 并连接 VNic

```azurecli
azure vm create myVM \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-n myVM \
-F myVNet \
-j mySubnet \
-N myVNic
```

## <a name="detailed-walkthrough"></a>详细演练

建议选择静态的、长期存在的且部署频率极低的资源作为 Azure 资产，例如 VNet 和 NSG。  VNet 部署后即可供新的部署反复使用，不会对基础结构造成负面影响。  以充当传统硬件网络交换机的 VNet 为例，用户不需要每次部署都配置全新的硬件交换机。  有了正确配置的 VNet，即可持续不断地将新服务器部署到该 VNet 中，在 VNet 的整个生命周期中几乎不需要进行更改。

## <a name="create-the-resource-group"></a>创建资源组

首先需创建一个资源组，对我们在本演练中创建的所有内容进行组织。  有关 Azure 资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>创建 VNet

第一步是生成将 VM 启动到其中的 VNet。  该 VNet 包含本演练的一个子网。  有关 Azure VNet 的详细信息，请参阅[使用 Azure CLI 创建虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>创建 NSG

该子网在现有的网络安全组之后生成，因此我们在生成子网之前先生成 NSG。  Azure NSG 相当于网络层的防火墙。  有关 Azure NSG 的详细信息，请参阅[如何在 Azure CLI 中创建 NSG](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>添加入站 SSH 允许规则

Linux VM 需要从 Internet 进行访问，因此需要一个规则，允许端口 22 的入站流量通过网络传递到 Linux VM 上的端口 22。

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix Internet \
--source-port-range 22 \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>将子网添加到 VNet

VNet 中的 VM 必须位于子网中。  每个 VNet 可以有多个子网。  创建子网并将子网与 NSG 相关联，以便将防火墙添加到子网。

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

现在，子网已添加到 VNet 中并与 NSG 和 NSG 规则相关联。


## <a name="add-a-vnic-to-the-subnet"></a>将 VNic 添加到子网

虚拟网卡 (VNic) 很重要，因为只需将其连接到不同的 VM，即可重复使用，使得 VNic 始终为静态资源，而 VM 则可以是临时资源。  创建 VNic 并将其与上一步创建的子网相关联。

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>将 VM 部署到 VNet 和 NSG

我们现在有了一个 VNet、一个位于该 VNet 中的子网，以及一个充当防火墙的 NSG，该防火墙可以通过阻止所有入站流量（用于 SSH 的端口 22 除外）来保护子网。  现在可以将 VM 部署到这个现有的网络基础结构内。

可以使用 Azure CLI 和 `azure vm create` 命令将 Linux VM 部署到现有的 Azure 资源组、VNet、子网和 VNic。  若要详细了解如何使用 CLI 部署完整的 VM，请参阅[使用 Azure CLI 创建完整的 Linux 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create myVM \
--resource-group myResourceGroup \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name myVNic
```

使用 CLI 标志调用现有资源是为了指示 Azure 将 VM 部署到现有网络中。  再说一遍，部署 VNet 和子网以后，即可将其作为静态资源或永久资源留在 Azure 区域。  

## <a name="next-steps"></a>后续步骤

* [使用 Azure Resource Manager 模板创建特定部署](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Jan17_HO4-->


