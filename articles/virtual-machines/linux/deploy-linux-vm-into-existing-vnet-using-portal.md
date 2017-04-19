---
title: "将 Linux VM 部署到现有网络 - Azure 门户 | Microsoft 文档"
description: "使用门户将 Linux VM 部署到现有的 Azure 虚拟网络。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 27704aa20a4514cfca200e70ffcc894059207281
ms.lasthandoff: 04/03/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>使用门户将 Linux VM 部署到现有的 VNet 和 NSG

本文介绍如何将 VM 部署到现有的虚拟网络 (VNet)。  建议选择静态的、长期存在的且部署频率极低的资源作为 Azure 资产，例如 VNet 和 NSG（网络安全组）。  VNet 部署后即可供经常性的重复部署反复使用，不会对基础结构造成负面影响。  以充当传统硬件网络交换机的 VNet 为例，用户不需要每次部署都配置全新的硬件交换机。  

有了正确配置的 VNet，即可持续不断地将新服务器部署到该 VNet 中，在 VNet 的整个生命周期中几乎不需要进行更改。

## <a name="create-the-resource-group"></a>创建资源组

首先部署一个资源组，对我们在本演练中创建的所有内容进行组织。  有关 Azure 资源组的详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>创建 VNet

第一步是生成将 VM 启动到其中的 VNet。  VNet 包含一个子网，我们会在后面的步骤中将 NSG 与此子网相关联。

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>将 VNic 添加到子网

虚拟网卡 (VNic) 很重要，因为用户可以将其连接到不同的 VM，使得 VNic 始终为静态资源，而 VM 则可以是临时资源。 创建 VNic 并将其与上一步创建的子网相关联。

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>创建 NSG

Azure NSG 相当于网络层的防火墙。 有关 Azure NSG 的详细信息，请参阅[什么是网络安全组](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>添加入站 SSH 允许规则

Linux VM 需要从 Internet 进行访问，因此需创建一个规则，允许端口 22 的入站流量通过网络传递到 Linux VM 上的端口 22。

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>将 NSG 与子网相关联

使用 VNet 和创建的子网，我们可以将 NSG 与子网相关联。  NSG 可以与整个子网或单个 VNic 相关联。  使用防火墙在子网级别筛选流量，即可通过 NSG 保护所有 VNic 以及子网中的 VM，而如果 NSG 只与一个 VNic 相关联，则只能保护一个 VM。

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>将 VM 部署到 VNet 和 NSG

可以使用 Azure 门户将 Linux VM 部署到现有的 Azure 资源组、VNet、子网和 VNic。

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

使用门户选择现有资源是为了指示 Azure 将 VM 部署到现有网络中。  再说一遍，部署 VNet 和子网以后，即可将其作为静态资源或永久资源留在 Azure 区域。  

## <a name="next-steps"></a>后续步骤

* [使用 Azure Resource Manager 模板创建特定部署](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用模板在 Azure 上创建 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

