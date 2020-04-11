---
title: 架构：全球交通网络架构
titleSuffix: Azure Virtual WAN
description: 了解虚拟 WAN 的全局传输网络体系结构
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 9515058bc78a2d56dc1734c046dac5d5b04f68d9
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113163"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全局传输网络体系结构和虚拟 WAN

现代企业需要在云中和本地的超分布式应用程序、数据与用户之间随时建立连接。 企业正在采用全球交通网络架构来整合、连接和控制以云为中心的现代全球企业 IT 足迹。

全局传输网络体系结构基于经典的中心辐射型连接模型，使用其中的云托管网络“中心”可以在跨不同“辐射”类型分布的终结点之间建立传输连接。

在此模型中，辐射可以是：
* 虚拟网络 (VNet)
* 物理分支站点
* 远程用户
* Internet

![枢纽和辐条](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**图1：全球中转枢纽和辐条网络**

图 1 显示了全球传输网络的逻辑视图，其中地理分布的用户、物理站点和 VNet 通过云中托管的网络中心互连。 此体系结构可在网络终结点之间实现逻辑单跃点传输连接。

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>具有虚拟广域网的全球传输网络

Azure 虚拟 WAN 是 Microsoft 管理的云网络服务。 此服务由的所有网络组件都由 Microsoft 托管和管理。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)一文。

Azure 虚拟 WAN 通过在 VNet、分支站点、SaaS 和 PaaS 应用程序以及用户中实现全球分布式云工作负载集之间的无所不在的任意连接，允许全球传输网络架构。

