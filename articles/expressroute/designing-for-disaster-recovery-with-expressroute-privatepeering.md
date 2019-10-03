---
title: 使用 Azure ExpressRoute 实现灾难恢复设计 |Microsoft Docs
description: 使用 Azure ExpressRoute 时，此页提供体系结构建议进行灾难恢复。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466069"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>使用 ExpressRoute 专用对等互连进行灾难恢复设计

ExpressRoute 专为高可用性来提供运营商级 Microsoft 资源的专用网络连接。 换而言之，没有任何单点故障，Microsoft 网络中的 ExpressRoute 路径中。 若要最大化 ExpressRoute 线路的可用性的设计注意事项，请参阅[以使用 ExpressRoute 实现高可用性设计][HA]。

但是，采用 Murphy 的常用句箴言-*如果任何内容可能会出现错误，它将*-纳入考虑范围，在这篇文章中让我们侧重于解决方案超过使用单个 ExpressRoute 线路能够解决的故障。 换而言之，在这篇文章中让我们深入了解有关构建功能强大的后端用于灾难恢复使用异地冗余的 ExpressRoute 线路的网络连接的网络体系结构注意事项。

## <a name="need-for-redundant-connectivity-solution"></a>需要冗余连接解决方案

有的可能性，其中整个区域服务 （为它的 microsoft、 网络服务提供商、 客户或其他云服务提供商） 获取已降级的实例。 此类区域范围的服务的影响的根本原因包括自然灾难。 因此，业务连续性和关键任务关键应用程序是用于灾难恢复规划很重要。   

不管您是否运行任务关键应用程序中的 Azure 区域或在本地或其他任何位置，可以使用另一个 Azure 区域，作为故障转移站点。 以下文章地址灾难恢复从应用程序和前端访问透视：

- [企业级灾难恢复][Enterprise DR]
- [使用 Azure Site Recovery 进行 SMB 灾难恢复][SMB DR]

如果你依赖本地网络与 Microsoft 之间的关键操作的 ExpressRoute 连接，灾难恢复计划还应包括异地冗余的网络连接。 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>使用多条 ExpressRoute 线路的挑战

时互连一组相同的使用多个连接网络、 你引入网络之间的平行路径。 并行路径，当未正确构建时，可能会导致非对称路由。 如果在路径中包含有状态的实体 （例如，NAT、 防火墙），非对称路由可能会阻止流量流。  通常情况下，通过 ExpressRoute 专用对等互连路径访问您将不会跨有状态的实体，例如 NAT 或防火墙。 因此，通过 ExpressRoute 专用对等互连的非对称路由不一定是阻止流量流。
 
但是，如果在异地冗余的平行路径，而不考虑是否有有状态的实体，或不是，负载均衡流量会遇到不一致的网络性能。 在本文中，让我们讨论如何解决这些难题。

## <a name="small-to-medium-on-premises-network-considerations"></a>小型到中型本地网络注意事项

让我们考虑下图中所示的示例网络。 在示例中，Contoso 的本地位置和 Contoso 的 Azure 区域中的 VNet 之间建立异地冗余的 ExpressRoute 连接。 在图中，绿色实线指示 （通过 ExpressRoute 1) 的首选的路径，以点分隔的一个表示备用的路径 （通过 ExpressRoute 2)。

[![1]][1]

在设计用于灾难恢复的 ExpressRoute 连接时，您需要考虑：

- 使用异地冗余的 ExpressRoute 线路
- 使用不同的 ExpressRoute 线路的不同服务提供程序网络
- 设计每个 ExpressRoute 线路的[高可用性][HA]
- 终止客户网络上的不同位置中的不同 ExpressRoute 线路

默认情况下，如果您播发的路由相同转移所有 ExpressRoute 路径，Azure 就会本地绑定的负载平衡流量跨使用相等成本多路径 (ECMP) 路由的所有 ExpressRoute 路径。

但是，使用异地冗余的 ExpressRoute 线路我们需要考虑到使用不同的网络路径 （尤其是对于网络延迟） 中不考虑不同的网络性能。 若要获取更一致的网络性能，在正常操作期间，你可能想要首选提供最低的延迟的 ExpressRoute 线路。

您可以影响 Azure 以首选一条 ExpressRoute 线路，而另一个使用 （按有效性顺序排列） 的以下技术之一：

- 通过首选相比其他 ExpressRoute 线路的 ExpressRoute 线路播发更具体的路由
- 链接到首选的 ExpressRoute 线路的虚拟网络的连接上配置更高版本连接权重
- 通过使用较长的 AS 路径 （如路径前面添加） 不太首选 ExpressRoute 线路播发路由

### <a name="more-specific-route"></a>更具体的路由

下图说明了产生影响的 ExpressRoute 路径选择使用更具体的路由播发。 在所示的示例中，Contoso 本地/24 IP 范围播发作为通过首选路径 (ExpressRoute 1) 的两个/25 地址范围以及通过备用的路径 (ExpressRoute 2) / 24。

[![2]][2]

由于/25 是更具体，相比/24，Azure 将流量发送往 10.1.11.0/24 通过 ExpressRoute 1 处于正常状态。 如果这两个连接的 ExpressRoute 1 出现故障，则通过然后 VNet 会看到仅通过 ExpressRoute 2; 10.1.11.0/24 路由播发并因此在这种失败状态中使用备用的线路。

### <a name="connection-weight"></a>连接权重

以下屏幕截图演示了配置 ExpressRoute 连接通过 Azure 门户的权重。

[![3]][3]

