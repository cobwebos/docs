---
title: 如何配置虚拟中心路由
titleSuffix: Azure Virtual WAN
description: 本文介绍如何配置虚拟中心路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313682"
---
# <a name="how-to-configure-virtual-hub-routing"></a>如何配置虚拟中心路由

虚拟中心可以包含多个网关，例如站点到站点 VPN 网关、ExpressRoute 网关、点到站点网关以及 Azure 防火墙。 虚拟中心的路由功能由路由器提供，该路由器使用边界网关协议 (BGP) 管理网关之间的所有路由（包括传输路由）。 该路由器还提供与虚拟中心连接的虚拟网络之间的传输连接，最高可支持 50 Gbps 的总吞吐量。 这些路由功能适用于标准虚拟 WAN 客户。

有关详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="create-a-route-table"></a><a name="create-table"></a>创建路由表

1. 在 Azure 门户中，导航到虚拟中心。
2. 在“连接”下，选择“路由” 。 在“路由”页上，可以看到“默认”和“无”路由表 。

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="“路由”页":::
3. 选择“+创建路由表”以打开“创建路由表”页 。
4. 在“创建路由表”页的“基本信息”选项卡上，请填写以下字段。

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="“路由”页":::

   * **名称**
   * **Routes**
   * **路由名称**
   * **目标类型**
   * 目标前缀：可以聚合前缀。 例如：VNet 1：10.1.0.0/24 和 VNet 2：10.1.1.0/24 可以聚合为 10.1.0.0/16。 分支路由适用于所有已连接的 VPN 站点、ExpressRoute 线路和用户 VPN 连接。
   * **下一跃点**：虚拟网络连接或 Azure 防火墙的列表。

     如果选择虚拟网络连接，则会看到“配置静态路由”。 这是可选的配置设置。 有关详细信息，请参阅[配置静态路由](about-virtual-hub-routing.md#static)。

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="“路由”页":::

5. 选择“标签”选项卡以配置标签名称。 标签提供一种对路由表进行逻辑分组的机制。

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="“路由”页":::

6. 选择“关联”选项卡以将连接关联到路由表。
你会看到连接的“分支”、“虚拟网络”和“当前设置”  。

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="“路由”页":::

7. 选择“传播”选项卡以将路由从连接传播到路由表。

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="“路由”页":::

8. 选择“创建”以创建路由表。

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>编辑路由表

在 Azure 门户中，找到虚拟中心的路由表。 选择路由表以编辑任何信息。

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>删除路由表

在 Azure 门户中，找到虚拟中心的路由表。 无法删除“默认”或“无”路由表。 但是可以删除所有自定义路由表。 单击“…”，然后选择“删除” 。

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>查看有效路由

在 Azure 门户中，找到虚拟中心的路由表。 单击“…”，然后选择“有效路由”以查看所选路由表了解的路由 。 在路由表的“有效路由”中会自动填充从连接传播到路由表的路由。 有关详细信息，请参阅[关于有效路由](effective-routes-virtual-hub.md)。

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="“路由”页" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>为虚拟网络连接设置路由配置

1. 在 Azure 门户中，导航到你的虚拟 WAN，然后在“连接”下选择“虚拟网络连接”。 
1. 选择“+添加连接”。
1. 从下拉列表中选择虚拟网络。
1. 设置要关联到路由表的路由配置。 对于“关联路由表”，请从下拉列表中选择路由表。
1. 设置路由配置以传播到一个或多个路由表。 对于“传播到路由表”，请从下拉列表中进行选择。
1. 对于“静态路由”，为网络虚拟设备配置静态路由（如果适用）。 对于虚拟网络连接中的静态路由，虚拟 WAN 支持单个下一跃点 IP。 例如，如果你有一个单独的虚拟设备用于入口和出口流量，则最好将虚拟设备置于单独的 VNET 中，并将 VNET 连接到虚拟中心。


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="“路由”页" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>后续步骤

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
