---
title: 方案：用于虚拟 WAN 的 Azure 防火墙自定义路由
titleSuffix: Azure Virtual WAN
description: 用于在 Vnet 之间直接路由流量的方案，但使用 Azure Firewall for VNet->Internet/分支以及分支到 VNet 流量流
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567441"
---
# <a name="scenario-azure-firewall---custom"></a>方案： Azure 防火墙-自定义

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在这种情况下，目标是直接在 Vnet 之间路由流量，但将 Azure 防火墙用于 VNet 到 Internet/分支以及分支到 VNet 的流量流。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

在这种情况下，你想要通过 Azure 防火墙路由流量以实现 VNet 到 Internet、VNet 到分支或分支到 VNet 的流量，但要直接访问 VNet 到 VNet 流量。 如果使用的是 Azure 防火墙管理器，路由设置将自动填充到**默认路由表**中。 专用流量适用于 VNet 和分支，Internet 流量适用于 0.0.0.0/0。

VPN、ExpressRoute 和用户 VPN 连接统称为分支，并与同一个（默认）路由表相关联。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。 若要配置此方案，请考虑以下步骤：

1. **RT_VNET**创建自定义路由表。
1. 创建一个路由，用于激活 VNet 到 Internet 和 VNet 到分支： 0.0.0.0/0，下一个指向 Azure 防火墙的跃点。 在 "传播" 部分中，请确保选择 "Vnet"，这将确保更具体的路由，从而允许 VNet 到 VNet 直接流量流。

   * **关联：** 选择 vnet，这意味着 vnet 将根据此路由表的路由到达目标。
   * 在 "传播" 中 **：** 选择 vnet，这将意味着 vnet 传播到此路由表;换句话说，更具体的路由将传播到此路由表，从而确保 VNet 到 VNet 之间的直接流量流动。

1. 将 Vnet 的聚合静态路由添加到**默认路由表**中，以便通过 Azure 防火墙激活分支到 VNet 流。 

   * 请记住，分支关联并传播到默认路由表。
   * 分支不会传播到 RT_VNET 路由表。 这可确保通过 Azure 防火墙的 VNet 到分支流量流动。

这将导致路由配置发生更改，如**图 1**所示。

**图1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="图1":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
* 有关如何配置虚拟中心路由的详细信息，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。
