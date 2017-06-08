---
title: "使用 Azure 门户将 Linux VM 部署到现有网络 | Microsoft Docs"
description: "使用门户将 Linux VM 部署到现有的 Azure 虚拟网络。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal"></a>如何使用 Azure 门户将 Linux 虚拟机部署到现有 Azure 虚拟网络

本文说明如何将虚拟机 (VM) 部署到现有虚拟网络 (VNet)。 需选择静态的、长期存在的且部署频率极低的资源作为 Azure 资产，例如 VNet 和网络安全组。 VNet 部署后即可供经常性的重复部署反复使用，不会对基础结构造成负面影响。 以充当传统硬件网络交换机的 VNet 为例，不需要每次部署时都配置全新的硬件交换机。  

有了正确配置的 VNet，即可持续不断地将新服务器部署到该 VNet 中，在 VNet 的整个生命周期中几乎不需要进行更改。

## <a name="create-the-resource-group"></a>创建资源组

首先需创建一个资源组，对本演练中创建的所有内容进行整理。 有关 Azure 资源组的详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md)

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>创建 VNet

接下来，生成将 VM 启动到其中的 VNet。 VNet 包含一个子网，后续步骤中会将网络安全组与此子网相关联。

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>将 VNic 添加到子网

虚拟网卡 (VNic) 可连接到不同的 VM，因此很重要。 此方法将 VNic 作为静态资源保存，而 VM 可以保存为临时资源。 创建 VNic 并将其与上一步创建的子网相关联。

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-network-security-group"></a>创建网络安全组

Azure 网络安全组相当于网络层防火墙。 有关 Azure 网络安全组的详细信息，请参阅[什么是网络安全组](../../virtual-network/virtual-networks-nsg.md)。

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>添加入站 SSH 允许规则

VM 需要从 Internet 进行访问，因此创建一个规则，允许端口 22 的入站流量通过网络传递到 VM 上的端口 22。

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>将 NSG 与子网相关联

使用 VNet 和创建的子网，可将网络安全组与子网相关联。 网络安全组可与整个子网或单个 VNic 相关联。 使用防火墙在子网级别筛选流量，即可通过网络安全组保护所有 VNic 以及子网中的 VM。 还可以将网络安全组与单个 VNic 关联，只保护一个 VM。

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>将 VM 部署到 VNet 和 NSG

可以使用 Azure 门户将 Linux VM 部署到现有的 Azure 资源组、VNet、子网和 VNic。

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

通过使用门户选择现有资源，指示 Azure 将 VM 部署到现有网络中。 部署 VNet 和子网后，即可将其作为静态资源或永久资源保留在 Azure 区域中。  

## <a name="next-steps"></a>后续步骤

* [使用 Azure Resource Manager 模板创建特定部署](../windows/cli-deploy-templates.md)
* [直接使用 Azure CLI 命令创建自定义的 Linux VM 环境](create-cli-complete.md)
* [使用模板在 Azure 上创建 Linux VM](create-ssh-secured-vm-from-template.md)

