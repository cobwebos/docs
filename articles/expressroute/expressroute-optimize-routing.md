---
title: "优化 ExpressRoute 路由： Azure |Microsoft 文档"
description: "此页提供有关如何优化路由时你有多个连接 Microsoft 和 corp 网络之间的 ExpressRoute 线路的详细信息。"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="optimize-expressroute-routing"></a>优化 ExpressRoute 路由
当你有多条 ExpressRoute 线路时，必须要连接到 Microsoft 的多个路径。 因此，并非最佳路由可能会发生的即你的流量可能需要较长的路径来访问 Microsoft 和 Microsoft 到你的网络。 较长的网络路径，更高的延迟。 造成延迟有直接影响应用程序性能和用户体验。 本文将说明这一问题，并介绍如何优化使用标准的路由技术进行路由。

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>并非最佳从客户路由到 Microsoft
通过一个示例，让我们仔细查看一下路由问题。 假设你有两个在美国，一个在洛杉矶，一个在纽约办公室。 您的办公室连接上广域网 (WAN)，它可以是你自己 backbone 网络或服务提供商的 IP VPN。 你具有两个 ExpressRoute 线路，一个在美国西部，一个在美国东部，还连接在广域网上。 显然，您必须连接到 Microsoft 网络的两个路径。 现在在美国西部和美国东部假设你有 Azure 部署 （例如，Azure 应用程序服务）。 您的意图是你在洛杉矶的用户连接到 Azure 美国西部和你的用户位于纽约到 Azure 美国东部，因为你的服务管理员在播发中每个办事处的用户访问的附近的 Azure 服务的最佳体验。 遗憾的是，计划制定出适用于东海岸用户但不是能为西海岸用户。 问题的原因如下所示。 在每条 ExpressRoute 线路，我们播发给你在 Azure 美国东部 (23.100.0.0/16) 的前缀和 Azure 美国西部 (13.100.0.0/16) 中的前缀。 如果你不知道哪个前缀是从哪个区域，你不能将其以不同的方式。 你的 WAN 网络可能会认为这两个前缀更接近于美国东部比美国西部，并因此将这两个 office 用户路由到 ExpressRoute 线路在美国东部。 在结束时，你将洛杉矶办公室中有许多是用户。

