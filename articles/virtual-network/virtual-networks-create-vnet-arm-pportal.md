---
title: "创建虚拟网络 - Azure 门户 | Microsoft Docs"
description: "了解如何使用 Azure 门户创建虚拟网络。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 988510350ceb97a15bb305edff397a0e3212a89d
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>使用 Azure 门户创建虚拟网络

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有两个部署模型：Azure Resource Manager 模型和经典模型。 Microsoft 建议通过 Resource Manager 部署模型创建资源。 若要详细了解这两个模型之间的差别，请阅读 [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md)（了解 Azure 部署模型）一文。
 
本文说明如何使用 Azure 门户通过 Resource Manager 部署模型创建 VNet。 此外，也可以使用其他工具通过 Resource Manager 创建 VNet，或者从以下列表中选择一个不同的选项，通过经典部署模型创建 VNet：

> [!div class="op_single_selector"]
> * [门户](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [模板](virtual-networks-create-vnet-arm-template-click.md)
> * [门户 经典）](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell（经典）](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI（经典）](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>创建虚拟网络

若要使用 Azure 门户创建虚拟网络，请完成以下步骤：

1. 从浏览器导航到 http://portal.azure.com，并在必要时用 Azure 帐户登录。
2. 单击“新建” > “网络” > “虚拟网络”，如下图所示：

    ![新建虚拟网络](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. 在出现的“虚拟网络”边栏选项卡中，确保已选中“Resource Manager”并单击“创建”，如下图所示：

    ![虚拟网络](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. 在出现的“创建虚拟网络”边栏选项卡中，为“名称”输入 *TestVNet*，为“地址空间”输入 *192.168.0.0/16*，为“子网名称”输入 *FrontEnd*，为“子网地址范围”输入 *192.168.1.0/24*，为“资源组”输入 *TestRG*，选择你的**订阅**和**位置**，然后单击“创建”按钮，如下图所示：

    ![创建虚拟网络](./media/virtual-network-create-vnet-arm-pportal/3.png)

    也可以选择现有的资源组。 若要了解有关资源组的详细信息，请参阅 [Resource Manager 概述](../azure-resource-manager/resource-group-overview.md#resource-groups)一文。 也可以选择不同的位置。 若要了解有关 Azure 位置和区域的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions)一文。

5. 在门户中创建 VNet 时，只能创建一个子网。 在这种情况下，必须在创建 VNet 之后创建第二个子网。 若要创建第二个子网，请单击“所有资源”，然后在“所有资源”边栏选项卡中单击“TestVNet”，如下图所示：

    ![创建的 VNet](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. 在出现的“TestVNet”边栏选项卡中，依次单击“子网”、“+子网”，接着在“添加子网”边栏选项卡中为“名称”输入 *BackEnd*，为“地址范围”输入 *192.168.2.0/24*，然后单击“确定”，如下图所示：

    ![添加子网](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. 如下图所示，已列出两个子网：
    
    ![VNet 中的子网列表](./media/virtual-network-create-vnet-arm-pportal/6.png)

本文已介绍如何创建包含两个子网的测试用虚拟网络。 在为生产用途创建虚拟网络之前，我们建议阅读文章[虚拟网络概述](virtual-networks-overview.md)和[虚拟网络的规划与设计](virtual-network-vnet-plan-design-arm.md)，全面了解虚拟网络和所有设置。 

## <a name="next-steps"></a>后续步骤

如何连接：

- 阅读文章[创建 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 或[创建 Linux VM](../virtual-machines/linux/quick-create-portal.md)，将虚拟机 (VM) 连接到虚拟网络。 不要根据这两篇文章的步骤创建 VNet 和子网，可以选择要将 VM 连接到的现有 VNet 和子网。
- 阅读[连接 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 一文，将一个虚拟网络连接到其他虚拟网络。
- 使用站点到站点虚拟专用网络 (VPN) 或 ExpressRoute 线路，将本地网络连接到虚拟网络。 阅读文章[使用站点到站点 VPN 将 VNet 连接到本地网络](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)和[将 VNet 链接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)，了解相关操作方法。
