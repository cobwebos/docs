---
title: "Azure ExpressRoute 线路和路由域 | Microsoft 文档"
description: "本页提供 ExpressRoute 线路和路由域的概述。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6f0c5d8e-cc60-4a04-8641-2c211bda93d9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/14/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: cb88ce2960d63b8a1c5861913b2095bcc977938f
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017

---
# <a name="expressroute-circuits-and-routing-domains"></a>ExpressRoute 线路和路由域
 必须订购一条 ExpressRoute 线路才能通过连接提供商将本地基础结构连接到 Microsoft。 下图提供了你的 WAN 与 Microsoft 之间的连接的逻辑表示形式。

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>ExpressRoute 线路
ExpressRoute 线路表示通过连接提供商在本地基础结构与 Microsoft 云服务之间建立的逻辑连接。 你可以订购多条 ExpressRoute 线路。 每条线路可以位于相同或不同的区域，且可以通过不同的连接提供程序连接到本地。 

ExpressRoute 线路不会映射到任何物理实体。 线路由称为服务密钥 (s-key) 的标准 GUID 进行唯一标识。 服务密钥是在 Microsoft、连接提供商与你之间唯一交换的一条信息。 s-key 不是用于保证安全的机密。 ExpressRoute 线路与 s-key 之间存在 1:1 映射。

ExpressRoute 线路最多可以有三个独立的对等互连：Azure 公共、Azure 专用和 Microsoft。 每个对等互连是一对独立的 BGP 会话，每个会话采用冗余配置以实现高可用性。 ExpressRoute 线路与路由域之间存在 1:N (1 <= N <= 3) 映射。 每条 ExpressRoute 线路可以启用一个、两个或全部三个对等互连。

每条线路有固定的带宽（50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、10 Gbps），并映射到连接提供商和对等互连位置。 所选择的带宽在线路的所有对等互连之间共享。 

### <a name="quotas-limits-and-limitations"></a>配额、限制和局限性
默认配额和限制适用于每条 ExpressRoute 线路。 有关配额的最新信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。

## <a name="expressroute-routing-domains"></a>ExpressRoute 路由域
一条 ExpressRoute 线路有多个关联的路由域：Azure 公共、Azure 专用和 Microsoft。 在一对路由器上，每个路由域采用相同的配置（主动-主动，或负载分担配置）以实现高可用性。 Azure 服务分类为 Azure 公共和 Azure 专用以表示 IP 寻址方案。

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="private-peering"></a>专用对等互连
可以通过专用对等域来连接虚拟网络内部署的 Azure 计算服务（即虚拟机 (IaaS) 和云服务 (PaaS)）。 专用对等域被视为进入 Microsoft Azure 的核心网络的受信任扩展。 可以在核心网络和 Azure 虚拟网络 (VNet) 之间设置双向连接。 利用此对等互连，可以使用专用 IP 地址直接连接到虚拟机和云服务。  

可以将多个虚拟网络连接到专用对等域。 有关限制和局限性的信息，请查看[常见问题解答页](expressroute-faqs.md)。 有关限制的最新信息，请访问 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。  有关路由配置的详细信息，请参阅[路由](expressroute-routing.md)页。

### <a name="public-peering"></a>公共对等互连
Azure 存储、SQL 数据库和网站等服务是使用公共 IP 地址提供的。 你可以通过公共对等路由域私下连接到公共 IP 地址（包括云服务的 VIP）上托管的服务。 可以将公共对等域连接到外围网络，并从 WAN 连接到公共 IP 地址上的所有 Azure 服务，而无需通过 Internet 连接。 

始终会从 WAN 发起到 Microsoft Azure 服务的连接。 Microsoft Azure 服务无法通过此路由域发起到你网络的连接。 启用公共对等互连后，你将能够连接到所有 Azure 服务。 我们不允许选择要将路由播发到的服务。 可以在 [Microsoft Azure 数据中心 IP 范围](http://www.microsoft.com/download/details.aspx?id=41653)页上查看我们通过此对等互连播发给用户的前缀列表。 该页每周更新。

你可以在网络中定义自定义路由筛选器，以只使用所需的路由。 有关路由配置的详细信息，请参阅[路由](expressroute-routing.md)页。 

有关通过公共对等路由域支持的服务的详细信息，请参阅[常见问题解答页](expressroute-faqs.md)。 

### <a name="microsoft-peering"></a>Microsoft 对等互连
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

与其他所有 Microsoft Online Services（如 Office 365 服务）的连接将通过 Microsoft 对等互连建立。 我们将通过 Microsoft 对等路由域在你的 WAN 和 Microsoft 云服务之间启用双向连接。 你只能通过由你或你的连接提供商拥有的公共 IP 地址连接到 Microsoft 云服务，并且你必须遵守我们规定的所有规则。 有关详细信息，请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)页。

有关支持的服务、费用和配置的详细信息，请参阅[常见问题解答页](expressroute-faqs.md)。 有关提供 Microsoft 对等互连支持的连接提供商列表的信息，请参阅 [ExpressRoute 位置](expressroute-locations.md)页。

## <a name="routing-domain-comparison"></a>路由域比较
下表比较了三种路由域。

|  | **专用对等互连** | **公共对等互连** | **Microsoft 对等互连** |
| --- | --- | --- | --- |
| **每个对等互连支持的最大前缀数** |默认情况下为 4000，而 ExpressRoute 高级版支持 10,000 |200 |200 |
| **支持的 IP 地址范围** |你的 WAN 中任何有效的 IPv4 地址。 |由你或你的连接提供商拥有的公共 IPv4 地址。 |由你或你的连接提供商拥有的公共 IPv4 地址。 |
| **AS 编号要求** |专用和公共 AS 编号。 如果选择使用公共 AS 编号，必须拥有该编号。 |专用和公共 AS 编号。 但是，必须证明对公共 IP 地址的所有权。 |专用和公共 AS 编号。 但是，必须证明对公共 IP 地址的所有权。 |
| **路由接口 IP 地址** |RFC1918 和公共 IP 地址 |在路由注册表中向你注册的公共 IP 地址。 |在路由注册表中向你注册的公共 IP 地址。 |
| **MD5 哈希支持** |是 |是 |是 |

可以选择启用一个或多个路由域作为 ExpressRoute 线路的一部分。 若要将这些路由域合并成单个路由域，可以选择将所有路由域放置在同一个 VPN 中。 此外，还可以如图所示，将它们放置在不同的路由域中。 建议的配置是将专用对等链路直接连接到核心网络，并将公共和 Microsoft 对等链路连接到外围网络。

如果你选择使用所有三个对等会话，必须使用三对 BGP 会话（每一对用于一个对等类型）。 BGP 会话对提供高度可用的链接。 如果要通过第 2 层连接提供商进行连接，则需要负责配置和管理路由。 可以通过查看设置 ExpressRoute 的[工作流](expressroute-workflows.md)了解详细信息。

## <a name="next-steps"></a>后续步骤
* 查找服务提供商。 请参阅 [ExpressRoute 服务提供商和位置](expressroute-locations.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
* 配置 ExpressRoute 连接。
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)
  * [配置路由（线路对等互连）](expressroute-howto-routing-classic.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)