下图演示了使用连接权重产生影响 ExpressRoute 路径选择。 默认连接权重为 0。 在下面的示例中，ExpressRoute 1 的连接的权重配置为 100。 当 VNet 收到多条 ExpressRoute 线路通过播发的路由前缀时，则 VNet 将首选具有最高权重的连接。

[![4]][4]

如果这两个连接的 ExpressRoute 1 出现故障，则通过然后 VNet 会看到仅通过 ExpressRoute 2; 10.1.11.0/24 路由播发并因此在这种失败状态中使用备用的线路。

### <a name="as-path-prepend"></a>作为路径前面预置

下图演示了使用如路径前面添加的产生影响 ExpressRoute 路径选择。 在关系图中，路由公布通过 ExpressRoute 1 指示 eBGP 的默认行为。 通过 ExpressRoute 2 路由播发，在本地网络的 ASN 被伪装此外上的路由路径。 通过多个 ExpressRoute 线路，每个 eBGP 路由选择进程，接收相同的路由时 VNet 希望使用最短路径的路由。 

[![5]][5]

如果这两个连接的 ExpressRoute 1 出现故障，则 VNet 会看到仅通过 ExpressRoute 2 10.1.11.0/24 路由播发。 因此，路径将会变得不相关的时间。 因此，会在此故障状态使用备用的线路。

使用任何技术，如果您影响 Azure 以首选 ExpressRoute 之一，而其他人，还需要确保在本地网络的 Azure 绑定流量以避免非对称流也会喜欢 ExpressRoute 路径相同。 通常情况下，本地首选项值用于影响的本地网络以通过其他人喜欢一条 ExpressRoute 线路。 本地首选项是内部 BGP (iBGP) 指标。 最高的本地首选项值的 BGP 路由是首选。

> [!IMPORTANT]
> 作为备用的使用特定 ExpressRoute 线路时，需要主动管理它们，并定期测试故障转移操作。 
> 

## <a name="large-distributed-enterprise-network"></a>大型分布式的企业网络

大型分布式的企业网络后，您可能有多条 ExpressRoute 线路。 在本部分中，让我们了解如何设计灾难恢复使用主动-主动 ExpressRoute 线路，而无需其他备用的线路。 

让我们考虑下图中所示的示例。 在示例中，Contoso 具有两个本地位置连接到两个 Contoso IaaS 部署中通过两个不同的对等互连位置中的 ExpressRoute 线路的两个不同 Azure 区域。 

[![6]][6]

我们构建灾难恢复的方式会影响如何跨区域相交位置 (region1/region2 到位置 2/位置 1) 将流量路由。 让我们考虑跨区域位置会将流量路由以不同的方式的两个不同的灾难体系结构。

### <a name="scenario-1"></a>方案 1

在第一个方案中，让我们来设计灾难恢复，以便 Azure 区域和本地网络之间的所有流量都流经本地 ExpressRoute 线路处于稳定状态。 如果本地 ExpressRoute 线路失败时，然后远程 ExpressRoute 线路用于 Azure 之间的所有流量流和本地网络。

下图说明了方案 1。 在图中，绿线表示 VNet1 和本地网络之间的流量流的路径。 蓝线指示 VNet2 和本地网络之间的流量流的路径。 实线表示在稳定状态所需的路径和虚线指示相应携带稳定状态流量流的 ExpressRoute 线路的失败的通信路径。 

[![7]][7]

您可以构建使用连接权重来影响 Vnet，以更喜欢与本地对等互连位置 ExpressRoute 的本地网络连接绑定流量的方案。 若要完成该解决方案，您需要确保对称的反向流量流。 可用于本地首选项 iBGP 会话 （在其的 ExpressRoute 线路终止的本地端） 在 BGP 路由器之间首选 ExpressRoute 线路。 下图说明了解决方案。 

[![8]][8]

### <a name="scenario-2"></a>方案 2

下图说明了第二种情况。 在图中，绿线表示 VNet1 和本地网络之间的流量流的路径。 蓝线指示 VNet2 和本地网络之间的流量流的路径。 在稳定状态 （实线关系图中），所有 Vnet 和本地位置之间的流量流通过 Microsoft 主干网大多数情况下，以及通过之间的相互流在本地位置仅处于故障状态 （点线中关系图中） 的 ExpressRoute。

[![9]][9]

下图说明了解决方案。 如所示，您可以构建方案或者使用更具体的路由 (选项 1) 或 AS 路径预置 (选项 2) 来影响 VNet 路径所选内容。 若要影响的 Azure 站流量的本地网络路由选择，需要如小于首选配置的本地位置之间的相互关系。 将相互连接的链接配置为首选的勋爵取决于在本地网络中使用的路由协议。 与 iBGP 或使用 IGP （OSPF 或 IS IS） 的指标，可以使用本地首选项。

[![10]][10]


## <a name="next-steps"></a>后续步骤

在本文中，我们讨论了如何设计用于灾难恢复的 ExpressRoute 线路的专用对等互连连接。 以下文章地址灾难恢复从应用程序和前端访问透视：

- [企业级灾难恢复][Enterprise DR]
- [使用 Azure Site Recovery 进行 SMB 灾难恢复][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "小型到中等大小的本地网络注意事项"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "影响使用更具体的路由路径选择"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "配置通过 Azure 门户的连接权重"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "影响使用连接权重的路径选择"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "影响使用作为路径的路径选择前面添加"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "大型分布式的本地网络的考虑事项"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "方案 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "主动-主动 ExpressRoute 线路的解决方案 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "主动-主动 ExpressRoute 线路的解决方案 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





