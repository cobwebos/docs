---
title: "优化 ExpressRoute 路由：Azure | Microsoft Docs"
description: "本页详细说明当 Microsoft 与企业网络之间存在多个 ExpressRoute 连接线路时如何优化路由。"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
ms.assetid: fca53249-d9c3-4cff-8916-f8749386a4dd
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2017
ms.author: charwen
ms.openlocfilehash: c3a85b9445d69330c3f6c7d298169efddb6ecca0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-expressroute-routing"></a>优化 ExpressRoute 路由
有多个 ExpressRoute 线路时，可以通过多个路径连接到 Microsoft。 结果就是，所采用的路由可能不是最理想的 - 也就是说，流量可能会经历较长的路径才能到达 Microsoft，而 Microsoft 的流量也可能会经历较长的路径才能到达网络。 网络路径越长，延迟越严重。 延迟对应用程序性能和用户体验有直接影响。 本文将详述此问题，并说明如何使用标准路由技术来优化路由。

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>从客户到 Microsoft 的欠佳路由
让我们通过一个示例来详细了解路由问题。 假设你在美国有两个办公室，一个在洛杉矶，一个在纽约。 办公室通过广域网 (WAN) 进行连接，该广域网可能是自己的主干网络，也可能是你服务提供商的 IP VPN。 有两个 ExpressRoute 线路，一个在美国西部，一个在美国东部，也通过 WAN 连接。 显然，可以通过两个路径连接到 Microsoft 网络。 现在，假设你在美国西部和美国东部都有 Azure 部署（例如 Azure 应用服务）。 目的是将洛杉矶的用户连接到 Azure 美国西部，将纽约的用户连接到 Azure 美国东部，因为服务管理员宣称每个办公室的用户都能够访问附近的 Azure 服务以获取最佳体验。 遗憾的是，此计划适用于东海岸用户，但不适用于西海岸用户。 以下是问题的原因。 在每个 ExpressRoute 线路上，我们会向你播发 Azure 美国东部的前缀 (23.100.0.0/16) 和 Azure 美国西部的前缀 (13.100.0.0/16)。 如果不知道哪个前缀是来自哪个区域，则无法进行区别对待。 WAN 网络可能会认为这两种前缀更靠近美国东部而非美国西部，因此会将两个办公室的用户都路由到美国东部的 ExpressRoute 线路。 结果就是，洛杉矶办公室的许多用户会很不满意。

