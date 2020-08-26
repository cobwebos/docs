---
title: 体系结构：全局传输网络体系结构
titleSuffix: Azure Virtual WAN
description: 了解 Azure 虚拟 WAN 如何通过在云工作负荷之间启用无处不在的连接来实现全局传输网络体系结构。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: 63a9c3a6c23d78411c04250359dac3c3aacde2ba
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212700"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全局传输网络体系结构和虚拟 WAN

现代企业需要在云中和本地的超分布式应用程序、数据与用户之间随时建立连接。 有许多企业正在采用全局传输网络体系结构来整合、连接和控制以云为中心的现代全球企业 IT 设施。

全局传输网络体系结构基于经典的中心辐射型连接模型，使用其中的云托管网络“中心”可以在跨不同“辐射”类型分布的终结点之间建立传输连接。

在此模型中，辐射可以是：
* 虚拟网络 (VNet)
* 物理分支站点
* 远程用户
* Internet

![中心和辐射](./media/virtual-wan-global-transit-network-architecture/figure1.png)

图 1：**全局传输中心辐射型网络**

图 1 显示了全局传输网络的逻辑视图，其中的地理分散式用户、物理站点和 VNet 通过云中托管的网络中心互连。 此体系结构可在网络终结点之间实现逻辑单跃点传输连接。

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>使用虚拟 WAN 的全局传输网络

Azure 虚拟 WAN 是 Microsoft 托管的云网络服务。 此服务所由的所有网络组件由 Microsoft 托管和管理。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)一文。

Azure 虚拟 WAN 支持在 VNet 中的全局分布式云工作负荷集、分支站点、SaaS 和 PaaS 应用程序与用户之间随时建立任意点到任意点的连接，从而实现全局传输网络体系结构。

