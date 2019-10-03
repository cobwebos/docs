---
title: Azure 虚拟 WAN 全局传输网络体系结构 |Microsoft Docs
description: 了解虚拟 WAN 的全局传输网络体系结构
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 0a5059382c26afd6120dc14a1ab2c7e5d281e7a1
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695272"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全局传输网络体系结构和虚拟 WAN

有许多企业正在采用全局传输网络体系结构来整合、连接和控制以云为中心的现代企业 IT 设施。 在以云为中心的现代企业中，网络流量不需要传回到总部。 全局传输网络体系结构基于客户熟悉的网络概念，以及云和基于云的体系结构所特有的新概念。

![体系结构](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

图 1：**使用虚拟 WAN 的全局传输网络**

现代企业需要在云中和本地的超分布式应用程序、数据与用户之间随时建立连接。 Azure 虚拟 WAN 支持在全局分布式的 VNet、站点、应用程序与用户集之间随时建立任意点到任意点的连接，从而实现全局传输网络体系结构。 Azure 虚拟 WAN 是一项 Microsoft 托管的服务。 此服务所由的所有网络组件由 Microsoft 托管和管理。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)一文。

在 Azure 虚拟 WAN 体系结构中，Azure 区域充当可将分支连接到的中心。 连接分支后，可以利用 Azure 主干网建立分支到 VNet 和（可选）分支到分支的连接。

若要建立虚拟 WAN，可以在包含最多辐射（分支、VNet、用户）的区域中创建单个虚拟 WAN 中心，然后将其他区域中的辐射连接到该中心。 或者，如果辐射是地理分散的，则你还可以实例化区域中心，并将这些中心互连到一起。 所有中心属于同一个虚拟 WAN，但可与不同的区域策略相关联。

## <a name="hub"></a>中心辐射型中转

全局传输网络体系结构基于经典的中心辐射型连接模型，使用其中的云托管网络“中心”可以在跨不同“辐射”类型分布的终结点之间建立传输连接。
  
在此模型中，辐射可以是：

* 虚拟网络 (VNet)
* 物理分支站点
* 远程用户
* Internet

![中心辐射型全局传输示意图](./media/virtual-wan-global-transit-network-architecture/architecture.png)

图 2：**中心辐射型**

图 2 显示了全局网络的逻辑视图，其中的地理分散式用户、物理站点和 VNet 通过云中托管的网络中心互连。 此体系结构可在网络终结点之间实现逻辑单跃点传输连接。 轮辐通过各种 Azure 网络服务（如 ExpressRoute 或站点到站点 VPN）连接到中心，用于物理分支、VNet 连接 Vnet 和远程用户的点到站点 VPN。

## <a name="crossregion"></a>跨区域连接

对于企业而言，云足迹通常类似于物理足迹。 大多数企业会从最靠近其物理站点和用户的区域访问云。 全局网络体系结构的重要原则之一是在网络实体与终结点之间实现跨区域连接。 云足迹可以跨越多个区域。 这意味着，来自连接到一个区域中的云的分支中的流量可以使用集线器到集线器连接访问另一区域中的另一个分支或 VNet，这当前位于我们的路线图上。

## <a name="any"></a>任何连接

全局传输网络体系结构通过中心网络中心实现任意点到任意点的连接。 此体系结构消除或减少了全网格或部分网格式连接模型的需要，此类模型的构建和维护更复杂。 此外，与网格网络相比，中心辐射型体系结构中的路由控制更容易配置和维护。

在全局体系结构环境中，任意点到任意点的连接可让企业中的全局分布式用户、分支、数据中心、VNet 和应用程序通过传输中心相互连接。 传输中心充当全局传输系统。

![流量路径](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

图 3：**虚拟 WAN 流量路径**

Azure 虚拟 WAN 支持以下全局传输连接路径。 括号中的字母对应于图 3 中的标识。

* 分支到 VNet (a)  
* 分支到分支 (b)
* 远程用户到 VNet (c)
* 远程用户到分支 (d)
* 使用 VNet 对等互连的 VNet 到 VNet 传输 (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>分支到 VNet

分支到 VNet 是 Azure 虚拟 WAN 支持的主要路径。 使用此路径可将分支连接到 Azure VNet 中部署的 Azure IAAS 企业工作负荷。 分支可以通过 ExpressRoute 或站点到站点 VPN 连接到虚拟 WAN。 流量上流动到通过 VNet 连接连接到虚拟 WAN 集线器的 Vnet。虚拟 WAN 不需要[网关传输](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)，因为虚拟 wan 会自动启用到分支站点的网关传输。

### <a name="branchbranch"></a>分支到分支

分支可以使用 ExpressRoute 线路和/或站点到站点 VPN 连接连接到 Azure 虚拟 WAN 中心。 可将分支连接到其最靠近的区域中的虚拟 WAN 中心。

企业可以通过此选项来利用 Azure 主干网连接分支。 但是，尽管可以此功能，应该权衡通过 Azure 虚拟 WAN 与通过专用 WAN 连接分支的利弊。

### <a name="usertovnet"></a>远程用户到 VNet

使用远程用户客户端与虚拟 WAN 之间的点到站点连接可以安全地直接远程访问 Azure。 企业远程用户不再需要使用企业 VPN 来始终与云保持连接。

### <a name="usertobranch"></a>远程用户到分支

使用远程用户到分支路径，与 Azure 建立了点到站点连接的远程用户可以通过云传输流量，以此访问本地工作负荷和应用程序。 此路径可让远程用户灵活访问部署在 Azure 中和本地的工作负荷。 企业可以在 Azure 虚拟 WAN 中启用基于中心云的安全远程访问服务。

### <a name="vnetvnet"></a>使用 VNet 对等互连的 VNet 到 VNet 传输

若要将 VNet 相互连接以支持跨多个 VNet 实现的多层应用程序，请使用 VNet 对等互连。 目前不支持通过 Azure 虚拟 WAN 实现 VNet 到 VNet 传输方案，但此方案已纳入 Azure 路线图。 对于需要相互连接的 VNet，通过 VNet 对等互连连接 VNet 是建议的解决方案。 

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute 是将本地网络连接到 Microsoft 云的一种私有且灵活的方式。 ExpressRoute Global Reach 是适用于 ExpressRoute 的附加功能。 使用 Global Reach，你可以将 ExpressRoute 线路链接在一起，以便在本地网络之间建立专用网络。 使用 ExpressRoute 连接到 Azure 虚拟 WAN 的分支要求 ExpressRoute Global Reach 彼此通信。

在此模型中，使用 ExpressRoute 连接到虚拟 WAN 集线器的每个分支都可以使用分支到 VNet 路径连接到 Vnet。 分支到分支流量不会传输中心，因为 ExpressRoute Global Reach 通过 Azure WAN 实现更最优的路径。

## <a name="security"></a>安全和策略控制

虚拟网络中心会相互连接，可能会看到所有传输流量。 可以在虚拟网络中心托管中心网络功能和服务，例如云路由、网络策略和安全性，以及 Internet 访问控制。

## <a name="next-steps"></a>后续步骤

使用虚拟 WAN 创建连接。

* [使用虚拟 WAN 的站点到站点连接](virtual-wan-site-to-site-portal.md)
* [使用虚拟 WAN 的 ExpressRoute 连接](virtual-wan-expressroute-portal.md)
