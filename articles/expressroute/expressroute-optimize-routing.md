<properties
   pageTitle="优化 ExpressRoute 路由 | Microsoft Azure"
   description="本页详细介绍了在客户可以通过多个 ExpressRoute 线路在 Microsoft 与客户的公司网络之间进行连接时，如何优化路由。"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="charwen"/>

# 优化 ExpressRoute 路由
当你有多个 ExpressRoute 线路时，可以通过多个路径连接到 Microsoft。结果就是，你所采用的路由可能不是最理想的 - 也就是说，你的流量可能会经历较长的路径才能到达 Microsoft，而 Microsoft 的流量也可能会经历较长的路径才能到达你的网络。网络路径越长，延迟越严重。延迟对应用程序性能和用户体验有直接影响。本文将详述此问题，并说明如何使用标准路由技术来优化路由。

## 非最优路由情况 1
让我们通过一个示例来详细了解路由问题。假设你在美国有两个办公室，一个在洛杉矶，一个在纽约。你的办公室通过广域网 (WAN) 进行连接，该广域网可能是你自己的主干网络，也可能是你服务提供商的 IP VPN。你有两个 ExpressRoute 线路，一个在美国西部，一个在美国东部，也通过 WAN 连接。显然，你可以通过两个路径连接到 Microsoft 网络。现在，假设你在美国西部和美国东部都有 Azure 部署（例如 Azure App Service）。你的目的是将洛杉矶的用户连接到 Azure 美国西部，将纽约的用户连接到 Azure 美国东部，因为你的服务管理员宣称每个办公室的用户都能够访问附近的 Azure 服务以获取最佳体验。遗憾的是，此计划适用于东海岸用户，但不适用于西海岸用户。以下是问题的原因。在每个 ExpressRoute 线路上，我们会向你播发 Azure 美国东部的前缀 (23.100.0.0/16) 和 Azure 美国西部的前缀 (13.100.0.0/16)。如果你不知道哪个前缀是来自哪个区域，则无法进行区别对待。你的 WAN 网络可能会认为这两种前缀更靠近美国东部而非美国西部，因此会将两个办公室的用户都路由到美国东部的 ExpressRoute 线路。结果就是，洛杉矶办公室的许多用户会很不满意。

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### 解决方案：使用 BGP 社区
若要优化两个办公室的用户的路由，你需要知道哪个前缀来自 Azure 美国西部，哪个前缀来自 Azure 美国东部。我们使用 [BGP 社区值](expressroute-routing.md)对此信息进行编码。我们向每个 Azure 区域分配了唯一的 BGP 社区值，例如：为美国东部分配“12076:51004”，为美国西部分配“12076:51006”。现在，你知道了哪个前缀来自哪个 Azure 区域，因此可以配置哪个 ExpressRoute 线路应该为首选线路。由于我们使用 BGP 来交换路由信息，因此你可以使用 BGP 的“本地首选项”来影响路由。在我们的示例中，你可以在美国西部将比美国东部更高的本地首选项值分配给 13.100.0.0/16。类似地，你可以在美国东部将比美国西部更高的本地首选项值分配给 23.100.0.0/16。此配置将确保当通往 Microsoft 的两个路径都可用时，你在洛杉矶的用户将使用美国西部的 ExpressRoute 线路连接到 Azure 美国西部，而你在纽约的用户将使用美国东部的 ExpressRoute 连接到 Azure 美国东部。两边的路由都获得了优化。

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## 非最优路由情况 2
下面是另一示例：从 Microsoft 发出的连接需要更长的路径才能到达你的网络。在此示例中，你在[混合环境](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx)中使用本地 Exchange 服务器和 Exchange Online。你的办公室都连接到 WAN。你通过两个 ExpressRoute 线路将两个办公室中的本地服务器的前缀都播发到 Microsoft。在进行邮箱迁移等情况下，Exchange Online 会发起到本地服务器的连接。遗憾的是，到你洛杉矶办公室的连接会在遍历整个大陆回到西海岸之前路由到美国东部的 ExpressRoute 线路。问题原因类似于第一个。在没有任何提示的情况下，Microsoft 网络无法判断哪个客户前缀靠近美国东部，哪个客户前缀靠近美国西部。它碰巧选取了到你洛杉矶办公室的错误路径。

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### 解决方案：使用 AS PATH 追加
此问题有两种解决方案。第一种解决方案是，直接将你洛杉矶办公室的本地前缀 177.2.0.0/31 播发到美国西部的 ExpressRoute 线路上，将你纽约办公室的本地前缀 177.2.0.2/31 播发到美国东部的 ExpressRoute 线路上。结果就是，Microsoft 只能通过一个路径连接到你的每个办公室。路径不再模拟两可，路由得到了优化。使用此设计时，需要考虑故障转移策略。在通过 ExpressRoute 连接到 Microsoft 的路径断开的情况下，需确保 Exchange Online 仍能连接到你的本地服务器。

第二种解决方案是，继续将两种前缀播发到两个 ExpressRoute 线路上，但除此之外你还需提示我们哪个前缀靠近你的哪个办公室。由于我们支持 BGP AS Path 追加，因此你可以对前缀的 AS Path 进行配置，使之影响路由。在此示例中，你可以延长美国东部 172.2.0.0/31 的 AS PATH，这样我们就会首选美国西部的 ExpressRoute 线路来传送目标为该前缀的流量（因为我们的网络会认为在西部，到此前缀的路径较短）。类似地，你可以延长美国西部 172.2.0.2/31 的 AS PATH，这样我们就会首选美国东部的 ExpressRoute 线路。路由是针对这两处办公室进行优化的。根据此设计，如果一个 ExpressRoute 线路断开，Exchange Online 仍可通过其他 ExpressRoute 线路以及你的 WAN 访问你。

>[AZURE.IMPORTANT] 对于在 Microsoft 对等互连上接收的前缀，我们会删除 AS PATH 中的专用 AS 数字。需在 AS PATH 中追加公共 AS 数字才能影响 Microsoft 对等互连的路由。

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

<!---HONumber=AcomDC_0921_2016-->