![Azure 虚拟 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**图 2：全球交通网络和虚拟广域网**

在 Azure 虚拟 WAN 体系结构中，虚拟 WAN 集线器预配在 Azure 区域中，您可以选择将分支、VNet 和远程用户连接到这些区域。 物理分支站点通过高级 ExpressRoute 或站点到站点 VPN 连接到集线器，VNet 通过 VNet 连接连接到集线器，远程用户可以使用用户 VPN（点对点 VPN）直接连接到集线器。 虚拟 WAN 还支持跨区域 VNet 连接，其中一个区域中的 VNet 可以连接到不同区域中的虚拟 WAN 集线器。

通过在具有最多分支（分支、VNet、用户）的区域创建单个虚拟 WAN 中心，然后将其他区域中的分支连接到中心，可以建立虚拟广域网。 当企业足迹主要位于具有几个远程分支的一个区域时，这是一个不错的选择。  
  
## <a name="hub-to-hub-connectivity-preview"></a><a name="hubtohub"></a>集线器到集线器连接（预览）

企业云占用空间可以跨越多个云区域，最好（延迟）从最接近其物理站点和用户的区域访问云。 全球交通网络架构的关键原则之一是在所有云和本地网络端点之间实现跨区域连接。 这意味着，从连接到一个区域中的云的分支的流量可以使用[Azure Global Network](https://azure.microsoft.com/global-infrastructure/global-network/)启用的集线器到集线器连接到达另一个分支或不同区域中的 VNet。

![跨区域](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**图 3：虚拟广域网跨区域连接**

当在单个虚拟 WAN 中启用多个集线器时，通过集线器到集线器链路自动互连集线器，从而实现分布在多个区域的分支和 Vnets 之间的全局连接。 

此外，属于同一虚拟 WAN 的集线器可以与不同的区域访问和安全策略相关联。 有关详细信息，请参阅本文后面的[安全和策略控制](#security)。

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>任意点到任意点的连接

全球交通网络架构支持通过虚拟广域网集线器进行任意连接。 此体系结构消除了或减少了在分支之间实现完全网格或部分网格连接的需要，这些连接在构建和维护方面更为复杂。 此外，与网格网络相比，中心辐射型体系结构中的路由控制更容易配置和维护。

任意到任意连接（在全局体系结构的上下文中）允许具有全局分布式用户、分支、数据中心、VNet 和应用程序的企业通过"传输"集线器相互连接。 Azure 虚拟 WAN 充当全局传输系统。

![任何到任何](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**图 4：虚拟广域网流量路径**

Azure 虚拟 WAN 支持以下全局传输连接路径。 括号中的字母映射到图 4。

* 分支到 VNet (a)
* 分支到分支 (b)
  * 快速路由全球覆盖和虚拟广域网
* 远程用户到 VNet (c)
* 远程用户到分支 (d)
* VNet 到 VNet （e）
* 分支到集线器-集线器到分支 （f）
* 分支到集线器-集线器到 VNet （g）
* VNet 到集线器-集线器到 VNet （h）

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>分支到 VNet （a） 和分支到 VNet 跨区域 （g）

分支到 VNet 是 Azure 虚拟 WAN 支持的主要路径。 使用此路径可将分支连接到 Azure VNet 中部署的 Azure IAAS 企业工作负荷。 分支可以通过 ExpressRoute 或站点到站点 VPN 连接到虚拟 WAN。 通过 VNet 连接连接到虚拟广域网集线器的流量传输到 VNet。 虚拟 WAN 不需要显式[网关传输](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)，因为虚拟 WAN 会自动启用到分支站点的网关传输。 有关如何将 SD-WAN CPE 连接到虚拟广域网的[虚拟 WAN 合作伙伴](virtual-wan-configure-automation-providers.md)文章。

### <a name="expressroute-global-reach-and-virtual-wan"></a>快速路由全球覆盖和虚拟广域网

ExpressRoute 是一种将本地网络连接到 Microsoft 云的专用且有弹性的方式。 虚拟广域网支持快速路由电路连接。 将分支站点连接到具有快速路由的虚拟广域网需要 1） 高级电路 2） 电路必须位于启用全局覆盖的位置。

ExpressRoute 全球覆盖是快速路由的附加功能。 借助 Global 覆盖，您可以将 ExpressRoute 电路链接在一起，在本地网络之间建立专用网络。 使用 ExpressRoute 连接到 Azure 虚拟 WAN 的分支需要快速路由全局覆盖彼此通信。

在此模型中，使用 ExpressRoute 连接到虚拟 WAN 中心的每个分支都可以使用分支到 VNet 路径连接到 VNet。 分支到分支的流量不会传输集线器，因为 ExpressRoute 全局覆盖线支持在 Azure WAN 上实现更优化的路径。

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>分支到分支 （b） 和分支到分支跨区域 （f）

分支可以使用 ExpressRoute 电路和/或站点到站点 VPN 连接连接到 Azure 虚拟 WAN 集线器。 您可以将分支连接到离分支最近的区域中的虚拟 WAN 中心。

企业可以通过此选项来利用 Azure 主干网连接分支。 但是，尽管可以此功能，应该权衡通过 Azure 虚拟 WAN 与通过专用 WAN 连接分支的利弊。  

### <a name="remote-user-to-vnet-c"></a>远程用户到 VNet (c)

您可以使用从远程用户客户端到虚拟 WAN 的点对点连接，对 Azure 进行直接、安全的远程访问。 企业远程用户不再需要使用企业 VPN 来始终与云保持连接。

### <a name="remote-user-to-branch-d"></a>远程用户到分支 (d)

使用远程用户到分支路径，与 Azure 建立了点到站点连接的远程用户可以通过云传输流量，以此访问本地工作负荷和应用程序。 此路径可让远程用户灵活访问部署在 Azure 中和本地的工作负荷。 企业可以在 Azure 虚拟 WAN 中启用基于中心云的安全远程访问服务。

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 到 VNet 传输 （e） 和 VNet 到 VNet 跨区域 （h）

VNet 到 VNet 传输使 VNet 能够相互连接，以便互连跨多个 VNet 实现的多层应用程序。 或者，您可以通过 VNet 对等互连将 VNet 彼此连接，这可能适用于不需要通过 VWAN 集线器进行传输的某些情况。

## <a name="security-and-policy-control"></a><a name="security"></a>安全和策略控制

Azure 虚拟广域网集线器互连了混合网络中的所有网络端点，并可能看到所有传输网络流量。 通过在 VWAN 集线器内部署 Azure 防火墙，实现基于云的安全、访问和策略控制，可以将虚拟 WAN 集线器转换为安全的虚拟中心。 虚拟 WAN 中心中的 Azure 防火墙的编排可以由 Azure 防火墙管理器执行。

[Azure 防火墙管理器](https://go.microsoft.com/fwlink/?linkid=2107683)提供了管理和扩展全局传输网络安全性的功能。 Azure 防火墙管理器提供通过第三方以及 Azure 防火墙集中管理路由、全局策略管理、高级 Internet 安全服务的能力。

![使用 Azure 防火墙保护虚拟中心](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**图 5：使用 Azure 防火墙的安全虚拟中心**

虚拟 WAN 的 Azure 防火墙支持以下全局安全传输连接路径。 括号中的字母映射到图 5。

* VNet 到 VNet 安全传输 （e）
* VNet 到 Internet 或第三方安全服务 （i）
* 分支机构到互联网或第三方安全服务 （j）

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 到 VNet 安全传输 （e）

VNet 到 VNet 安全传输使 VNet 能够通过虚拟 WAN 集线器中的 Azure 防火墙相互连接。

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 到 Internet 或第三方安全服务 （i）

VNet 到 Internet 或第三方安全传输使 VNet 能够通过虚拟 WAN 中心的 Azure 防火墙连接到 Internet 或受支持的第三方安全服务。

### <a name="branch-to-internet-or-third-party-security-service-j"></a>分支机构到互联网或第三方安全服务 （j）
分支到 Internet 或第三方安全传输使分支能够通过虚拟 WAN 中心的 Azure 防火墙连接到 Internet 或受支持的第三方安全服务。

## <a name="next-steps"></a>后续步骤

使用虚拟 WAN 创建连接，并在 VWAN 中心中部署 Azure 防火墙。

* [使用虚拟 WAN 的站点到站点连接](virtual-wan-site-to-site-portal.md)
* [使用虚拟 WAN 的快速路由连接](virtual-wan-expressroute-portal.md)
* [Azure 防火墙管理器在 VWAN 中部署 Azure FW](https://go.microsoft.com/fwlink/?linkid=2107683)
