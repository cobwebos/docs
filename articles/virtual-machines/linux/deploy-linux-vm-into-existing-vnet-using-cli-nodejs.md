---
title: "使用 Azure CLI 1.0 将 Linux VM 部署到现有网络 | Microsoft Docs"
description: "如何使用 Azure CLI 1.0 将 Linux VM 部署到现有虚拟网络"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10"></a>如何使用 Azure CLI 1.0 将 Linux 虚拟机部署到现有 Azure 虚拟网络

本文说明如何使用 Azure CLI 1.0 将虚拟机 (VM) 部署到现有虚拟网络 (VNet)。 要求如下：

- [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)
- [SSH 公钥和私钥文件](mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#quick-commands) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md) - 适用于资源管理部署模型的下一代 CLI


## <a name="quick-commands"></a>快速命令

如果需要快速完成任务，以下部分详细介绍所需的命令。 本文档的余下部分（[从此处开始](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough)）提供了每个步骤的更详细信息和上下文。

前提条件：资源组、VNet、SSH 入站的 NSG、子网。 将任何示例替换为你自己的设置。

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>将 VM 部署到虚拟网络基础结构

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## <a name="detailed-walkthrough"></a>详细演练

选择静态的、长期存在的且部署频率极低的资源作为 Azure 资产，例如 VNet 和网络安全组。 VNet 部署后即可供新的部署反复使用，不会对基础结构造成负面影响。 将 VNet 想像成传统硬件网络交换机。 不需要使用每个部署配置全新的硬件交换机。 有了正确配置的 VNet，即可持续不断地将新服务器部署到该 VNet 中，在 VNet 的整个生命周期中几乎不需要进行更改。

## <a name="create-the-resource-group"></a>创建资源组

首先需创建一个资源组，对本演练中创建的所有内容进行整理。 有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md)

```azurecli
azure group create myResourceGroup --location eastus
```

## <a name="create-the-vnet"></a>创建 VNet

第一步是生成将 VM 启动到其中的 VNet。 该 VNet 包含本演练的一个子网。 有关 Azure VNet 的详细信息，请参阅[使用 Azure CLI 创建虚拟网络](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## <a name="create-the-network-security-group"></a>创建网络安全组

该子网在现有的网络安全组之后生成，因此先生成网络安全组，再生成子网。 Azure 网络安全组相当于网络层防火墙。 有关 Azure 网络安全组的详细信息，请参阅[如何在 Azure CLI 中创建网络安全组](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)。

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>添加入站 SSH 允许规则

VM 需要从 Internet 进行访问，因此需要一个规则，允许端口 22 的入站流量通过网络传递到 VM 上的端口 22。

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

VNet 中的 VM 必须位于子网中。 每个 VNet 可以有多个子网。 创建子网并将关联到网络安全组。

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

现在，子网已添加到 VNet 中并与网络安全组和规则相关联。


## <a name="add-a-vnic-to-the-subnet"></a>将 VNic 添加到子网

虚拟网卡 (VNic) 可连接到不同的 VM 供用户重复使用，因此很重要。 此方法将 VNic 作为静态资源保存，而 VM 可以保存为临时资源。 创建 VNic 并将其与上一步创建的子网相关联。

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>将 VM 部署到 VNet 和 NSG

现在有一个 VNet，此 VNet 中有一个子网，还有一个网络安全组用于通过阻止所有入站流量（用于 SSH 的端口 22 除外）来保护子网。 现在可以将 VM 部署到这个现有的网络基础结构内。

可以使用 Azure CLI 和 `azure vm create` 命令将 Linux VM 部署到现有的 Azure 资源组、VNet、子网和 VNic。 若要详细了解如何使用 CLI 部署完整的 VM，请参阅[使用 Azure CLI 创建完整的 Linux 环境](create-cli-complete.md)

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

通过使用 CLI 标志调用现有资源，指示 Azure 将 VM 部署到现有网络中。 部署 VNet 和子网后，即可将其作为静态资源或永久资源保留在 Azure 区域中。  

## <a name="next-steps"></a>后续步骤

* [使用 Azure Resource Manager 模板创建特定部署](../windows/cli-deploy-templates.md)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](create-cli-complete.md)
* [使用模板在 Azure 上创建 Linux VM](create-ssh-secured-vm-from-template.md)

