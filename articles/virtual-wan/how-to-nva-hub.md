---
title: 'Azure 虚拟 WAN：在中心创建网络虚拟设备 (NVA) '
description: 了解如何在虚拟 WAN 中心部署网络虚拟设备。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 0e174f9b78606e0bea5cded3a176f4207745bb5c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077297"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>如何在 Azure 虚拟 WAN 中心 (预览中创建网络虚拟设备) 

本文介绍如何使用虚拟 WAN 通过 Azure 中 (NVA) 的 **网络虚拟设备** 连接到 azure 中的资源。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关虚拟 WAN 的详细信息，请参阅 [什么是虚拟 wan？](virtual-wan-about.md)。

本文中的步骤可帮助你在虚拟 WAN 集线器中创建 **Barracuda CLOUDGEN WAN** 网络虚拟设备。 若要完成此练习，必须在开始之前，将 Barracuda Cloud 本地设备 (CPE) 和部署到中心的 Barracuda CloudGen WAN 设备的许可证。

有关 Azure 虚拟广域网内 **CISCO SD** 的部署文档，请通过以下电子邮件地址向 Cisco 发送电子邮件： vwan_public_preview@external.cisco.com


## <a name="prerequisites"></a>先决条件

在开始配置之前，请验证你是否符合以下条件：

* 获取 Barracuda CloudGen WAN 网关的许可证。 若要了解有关如何执行此操作的详细信息，请参阅 [Barracuda CLOUDGEN WAN 文档](https://www.barracuda.com/products/cloudgenwan)

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>创建虚拟 WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>创建中心

中心是一种虚拟网络，可包含用于站点到站点、ExpressRoute、点到站点或网络虚拟设备功能的网关。 创建中心后，即使你没有附加任何站点，也会对该中心收取费用。 如果选择创建站点到站点 VPN 网关，则需要30分钟，才能在虚拟中心创建站点到站点 VPN 网关。 与站点到站点、ExpressRoute 或点到站点不同，必须首先创建集线器，然后才能将网络虚拟设备部署到中心 VNet 中。

1. 找到创建的虚拟 WAN。 在 " **虚拟 WAN** " 页的 " **连接** " 部分下，选择 " **集线器**"。
1. 在 " **中心** " 页上，选择 "+ 新建中心"，打开 " **创建虚拟中心** " 页。

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="基础知识":::
1. 在“创建虚拟中心”页上的“基本”选项卡上，请填写以下字段 ：

   **项目详细信息**

   * 区域（之前称为位置）
   * 名称
   * 中心专用地址空间。 用于创建中心的最小地址空间是 /24，这表示在创建过程中从 /25 到 /32 的任何范围都将产生错误。 Azure 虚拟 WAN 是 Microsoft 提供的一项托管服务，它在虚拟中心为不同的网关/服务创建适当的子网。  (例如：网络虚拟设备、VPN 网关、ExpressRoute 网关、用户 VPN/点到站点网关、防火墙、路由等 ) 。 用户无需为虚拟中心中的服务显式规划子网地址空间，因为 Microsoft 将此功能作为服务的一部分进行。
1. 选择“查看 + 创建”以进行验证。
1. 选择“创建”以创建中心。

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>在中心创建网络虚拟设备

在此步骤中，将在中心创建网络虚拟设备。 对于每个 NVA 合作伙伴的产品，每个 NVA 的过程各不相同。 在此示例中，我们将创建 Barracuda CloudGen WAN 网关。

1. 找到在上一步中创建的虚拟 WAN 中心，然后将其打开。

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="基础知识" 按钮。

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="基础知识" 按钮。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="基础知识" 按钮。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="基础知识" **创建**"。

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>将 VNet 连接到中心

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>后续步骤

* 若要了解有关虚拟 WAN 的详细信息，请参阅 [什么是虚拟 wan？](virtual-wan-about.md) 页。
* 若要了解有关虚拟 WAN 中心中 Nva 的详细信息，请参阅 [关于虚拟 wan 集线器中的网络虚拟设备 (预览) ](about-nva-hub.md)。
