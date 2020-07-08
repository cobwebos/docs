---
title: 关于虚拟中心路由
titleSuffix: Azure Virtual WAN
description: 本文介绍虚拟中心路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 6809429b26eea30ca0569044634308d2e4dff9f7
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86025975"
---
# <a name="about-virtual-hub-routing"></a>关于虚拟中心路由

虚拟中心中的路由功能由路由器提供，用于管理使用边界网关协议（BGP）的网关之间的所有路由。 一个虚拟中心可包含多个网关，例如站点到站点 VPN 网关、ExpressRoute 网关、点到站点网关、Azure 防火墙。 此路由器还提供连接到虚拟中心的虚拟网络之间的传输连接，并且最多可支持 50 Gbps 的聚合吞吐量。 这些路由功能适用于标准虚拟 WAN 客户。

若要配置路由，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。

## <a name="routing-concepts"></a><a name="concepts"></a>路由概念

以下部分介绍了虚拟中心路由中的关键概念。

### <a name="hub-route-table"></a><a name="hub-route"></a>中心路由表

虚拟中心路由表可以包含一个或多个路由。 路由包括其名称、标签、目标类型、目标前缀列表以及要路由的数据包的下一跃点信息。 **连接**通常会有一个关联或传播到路由表的路由配置。

### <a name="connection"></a><a name="connection"></a>连接

连接资源管理器具有路由配置的资源。 这四种连接类型为：

* **Vpn 连接**：将 VPN 站点连接到虚拟中心 VPN 网关。
* **Expressroute 连接**：将 ExpressRoute 线路连接到虚拟中心 ExpressRoute 网关。
* **P2S 配置连接**：将用户 Vpn （点到站点）配置连接到虚拟中心用户 Vpn （点到站点）网关。
* **中心虚拟网络连接**：将虚拟网络连接到虚拟中心。

可以在安装过程中设置虚拟网络连接的路由配置。 默认情况下，所有连接关联并传播到默认路由表。

### <a name="association"></a><a name="association"></a>关联

每个连接都与一个路由表相关联。 将连接关联到路由表后，可以将流量发送到指定为路由表中的路由的目标。 连接的路由配置将显示关联的路由表。  可以将多个连接关联到相同的路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接均关联到相同（默认）路由表。

默认情况下，所有连接都与虚拟中心中的**默认路由表**相关联。 每个虚拟中心都有其自己的默认路由表，可以对其进行编辑以添加静态路由。 静态添加的路由优先于动态了解相同前缀的路由。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="关联":::

### <a name="propagation"></a><a name="propagation"></a>传播

连接将路由动态传播到路由表。 使用 VPN 连接、ExpressRoute 连接或 P2S 配置连接，使用 BGP 将路由从虚拟中心传播到本地路由器。 路由可以传播到一个或多个路由表。

每个虚拟中心也有一个**无路由表**。 传播到无路由表意味着无需从连接传播路由。 VPN、ExpressRoute 和用户 VPN 连接将路由传播到同一组路由表。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="传播":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>在虚拟网络连接中配置静态路由

配置静态路由可提供一种机制，用于通过下一个跃点 IP 来控制流量，这可能是连接到虚拟中心的分支 VNet 中预配的网络虚拟设备（NVA）。 静态路由由路由名称、目标前缀列表和下一跃点 IP 组成。

> [!NOTE]
> 虚拟网络连接中的关联、传播和静态路由的其中一些新概念可能仍在推出，并应在8月3日的第几周完成。
>

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>在建立关联和传播功能之前，先在基本和标准虚拟 Wan 中路由表

路由表现在具有用于关联和传播的功能。 预先存在的路由表是不具有这些功能的路由表。 如果你在中心路由中具有预先存在的路由，并且想要使用新功能，请考虑以下事项：

* **在虚拟中心中具有预先存在的路由的标准虚拟 WAN 客户**：

若要使用新的路由表功能，请等到8月3日起，才能完成 Azure 中的推出。 如果在 Azure 门户中的中心的 "路由" 部分中具有预先存在的路由，则需要先删除它们，然后尝试创建新的路由表（在 Azure 门户中的中心的 "路由表" 部分中提供）

* **具有虚拟中心中预先存在的路由的基本虚拟 WAN 客户**：若要使用新的路由表功能，请等到8月3日开始使用 Azure 完成。 如果在 Azure 门户中的中心的 "路由" 部分中具有预先存在的路由，则需先将其删除，然后将基本虚拟 WAN**升级**到标准虚拟 wan。 请参阅[将虚拟 WAN 从 Basic 升级到 Standard](upgrade-virtual-wan.md)。

## <a name="next-steps"></a>后续步骤

若要配置路由，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