![ExpressRoute 案例 1 问题 - 从客户到 Microsoft 的路由欠佳](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>解决方案：使用 BGP 社区
要优化两个办公室的用户的路由，需要知道哪个前缀来自 Azure 美国西部，哪个前缀来自 Azure 美国东部。 我们使用 [BGP 社区值](expressroute-routing.md)对此信息进行编码。 我们向每个 Azure 区域分配了唯一的 BGP 社区值，例如：为美国东部分配“12076:51004”，为美国西部分配“12076:51006”。 现在，知道了哪个前缀来自哪个 Azure 区域，因此可以配置哪个 ExpressRoute 线路应该为首选线路。 由于我们使用 BGP 来交换路由信息，因此可以使用 BGP 的“本地首选项”来影响路由。 在我们的示例中，可以在美国西部将比美国东部更高的本地首选项值分配给 13.100.0.0/16。类似地，可以在美国东部将比美国西部更高的本地首选项值分配给 23.100.0.0/16。 此配置将确保当通往 Microsoft 的两个路径都可用时，在洛杉矶的用户将使用美国西部的 ExpressRoute 线路连接到 Azure 美国西部，而你在纽约的用户将使用美国东部的 ExpressRoute 连接到 Azure 美国东部。 两边的路由都获得了优化。 

![ExpressRoute 案例 1 解决方法 - 使用 BGP 社区](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> 对于从客户到 Azure 虚拟网络的路由，同样可以应用“本地首选项”技术。 我们不会在从 Azure 播发到网络的前缀中标记 BGP 社区值。 但是，由于你知道哪个虚拟网络部署靠近哪间办公室，因此可以相应地配置路由器，以优先使用某一条 ExpressRoute 线路。
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>从 Microsoft 到客户的欠佳路由
下面是另一示例：从 Microsoft 发出的连接需要更长的路径才能到达网络。 在此示例中，在 [混合环境](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx)中使用本地 Exchange 服务器和 Exchange Online。 办公室都连接到 WAN。 通过两个 ExpressRoute 线路将两个办公室中的本地服务器的前缀都播发到 Microsoft。 在进行邮箱迁移等情况下，Exchange Online 会发起到本地服务器的连接。 遗憾的是，到洛杉矶办公室的连接会在遍历整个大陆回到西海岸之前路由到美国东部的 ExpressRoute 线路。 问题原因类似于第一个。 在没有任何提示的情况下，Microsoft 网络无法判断哪个客户前缀靠近美国东部，哪个客户前缀靠近美国西部。 它碰巧选取了到洛杉矶办公室的错误路径。

![ExpressRoute 案例 2 - 从 Microsoft 到客户的路由欠佳](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>解决方案：使用 AS PATH 追加
此问题有两种解决方案。 第一种解决方案是，直接你将洛杉矶办公室的本地前缀 177.2.0.0/31 播发到美国西部的 ExpressRoute 线路上，你将纽约办公室的本地前缀 177.2.0.2/31 播发到美国东部的 ExpressRoute 线路上。 结果就是，Microsoft 只能通过一个路径连接到每个办公室。 路径不再模拟两可，路由得到了优化。 使用此设计时，需要考虑故障转移策略。 在通过 ExpressRoute 连接到 Microsoft 的路径断开的情况下，需确保 Exchange Online 仍能连接到本地服务器。 

第二种解决方案是，继续将两种前缀播发到两个 ExpressRoute 线路上，但除此之外你还需提示我们哪个前缀靠近你的哪个办公室。 由于我们支持 BGP AS Path 追加，因此可以对前缀的 AS Path 进行配置，使之影响路由。 在此示例中，可以延长美国东部 172.2.0.0/31 的 AS PATH，这样我们就会首选美国西部的 ExpressRoute 线路来传送目标为该前缀的流量（因为我们的网络会认为在西部，到此前缀的路径较短）。 类似地，可以延长美国西部 172.2.0.2/31 的 AS PATH，这样我们就会首选美国东部的 ExpressRoute 线路。 路由是针对这两处办公室进行优化的。 根据此设计，如果一个 ExpressRoute 线路断开，Exchange Online 仍可通过其他 ExpressRoute 线路以及 WAN 访问你。 

> [!IMPORTANT]
> 对于在 Microsoft 对等互连上接收的前缀，我们会删除 AS PATH 中的专用 AS 数字。 需在 AS PATH 中追加公共 AS 数字才能影响 Microsoft 对等互连的路由。
> 
> 

![ExpressRoute 案例 2 解决方法 - 使用 AS PATH 附加](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> 尽管此处给出的示例针对的是 Microsoft 和公共对等互连，但我们也支持专用对等互连的相同功能。 此外，AS Path 追加会在单个 ExpressRoute 线路内工作，以影响主要和次要路径的选择。
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>虚拟网络之间的路由欠佳
可以通过将虚拟网络（也称为“VNet”）链接到 ExpressRoute 线路，来实现虚拟网络之间的通信。 将 VNet 链接到多条 ExpressRoute 线路时，VNet 之间的路由可能欠佳。 我们分析一个示例。 有两条 ExpressRoute 线路，一条在美国西部，一条在美国东部。 每个区域中有两个 VNet。 Web 服务器部署在一个 VNet 中，应用程序服务器部署在另一个 VNet 中。 为实现冗余，将每个区域中的两个 VNet 链接到了本地 ExpressRoute 线路和远程 ExpressRoute 线路。 如下所示，一个 VNet 与另一个 VNet 之间有两条路径。 VNet 不知道哪条 ExpressRoute 线路是本地的，哪条是远程的。 由于 VNet 针对负载均衡的 VNet 间流量执行相等成本多路径 (ECMP) 路由，因此某些流量会经历更长的路径，最终路由到远程 ExpressRoute 线路。

![ExpressRoute 案例 3 - 虚拟网络之间的路由欠佳](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>解决方法：将高权重分配给本地连接
解决方法很简单。 由于你知道 VNet 和线路的位置，因此可以告诉我们，每个 VNet 应该优先使用哪条路径。 具体而言，在本示例中，可向本地连接分配一个比远程连接更高的权重（请参阅[此处](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)的配置示例）。 当 VNet 收到多个连接上的另一个 VNet 的前缀时，将优先选择具有最高权重的连接将流量发送到该前缀。

![ExpressRoute 案例 3 解决方法 - 将高权重分配给本地连接](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> 如果有多条 ExpressRoute 线路，也可以影响从 VNet 到本地网络的路由：配置连接的权重而不是应用 AS PATH 附加（上面第二种方案中所述的技术）。 对于每个前缀，我们在确定如何发送流量时，始终会查看 AS Path 长度前面的连接权重。
>
>
