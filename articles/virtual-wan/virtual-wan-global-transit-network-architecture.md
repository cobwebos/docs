---
title: 体系结构：全局传输网络体系结构
titleSuffix: Azure Virtual WAN
description: 了解用于虚拟 WAN 的全局传输网络体系结构
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064965"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全局传输网络体系结构和虚拟 WAN

新式企业需要跨云和本地的超分布式应用程序、数据和用户进行广泛的连接。 企业采用全局传输网络体系结构来整合、连接和控制以云为中心的新式企业 IT 需求。

全局传输网络体系结构基于经典中心辐射型连接模型，在该模型中，云托管网络 "集线器" 启用可跨不同类型的 "轮辐" 分布的终结点之间的可传递连接。

在此模型中，分支可以是：
* 虚拟网络（Vnet）
* 物理分支站点
* 远程用户
* Internet

![中心辐射](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**图1：全局传输中心辐射型网络**

图1显示了一个全局传输网络的逻辑视图，地理位置分散的用户、物理站点和 Vnet 通过云中托管的网络集线器互连。 此体系结构启用网络终结点之间的逻辑单跃点传输连接。

## <a name="globalnetworktransit"></a>具有虚拟 WAN 的全局传输网络

Azure 虚拟 WAN 是 Microsoft 托管的云网络服务。 此服务所由的所有网络组件由 Microsoft 托管和管理。 有关虚拟 WAN 的详细信息，请参阅[虚拟 Wan 概述](virtual-wan-about.md)一文。

通过在 Vnet、分支站点、SaaS 和 PaaS 应用程序以及用户的全球分布式云工作负荷组之间实现无处不在的连接，Azure 虚拟 WAN 允许全局传输网络体系结构。

![Azure 虚拟 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**图2：全局传输网络和虚拟 WAN**

在 Azure 虚拟 WAN 体系结构中，虚拟 WAN 中心在 Azure 区域中进行了设置，你可以选择将分支、Vnet 和远程用户连接到这些区域。 物理分支站点通过高级 ExpressRoute 或站点到站点 Vpn 连接到中心，Vnet 通过 VNet 连接连接到集线器，远程用户可以使用用户 VPN （点到站点 Vpn）直接连接到中心。 虚拟 WAN 还支持跨区域 VNet 连接，其中一个区域中的 VNet 可以连接到不同区域中的虚拟 WAN 集线器。

可以通过在包含最多轮辐（分支、Vnet、用户）的区域中创建单个虚拟 WAN 中心，然后将其他区域中的轮辐连接到中心，建立虚拟 WAN。 当企业占用量主要位于具有几个远程轮辐的某个区域时，这是一个不错的选择。  
  
## <a name="hubtohub"></a>集线器到中心连接

企业云足迹可跨多个云区域，并且它是从距离其物理站点和用户最近的区域访问云的最佳（延迟）。 全局传输网络体系结构的关键原则之一是在所有云和本地网络终结点之间启用跨区域连接。 这意味着，来自连接到一个区域中的云的分支中的流量可以使用[Azure 全局网络](https://azure.microsoft.com/global-infrastructure/global-network/)启用的集线器到集线器连接访问另一个分支或不同区域中的 VNet。

![跨区域](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**图3：虚拟 WAN 跨区域连接**

当在单个虚拟 WAN 中启用多个中心时，集线器会通过集线器到集线器的链路自动互连，从而在跨多个区域分布的分支与 Vnet 之间实现全局连接。 

此外，所有属于同一虚拟 WAN 的集线器都可以与不同的区域访问和安全策略相关联。 有关详细信息，请参阅本文后面的[安全性和策略控制](#security)。

## <a name="anytoany"></a>任何连接

全局传输网络体系结构可实现通过虚拟 WAN 中心实现的任何连接。 此体系结构消除或减少了轮辐之间完全网格或部分网格连接的需要，更复杂的是生成和维护。 此外，中心辐射型网络与网格网络中的路由控制更易于配置和维护。

任何连接（在全局体系结构的上下文中）都允许企业使用全球分布的用户、分支、数据中心、Vnet 和应用程序通过 "传输" 中心彼此连接。 Azure 虚拟 WAN 充当全局传输系统。

![任何](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**图4：虚拟 WAN 流量路径**

Azure 虚拟 WAN 支持以下全局传输连接路径。 括号中的字母映射到图4。

* 分支到 VNet （a）
* 分支到分支（b）
  * ExpressRoute Global Reach 和虚拟广域网
* 远程用户到 VNet （c）
* 远程用户到分支（d）
* VNet 到 VNet （e）
* 分支到集线器-中心到分支（f）
* 分支到集线器-从中心到 VNet （g）
* VNet 到中心-从集线器到 VNet （h）

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>分支到 VNet （a）和分支到 VNet 跨区域（g）

分支到 VNet 是 Azure 虚拟 WAN 支持的主要路径。 此路径允许将分支连接到在 Azure Vnet 中部署的 Azure IAAS 企业工作负荷。 可以通过 ExpressRoute 或站点到站点 VPN 连接到虚拟 WAN。 流量上流动到通过 VNet 连接连接到虚拟 WAN 集线器的 Vnet。 虚拟 WAN 不需要显式[网关传输](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)，因为虚拟 wan 会自动启用到分支站点的网关传输。 请参阅[虚拟 Wan 合作伙伴](virtual-wan-configure-automation-providers.md)一文，了解如何将 SD 广域网 CPE 连接到虚拟 wan。

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach 和虚拟广域网

ExpressRoute 是将本地网络连接到 Microsoft 云的一种私有且灵活的方式。 虚拟 WAN 支持快速路由线路连接。 使用 Express Route 将分支站点连接到虚拟 WAN 需要1）高级线路2）线路处于 Global Reach 启用位置。

ExpressRoute Global Reach 是适用于 ExpressRoute 的附加功能。 使用 Global Reach，你可以将 ExpressRoute 线路链接在一起，以便在本地网络之间建立专用网络。 使用 ExpressRoute 连接到 Azure 虚拟 WAN 的分支要求 ExpressRoute Global Reach 彼此通信。

在此模型中，使用 ExpressRoute 连接到虚拟 WAN 集线器的每个分支都可以使用分支到 VNet 路径连接到 Vnet。 分支到分支流量不会传输中心，因为 ExpressRoute Global Reach 通过 Azure WAN 实现更最优的路径。

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>分支到分支（b）和分支到分支跨区域（f）

可以使用 ExpressRoute 线路和/或站点到站点 VPN 连接将分支连接到 Azure 虚拟 WAN 集线器。 你可以将分支连接到距离分支最近的区域中的虚拟 WAN 集线器。

此选项使企业能够利用 Azure 主干来连接分支。 但尽管此功能可用，但你应该权衡通过 Azure 虚拟 WAN 连接分支与使用专用 WAN 的好处。  

### <a name="remote-user-to-vnet-c"></a>远程用户到 VNet （c）

你可以使用从远程用户客户端到虚拟 WAN 的点到站点连接来启用对 Azure 的直接、安全远程访问。 企业远程用户不再需要使用企业 VPN hairpin 到云中。

### <a name="remote-user-to-branch-d"></a>远程用户到分支（d）

远程用户到分支路径允许使用点到站点连接的远程用户通过云传输访问本地工作负荷和应用程序。 此路径使远程用户可以灵活地访问部署在 Azure 和本地的工作负载。 企业可以在 Azure 虚拟 WAN 中启用基于云的中心安全远程访问服务。

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 到 VNet 传输（e）和 VNet 到 VNet 跨区域（h）

VNet 到 VNet 传输使 Vnet 可以相互连接，以便互连跨多个 Vnet 实现的多层应用程序。 还可以通过 VNet 对等互连将 Vnet 彼此连接，这可能适用于不需要通过 VWAN 集线器传输的某些方案。

## <a name="security"></a>安全和策略控制

Azure 虚拟 WAN 中心跨混合网络互连所有网络终结点，并且可能会看到所有传输网络流量。 可以通过在 VWAN 中心内部署 Azure 防火墙来将虚拟 WAN 中心转换为受保护的虚拟中心，以实现基于云的安全性、访问和策略控制。 虚拟 WAN 集线器中的 Azure 防火墙的业务流程可以由 Azure 防火墙管理器执行。

[Azure 防火墙管理器](https://go.microsoft.com/fwlink/?linkid=2107683)提供用于管理和缩放全局传输网络的安全性的功能。 Azure 防火墙管理器提供了通过第三方和 Azure 防火墙集中管理路由、全局策略管理和高级 Internet 安全服务的功能。

![受保护的虚拟中心与 Azure 防火墙](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**图5：通过 Azure 防火墙保护的虚拟中心**

Azure Firewall 到虚拟 WAN 支持以下全球安全的传输连接路径。 括号中的字母映射到图5。

* VNet 到 VNet 安全传输（e）
* VNet 到 Internet 或第三方安全服务（i）
* 分支到 Internet 或第三方安全服务（j）

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 到 VNet 安全传输（e）

通过 VNet 到 VNet 保护的传输，Vnet 可通过虚拟 WAN 集线器中的 Azure 防火墙彼此连接。

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 到 Internet 或第三方安全服务（i）

通过 VNet 到 Internet 或第三方的安全传输，Vnet 可通过虚拟 WAN 集线器中的 Azure 防火墙连接到 internet 或受支持的第三方安全服务。

### <a name="branch-to-internet-or-third-party-security-service-j"></a>分支到 Internet 或第三方安全服务（j）
分支到 Internet 或第三方安全传输允许分支通过虚拟 WAN 集线器中的 Azure 防火墙连接到 Internet 或受支持的第三方安全服务。

## <a name="next-steps"></a>后续步骤

使用虚拟 WAN 创建连接并在 VWAN hub 中部署 Azure 防火墙。

* [使用虚拟 WAN 的站点到站点连接](virtual-wan-site-to-site-portal.md)
* [使用虚拟 WAN 的 ExpressRoute 连接](virtual-wan-expressroute-portal.md)
* [用于在 VWAN 中部署 Azure FW 的 azure 防火墙管理器](https://go.microsoft.com/fwlink/?linkid=2107683)
