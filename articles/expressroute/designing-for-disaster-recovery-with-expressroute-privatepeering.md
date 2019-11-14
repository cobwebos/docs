---
title: Azure ExpressRoute：针对灾难恢复进行设计
description: 本页提供有关使用 Azure ExpressRoute 实现灾难恢复的体系结构建议。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076689"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>使用 ExpressRoute 专用对等互连进行灾难恢复设计

ExpressRoute 旨在实现高可用性，以便与 Microsoft 资源建立运营商级专用网络连接。 换句话说，Microsoft 网络中的 ExpressRoute 路径不存在单一故障点。 有关最大化 ExpressRoute 线路可用性的设计注意事项，请参阅[使用 ExpressRoute 进行高可用性设计][HA]。

但是，考虑到墨菲的一句格言“如果某件事可能会出错，那么它就会出错”，本文将重点分析的解决方案并不局限于使用单条 ExpressRoute 线路可以解决的故障。 换而言之，本文将会探讨使用异地冗余 ExpressRoute 线路构建可靠的后端网络连接以实现灾难恢复时的网络体系结构考虑因素。

## <a name="need-for-redundant-connectivity-solution"></a>对冗余连接解决方案的需求

在某些情况下，（Microsoft、网络服务提供商、客户或其他云服务提供商的）整个区域性服务可能会降级。 造成此类区域范围的服务影响的根本原因包括自然灾难。 因此，若要实现业务连续性并使任务关键型应用程序保持正常运行，必须规划好灾难恢复。   

无论是在 Azure 区域、本地还是其他任何位置运行任务关键型应用程序，都可以使用另一个 Azure 区域作为故障转移站点。 以下文章介绍了从应用程序和前端访问角度进行的灾难恢复：

- [企业级灾难恢复][Enterprise DR]
- [使用 Azure Site Recovery 实现 SMB 灾难恢复][SMB DR]

如果你依赖于使用本地网络与 Microsoft 之间的 ExpressRoute 连接来执行任务关键的操作，则还应该在灾难恢复计划中包含异地冗余的网络连接。 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>使用多条 ExpressRoute 线路时的难点

如果你使用多个连接来互连一组相同的网络，则会在网络之间引入并行路径。 未正确架构的并行路径可能会导致非对称路由。 如果路径中包含有状态实体（例如 NAT、防火墙），则非对称路由可能会阻止流量流。  通常，ExpressRoute 专用对等路径中不会包含 NAT 或防火墙等有状态实体。 因此，通过 ExpressRoute 专用对等互连进行非对称路由不一定会阻止流量流。
 
但是，如果你对异地冗余的并行路径中的流量进行负载均衡，则不管是否存在有状态实体，都会遇到不一致的网络性能。 本文介绍如何解决这些难题。

## <a name="small-to-medium-on-premises-network-considerations"></a>中小型本地网络的考虑因素

让我们考虑下图所示的示例网络。 在该示例中，Contoso 的本地位置与 Contoso 的 Azure 区域中 VNet 之间建立了异地冗余的 ExpressRoute 连接。 在图中，绿色实线表示首选路径（通过 ExpressRoute 1），虚线表示备用路径（通过 ExpressRoute 2）。

[![1]][1]

为灾难恢复设计 ExpressRoute 连接时，需要考虑以下因素：

- 使用异地冗余的 ExpressRoute 线路
- 对不同的 ExpressRoute 线路使用多样化的服务提供商网络
- 为[高可用性][HA]设计每条 ExpressRoute 线路
- 在客户网络上的不同位置终止不同的 ExpressRoute 线路

默认情况下，如果你在所有 ExpressRoute 路径中以相同的方式播发路由，则 Azure 将使用成本相同的多路径 (ECMP) 路由对所有 ExpressRoute 路径中的本地绑定流量进行负载均衡。

但是，在使用异地冗余的 ExpressRoute 线路时，我们需要考虑到不同网络路径的不同网络性能（尤其是网络延迟）。 若要在正常操作期间获得更加一致的网络性能，可以优先使用延迟最低的 ExpressRoute 线路。

可以使用以下方法之一来影响 Azure，以优先使用其中的一条 ExpressRoute 线路：

- 通过首选的 ExpressRoute 线路而不是其他 ExpressRoute 线路播发更具体的路由
- 在用于将虚拟网络链接到首选 ExpressRoute 线路的连接上配置较高的连接权重
- 通过 AS 路径（AS 路径预置）较长的不太选用的 ExpressRoute 路线播发路由

### <a name="more-specific-route"></a>更具体的路由

下图演示了如何使用更具体的路由播发来影响 ExpressRoute 路径选择。 在演示的示例中，Contoso 的本地 /24 IP 范围通过首选路径 (ExpressRoute 1) 播发为两个 /25 地址范围，并通过备用路径 (ExpressRoute 2) 播发为 /24 地址范围。

[![2]][2]

由于 /25 相比 /24 而言更具体，在正常状态下，Azure 将通过 ExpressRoute 1 将流量发送到 10.1.11.0/24。 如果 ExpressRoute 1 的连接已关闭，则 VNet 只会看到通过 ExpressRoute 2 进行的 10.1.11.0/24 路由播发；因此，在这种故障状态下会使用备用线路。

### <a name="connection-weight"></a>连接权重

以下屏幕截图演示了如何通过 Azure 门户配置 ExpressRoute 连接的权重。

[![3]][3]