![ExpressRoute 情况 1 问题-并非最佳从客户路由到 Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>解决方案： 使用 BGP 社区
若要优化的这两个 office 用户路由，你需要知道哪个前缀是从 Azure 美国西部和从 Azure 美国东部。 我们对此信息进行编码使用[BGP 社区值](expressroute-routing.md)。 我们已分配一个唯一的 BGP 社区值给每个 Azure 区域例如"12076:51004"对于美国东部、"12076:51006"有关美国西部。 既然你知道哪个前缀是从哪个 Azure 区域，你可以配置哪些 ExpressRoute 线路应为首选。 因为我们使用 BGP 交换路由的信息，你可以使用 BGP 的本地首选项来影响路由。 在本示例中，可以将更高版本本地首选项值转换为 13.100.0.0/16 中比在美国东部、 美国西部并与此类似，一个更高版本的本地首选项值分配给 23.100.0.0/16 中比在美国西部的美国东部。 此配置将确保，Microsoft 这两个路径可用时，你在洛杉矶的用户将在美国西部，而你的用户位于纽约到 Azure 美国东部在美国东部采用 ExpressRoute 连接到 Azure 美国西部花费 ExpressRoute 线路。 两端上的路由进行了优化。 

![ExpressRoute 情况 1 的解决方案使用 BGP 社区](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> 相同的技术，使用本地首选项，可以应用于从客户路由到 Azure 虚拟网络。 我们不标记 BGP 社区值于播发从 Azure 到你的网络的前缀。 但是，由于你知道哪些虚拟网络的部署即将其办公室，你可以配置你的路由器相应地首选到另一条 ExpressRoute 线路。
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>从 Microsoft 路由到客户并非最佳
下面是另一个示例，其中来自 Microsoft 的连接要花费较长的路径来访问你的网络。 在这种情况下，你使用本地 Exchange 服务器和在 Exchange Online[混合环境](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx)。 您的办公室都连接到 WAN。 您播发的前缀的两个您向 Microsoft 通过两个 ExpressRoute 线路的办公室中你的本地服务器。 Exchange Online 将启动到在情况下，例如邮箱迁移的本地服务器的连接。 遗憾的是，到洛杉矶办公室的连接将连接到 ExpressRoute 线路在美国东部路由到西海岸遍历整个 continent 后之前。 问题的原因是类似于第一个。 而无需任何提示，Microsoft 网络无法判断哪些客户前缀即将美国东部和哪个订阅是接近美国西部。 它发生在办公室中洛杉矶到选取错误的路径。

![ExpressRoute 情况 2-从 Microsoft 路由到客户并非最佳](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>解决方案： 使用 AS 路径预先计算
有两个问题的解决方案。 第一个参数是只需播发用于洛杉矶办公，177.2.0.0/31，在美国西部 ExpressRoute 线路上的本地前缀和本地前缀为纽约办公室，177.2.0.2/31 上在美国东部 ExpressRoute 线路。 因此，没有连接到每个办事处的 microsoft 只有一条路径。 没有歧义，且优化了路由。 利用此设计，你需要考虑你的故障转移策略。 事件中的路径通过 ExpressRoute Microsoft 已损坏，你需要确保，Exchange Online 仍可连接到你的本地服务器。 

第二个解决方案是前缀的继续播发同时在这两个 ExpressRoute 线路，前缀，并且除了你向我们提供的提示是前缀的接近您的办公室中的哪一个。 因为我们支持 BGP AS 路径预先计算，你可以配置要影响路由前缀的 AS 路径。 在此示例中，你会延长在美国东部 172.2.0.0/31 AS 路径，以便我们会更喜欢在美国西部发往此前缀 （如我们的网络，将认为此前缀到路径较短在西部） 流量的 ExpressRoute 线路。 同样会延长在美国西部 172.2.0.2/31 AS 路径，以便我们将更喜欢在美国东部 ExpressRoute 线路。 路由针对两个办公室进行优化。 与此设计中，如果一个 ExpressRoute 线路已损坏，Exchange Online 仍然可以访问你通过另一个 ExpressRoute 线路与你的 WAN。 

> [!IMPORTANT]
> 我们删除私有 AS 编号的前缀 Microsoft 对等上接收到的 AS 路径中。 你需要追加公共 AS 编号影响 Microsoft 对等的路由将 AS 路径中。
> 
> 

![ExpressRoute 情况 2 解决方案-用作路径预先计算](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> 虽然此处提供的示例是针对 Microsoft 和公共对等互连，我们的私有对等互连支持相同的功能。 在一个单个 ExpressRoute 线路，以影响主和辅助路径的所选内容中还，AS 路径预先计算工作。
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>并非最佳的虚拟网络之间路由
使用 ExpressRoute，你可以启用虚拟网络与虚拟网络 （也称为"VNet"） 方法将其链接到 ExpressRoute 线路的通信。 当你将它们链接到多条 ExpressRoute 线路时，次优路由 Vnet 之间可能发生。 让我们考虑一个示例。 你有两个 ExpressRoute 线路，一个在美国西部，一个在美国东部。 在每个区域中，您有两个 Vnet。 在一个 VNet 中部署你的 web 服务器和另一部分中的应用程序服务器。 为实现冗余，每个区域中的两个 Vnet 链接到本地的 ExpressRoute 线路和远程的 ExpressRoute 线路。 按如下所示，从每个 VNet 有两个路径到另一个 VNet。 Vnet 不知道的 ExpressRoute 线路是本地还是远程哪一个。 因此一样相等成本多路径 (ECMP) 负载平衡虚拟网络间 VNet 流量路由到某些流量流将花费更长的路径，并且在远程的 ExpressRoute 线路路由。

![ExpressRoute 情况 3-并非最佳的虚拟网络之间路由](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>解决方案： 将高权重分配给本地连接
解决方法很简单。 因为你知道 Vnet 和线路所在位置，你可以告诉我们应该会希望每个 VNet 的路径。 专门为此示例中，你可以将更高版本的权重分配给比远程连接到的本地连接 (请参阅配置示例[此处](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection))。 VNet 接收多个连接上的其他 VNet 的前缀时它将选择具有最高权重发送发往该前缀流量连接。

![ExpressRoute 情况 3 解决方案-分配给本地连接的高权重](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> 你还可以影响路由从 VNet 到本地网络，如果你有多条 ExpressRoute 线路，通过配置的连接而不是应用 AS 路径预先计算，上面第二个方案中所述的技术的权重。 针对每个前缀，我们将始终查看之前 AS 路径长度的连接权重时决定如何发送流量。
>
>