![Azure 虚拟 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

图 2：**全局传输网络和虚拟 WAN**

在 Azure 虚拟 WAN 体系结构中，虚拟 WAN 中心在可将分支、VNet 和远程用户连接到的 Azure 区域中预配。 物理分支站点通过高级 ExpressRoute 或站点到站点 VPN 连接到中心，VNet 通过 VNet 连接连接到中心，远程用户可以使用用户 VPN（点到站点 VPN）直接连接到中心。 虚拟 WAN 还支持跨区域 VNet 连接，其中，一个区域中的 VNet 可以连接到另一个区域中的虚拟 WAN 中心。

若要建立虚拟 WAN，可以在包含最多辐射（分支、VNet、用户）的区域中创建单个虚拟 WAN 中心，然后将其他区域中的辐射连接到该中心。 如果企业的运营足迹主要在包括少量几个远程辐射的单个区域内，则这是一个不错的选择。  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>集线器到中心连接

企业云足迹可以跨多个云区域，最好是从最靠近其物理站点和用户的区域访问云（改善延迟）。 全局传输网络体系结构的重要原则之一是在所有云与本地网络终结点之间实现跨区域连接。 这意味着，与一个区域中的云相连接的分支发出的流量，可以使用 [Azure 全球网络](https://azure.microsoft.com/global-infrastructure/global-network/)实现的中心到中心连接抵达不同区域中的另一个分支或 VNet。

![跨区域](./media/virtual-wan-global-transit-network-architecture/figure3.png)

图 3：**虚拟 WAN 跨区域连接**

在单个虚拟 WAN 中启用多个中心时，中心将通过中心到中心的链接自动互连，从而在跨多个区域分布的分支与 VNet 之间实现全局连接。 

此外，全部属于同一虚拟 WAN 的中心可与不同的区域访问和安全策略相关联。 有关详细信息，请参阅本文稍后的[安全和策略控制](#security)。

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>任意点到任意点的连接

全局传输网络体系结构通过虚拟 WAN 中心实现任意点到任意点的连接。 此体系结构消除或减少了辐射之间的全网格或部分网格式连接模型的需要，此类模型的构建和维护更复杂。 此外，与网格网络相比，中心辐射型体系结构中的路由控制更容易配置和维护。

全局体系结构的上下文中 (任意到任意连接性) 使具有全球分布式用户、分支、数据中心、Vnet 和应用程序的企业可以通过 "传输" 中心 () 相互连接。 Azure 虚拟 WAN 充当全局传输系统。

![任意点到任意点](./media/virtual-wan-global-transit-network-architecture/figure4.png)

图 4：**虚拟 WAN 流量路径**

Azure 虚拟 WAN 支持以下全局传输连接路径。 括号中的字母对应于图 4 中的标识。

* 分支到 VNet (a)
* 分支到分支 (b)
  * ExpressRoute Global Reach 和虚拟广域网
* 远程用户到 VNet (c)
* 远程用户到分支 (d)
* VNet 到 VNet (e)
* 分支到中心/中心到分支 (f)
* 分支到中心/中心到 VNet (g)
* VNet 到中心/中心到 VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>分支到 VNet (a)/分支到 VNet 跨区域 (g)

分支到 VNet 是 Azure 虚拟 WAN 支持的主要路径。 使用此路径可将分支连接到 Azure VNet 中部署的 Azure IAAS 企业工作负荷。 分支可以通过 ExpressRoute 或站点到站点 VPN 连接到虚拟 WAN。 流量通过 VNet 连接传输到与虚拟 WAN 中心相连接的 VNet。 虚拟 WAN 不需要明确的[网关传输](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)，因为虚拟 WAN 会自动启用到分支站点的网关传输。 请参阅[虚拟 WAN 合作伙伴](virtual-wan-configure-automation-providers.md)一文，了解如何将 SD-WAN CPE 连接到虚拟 WAN。

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach 和虚拟广域网

ExpressRoute 是将本地网络连接到 Microsoft 云的一种私有且灵活的方式。 虚拟 WAN 支持快速路由线路连接。 使用 Express Route 将分支站点连接到虚拟 WAN 需要 1) 高级线路 2) 线路置于 Global Reach 启用位置。

ExpressRoute Global Reach 是适用于 ExpressRoute 的附加功能。 使用 Global Reach，你可以将 ExpressRoute 线路链接在一起，以便在本地网络之间建立专用网络。 使用 ExpressRoute 连接到 Azure 虚拟 WAN 的分支要求 ExpressRoute Global Reach 彼此通信。

在此模型中，使用 ExpressRoute 连接到虚拟 WAN 中心的每个分支可以使用分支到 VNet 的路径连接到 VNet。 分支到分支流量不会传输中心，因为 ExpressRoute Global Reach 通过 Azure WAN 实现更最优的路径。

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>分支到分支 (b)/分支到分支跨区域 (f)

分支可以使用 ExpressRoute 线路和/或站点到站点 VPN 连接连接到 Azure 虚拟 WAN 中心。 可将分支连接到其最靠近的区域中的虚拟 WAN 中心。

企业可以通过此选项来利用 Azure 主干网连接分支。 但是，尽管可以此功能，应该权衡通过 Azure 虚拟 WAN 与通过专用 WAN 连接分支的利弊。  

> [!NOTE]
> 禁用虚拟 WAN 中的分支到分支连接 - 可以将虚拟 WAN 配置为禁用分支到分支连接。 此配置将阻止连接了 VPN（S2S 和 P2S）与 Express Route 的站点之间的路由传播。 此配置不会影响分支到 Vnet 以及 Vnet 到 Vnet 的路由传播和连接。 使用 Azure 门户配置此设置：在“虚拟 WAN 配置”菜单下，选择“设置”：分支到分支 - 已禁用。 

### <a name="remote-user-to-vnet-c"></a>远程用户到 VNet (c)

使用远程用户客户端与虚拟 WAN 之间的点到站点连接可以安全地直接远程访问 Azure。 企业远程用户不再需要使用企业 VPN 来始终与云保持连接。

### <a name="remote-user-to-branch-d"></a>远程用户到分支 (d)

使用远程用户到分支路径，与 Azure 建立了点到站点连接的远程用户可以通过云传输流量，以此访问本地工作负荷和应用程序。 此路径可让远程用户灵活访问部署在 Azure 中和本地的工作负荷。 企业可以在 Azure 虚拟 WAN 中启用基于中心云的安全远程访问服务。

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 到 VNet 传输 (e)/VNet 到 VNet 跨区域 (h)

VNet 到 VNet 传输使 VNet 能够相互连接，以互连跨多个 VNet 实施的多层应用程序。 或者，可以通过 VNet 对等互连将 VNet 相互连接，这可能适合无需通过 VWAN 中心进行传输的某些方案。


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Azure 虚拟 WAN 中的强制隧道和默认路由

可以通过在虚拟 WAN 中的 VPN、ExpressRoute 或虚拟网络连接上配置“启用默认路由”来启用强制隧道。

如果连接上的启用默认标志为“已启用”，则虚拟中心可将获知的默认路由传播到虚拟网络/站点到站点 VPN/ExpressRoute 连接。 

当用户编辑虚拟网络连接、VPN 连接或 ExpressRoute 连接时，将显示此标志。 默认情况下，当站点或 ExpressRoute 线路连接到中心时，将禁用此标志。 如果添加虚拟网络连接以将 VNet 连接到虚拟中心，则默认情况下启用此功能。 默认路由不是源自虚拟 WAN 中心；只有当虚拟 WAN 中心由于在中心部署防火墙而获知默认路由或另一个连接的站点已启用强制隧道时，此标志才会将默认路由传播到连接。

## <a name="security-and-policy-control"></a><a name="security"></a>安全和策略控制

Azure 虚拟 WAN 中心将互连整个混合网络中的所有网络终结点，可能会看到所有传输网络流量。 可以在 VWAN 中心内部署 Azure 防火墙以启用基于云的安全、访问和策略控制，将虚拟 WAN 中心转换为安全虚拟中心。 虚拟 WAN 中心内的 Azure 防火墙协调可由 Azure 防火墙管理器执行。

[Azure 防火墙管理器](https://go.microsoft.com/fwlink/?linkid=2107683)提供管理和缩放全局传输网络安全性的功能。 Azure 防火墙管理器提供通过第三方工具和 Azure 防火墙集中管理路由、全局策略和高级 Internet 安全服务的功能。

![使用 Azure 防火墙的安全虚拟中心](./media/virtual-wan-global-transit-network-architecture/figure5.png)

图 5：**使用 Azure 防火墙的安全虚拟中心**

> [!NOTE]
> 当前不支持与防火墙的集线器。 集线器之间的流量将直接在每个中心绕过 Azure 防火墙。

虚拟 WAN 的 Azure 防火墙支持以下全局安全传输连接路径。 括号中的字母对应于图 5 中的标识。

* VNet 到 VNet 安全传输 (e)
* VNet 到 Internet 或第三方安全服务 (i)
* 分支到 Internet 或第三方安全服务 (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 到 VNet 安全传输 (e)

VNet 到 VNet 安全传输使 VNet 能够通过虚拟 WAN 中心内的 Azure 防火墙相互连接。

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 到 Internet 或第三方安全服务 (i)

VNet 到 Internet 使 VNet 能够通过虚拟 WAN 中心内的 Azure 防火墙连接到 Internet。 通过受支持的第三方安全服务流向 Internet 的流量不会流经 Azure 防火墙。 可以使用 Azure 防火墙管理器通过受支持的第三方安全服务配置 Vnet 到 Internet 的路径。  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>分支到 Internet 或第三方安全服务 (j)
分支到 Internet 使分支能够通过虚拟 WAN 中心内的 Azure 防火墙连接到 Internet。 通过受支持的第三方安全服务流向 Internet 的流量不会流经 Azure 防火墙。 可以使用 Azure 防火墙管理器通过受支持的第三方安全服务配置分支到 Internet 的路径。 

### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>如何在安全虚拟中心中启用默认路由 (0.0.0.0/0)

可以将虚拟 WAN 中心（安全虚拟中心）中部署的 Azure 防火墙配置为 Internet 的默认路由器，或配置为适用于所有分支（通过 VPN 或 Express Route 连接）、辐射 Vnet 和用户（通过 P2S VPN 连接）的受信任安全提供程序。 必须使用 Azure 防火墙管理器完成此配置。  请参阅流向中心的流量路由，配置来自分支（包括用户）以及经 Azure 防火墙 Vnet 到 Internet 的所有流量。 

这是一个两步式配置：

1. 使用“安全虚拟中心路由设置”菜单配置 Internet 流量路由。 配置可以通过防火墙将流量发送到 Internet 的 Vnet 和分支。

2. 将那些连接（Vnet 和分支）配置为可以通过中心或受信任安全提供程序中的 Azure 防火墙将流量路由到 Internet (0.0.0.0/0)。 此步骤确保将默认路由传播到通过“连接”附加到虚拟 WAN 中心的选定分支和 Vnet。 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>安全虚拟中心中流向本地防火墙的强制隧道流量

如果通过虚拟中心获知（通过 BGP）的已有默认路由源自分支之一（VPN 或 ER 站点），则此默认路由会被从 Azure 防火墙管理器设置中获知的默认路由覆盖。 这种情况下，从 Vnet 和分支进入中心并流向 Internet 的所有流量都将路由到 Azure 防火墙或受信任的安全提供程序。

> [!NOTE]
> 目前，对于源自 Vnet、分支或用户的 Internet 绑定流量，无法选择本地防火墙或 Azure 防火墙（以及受信任的安全提供程序）。 从 Azure 防火墙管理器设置中获知的默认路由始终优先于从分支之一获知的默认路由。


## <a name="next-steps"></a>后续步骤

使用虚拟 WAN 创建连接并在 VWAN 中心内部署 Azure 防火墙。

* [使用虚拟 WAN 的站点到站点连接](virtual-wan-site-to-site-portal.md)
* [使用虚拟 WAN 的 ExpressRoute 连接](virtual-wan-expressroute-portal.md)
* [用于在 VWAN 中部署 Azure FW 的 azure 防火墙管理器](https://go.microsoft.com/fwlink/?linkid=2107683)