下图演示了如何使用连接权重来影响 ExpressRoute 路径选择。 默认连接权重为 0。 在以下示例中，ExpressRoute 1 的连接权重配置为 100。 当 VNet 收到通过多条 ExpressRoute 线路播发的路由前缀时，VNet 将优先使用权重最高的连接。

[![4]][4]

如果 ExpressRoute 1 的连接已关闭，则 VNet 只会看到通过 ExpressRoute 2 进行的 10.1.11.0/24 路由播发；因此，在这种故障状态下会使用备用线路。

### <a name="as-path-prepend"></a>AS 路径预置

下图演示了如何使用 AS 路径前置来影响 ExpressRoute 路径选择。 在图中，通过 ExpressRoute 1 进行的路由播发指示 eBGP 的默认行为。 在通过 ExpressRoute 2 进行的路由播发中，本地网络的 ASN 额外预置在路由的 AS 路径中。 通过多条 ExpressRoute 线路收到相同的路由时，根据 eBGP 路由选择过程，VNet 将优先使用 AS 路径最短的路由。 

[![5]][5]

如果 ExpressRoute 1 的两个连接都关闭，则 VNet 只会看到通过 ExpressRoute 2 进行的 10.1.11.0/24 路由播发。 因此，较长的 AS 路径不起作用。 在这种故障状态下，将使用备用线路。

使用任一方法时，如果你通过影响 Azure 来优先使用其中的一条 ExpressRoute 线路，则还需要确保本地网络也对 Azure 绑定流量优先使用同一条 ExpressRoute 路径，以避免非对称流。 通常，会使用本地首选项值来影响本地网络，以优先使用其中的一条 ExpressRoute 线路。 本地首选项是一个内部 BGP (iBGP) 指标。 优先使用本地首选项值最高的 BGP 路由。

> [!IMPORTANT]
> 使用特定的 ExpressRoute 线路作为备用线路时，需要主动对其进行管理，并定期测试故障转移操作。 
> 

## <a name="large-distributed-enterprise-network"></a>大型分布式企业网络

如果你使用大型分布式企业网络，则可能已部署多条 ExpressRoute 线路。 本部分介绍如何使用主动-主动 ExpressRoute 线路（无需额外的备用线路）设计灾难恢复。 

让我们考虑下图所示的示例。 在此示例中，Contoso 已通过两个不同对等互连位置的 ExpressRoute 线路，将两个本地位置连接到两个不同 Azure 区域中的两个 Contoso IaaS 部署。 

[![6]][6]

灾难恢复的架构方式会影响跨区域到跨位置（区域 1/区域 2 到位置 2/位置 1）流量的路由方式。 假设有两个不同的灾难恢复体系结构以不同的方式路由跨区域-跨位置的流量。

### <a name="scenario-1"></a>方案 1

在第一种方案中，让我们设计这样一种灾难恢复方式：Azure 区域与本地网络之间的所有流量在稳定状态下通过本地 ExpressRoute 线路传送。 如果本地 ExpressRoute 线路出现故障，则使用远程 ExpressRoute 线路传送 Azure 与本地网络之间的所有流量。

下图演示了方案 1。 在此图中，绿线表示 VNet1 与本地网络之间的流量流路径。 蓝线表示 VNet2 与本地网络之间的流量流路径。 实线表示处于稳定状态的所需路径，虚线表示在携带稳定状态流量流的相应 ExpressRoute 线路发生故障时使用的流量路径。 

[![7]][7]

可以使用连接权重来架构方案，以影响 VNet 优先使用与本地对等互连位置 ExpressRoute 建立的连接来传送本地网络绑定的流量。 若要完成该解决方案，需确保对称反转流量流。 可以在 BGP 路由器（其上的 ExpressRoute 线路在本地端终止）之间的 iBGP 会话中使用本地首选项，以优先使用 ExpressRoute 线路。 下图演示了该解决方案。 

[![8]][8]

### <a name="scenario-2"></a>方案 2

下图演示了方案 2。 在此图中，绿线表示 VNet1 与本地网络之间的流量流路径。 蓝线表示 VNet2 与本地网络之间的流量流路径。 在稳定状态下（图中以实线表示），VNet 与本地位置之间的所有流量主要通过 Microsoft 主干网络传送；只有在 ExpressRoute 故障状态下（图中以虚线表示），这些流量才通过本地位置之间的互连传送。

[![9]][9]

下图演示了该解决方案。 如图所示，可以使用更具体的路由（选项 1）或 AS 路径预置（选项 2）来架构方案，以影响 VNet 路径选择。 若要影响 Azure 绑定流量的本地网络路由选择，需要将本地位置之间的互连配置为不常选用。 将互连链接配置为首选连接的方式取决于在本地网络中使用的路由协议。 可以配合 iBGP 或 IGP 指标（OSPF 或 IS）使用本地首选项。

[![10]][10]


## <a name="next-steps"></a>后续步骤

本文已介绍如何设计 ExpressRoute 线路专用对等互连的灾难恢复。 以下文章介绍了从应用程序和前端访问角度进行的灾难恢复：

- [企业级灾难恢复][Enterprise DR]
- [使用 Azure Site Recovery 实现 SMB 灾难恢复][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "中小型本地网络的考虑因素"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "使用更具体的路由影响路径选择"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "通过 Azure 门户配置连接权重"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "使用连接权重影响路径选择"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "使用 AS 路径预置影响路径选择"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "大型分布式本地网络考虑因素"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "方案1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "主动-主动 ExpressRoute 线路解决方案 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "方案 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "主动-主动 ExpressRoute 线路解决方案 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





