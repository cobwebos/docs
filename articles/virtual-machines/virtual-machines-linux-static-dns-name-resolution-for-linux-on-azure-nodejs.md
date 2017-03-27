---
title: "在 Azure 上使用内部 DNS 进行 VM 名称解析 | Microsoft Docs"
description: "在 Azure 上使用内部 DNS 进行 VM 名称解析。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 1aeb983730f732a021b828c658cc741f8659c487
ms.openlocfilehash: caae05d89bc0375e08418a015150726e2a6393b9
ms.lasthandoff: 02/27/2017


---

# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>在 Azure 上使用内部 DNS 进行 VM 名称解析

本文说明如何使用虚拟 NIC 卡 (VNic) 和 DNS 标签名称为 Linux VM 设置静态内部 DNS 名称。 静态 DNS 名称用于永久基础结构服务，如本文档所使用的 Jenkins 生成服务器或 Git 服务器。

要求如下：

* [一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 公钥和私钥文件](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#quick-commands) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](virtual-machines-linux-static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于资源管理部署模型的下一代 CLI


## <a name="quick-commands"></a>快速命令

如果需要快速完成任务，以下部分详细介绍所需的命令。 本文档的余下部分（[从此处开始](#detailed-walkthrough)）提供了每个步骤的更详细信息和上下文。  

前提条件：资源组、VNet、SSH 入站的 NSG、子网。

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>使用静态内部 DNS 名称创建 VNic

`-r` cli 标志用于设置 DNS 标签，该标签为 VNic 提供静态 DNS 名称。

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>将 VM 部署到 VNet、NSG 中并连接 VNic

在部署到 Azure 期间，`-N` 将 VNic 连接到新 VM。

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>详细演练

Azure 上的完整持续集成和持续部署 (CiCd) 基础结构需要某些服务器是静态服务器或长期存在的服务器。  建议虚拟网络 (VNet) 和网络安全组 (NSG) 等 Azure 资产应该是静态的、很少部署的长期存在资源。  VNet 部署后即可供新的部署反复使用，不会对基础结构造成负面影响。  向此静态网络添加 Git 存储库服务器和 Jenkins 自动化服务器可将 CiCd 提供给开发或测试环境。  

内部 DNS 名称仅在 Azure 虚拟网络内可解析。  由于 DNS 名称是内部类，因此它们无法解析到外部 Internet，从而为基础结构提供了附加安全性。

_将任何示例替换为自己的命名。_

## <a name="create-the-resource-group"></a>创建资源组

需要使用资源组来组织在本演练中创建的所有内容。  有关 Azure 资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

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
--source-address-prefix * \
--source-port-range * \
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

## <a name="creating-static-dns-names"></a>创建静态 DNS 名称

Azure 非常灵活，但若要使用 DNS 名称进行 VM 名称解析，需要使用 DNS 标签将它们创建为虚拟网卡 (VNic)。  VNic 很重要，因为用户可以通过将它们连接到不同的 VM 重新使用它们，这使 VNic 保持作为静态资源，而 VM 可以是临时 VM。  通过在 VNic 上使用 DNS 标签，我们能够从 VNet 中的其他 VM 启用简单名称解析。  使用可解析名称可使其他 VM 能够通过 DNS 名称 `Jenkins` 或作为 `gitrepo` 的 Git 服务器访问自动化服务器。  创建 VNic 并将其与上一步创建的子网相关联。

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>将 VM 部署到 VNet 和 NSG

我们现在有了一个 VNet、一个位于该 VNet 中的子网，以及一个充当防火墙的 NSG，该防火墙可以通过阻止所有入站流量（用于 SSH 的端口 22 除外）来保护子网。  现在可以将 VM 部署到这个现有的网络基础结构内。

可以使用 Azure CLI 和 `azure vm create` 命令将 Linux VM 部署到现有的 Azure 资源组、VNet、子网和 VNic。  若要详细了解如何使用 CLI 部署完整的 VM，请参阅[使用 Azure CLI 创建完整的 Linux 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

使用 CLI 标志调用现有资源是为了指示 Azure 将 VM 部署到现有网络中。  再说一遍，部署 VNet 和子网以后，即可将其作为静态资源或永久资源留在 Azure 区域。  

## <a name="next-steps"></a>后续步骤

* [使用 Azure Resource Manager 模板创建特定部署](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

