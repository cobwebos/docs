---
title: 将 ExpressRoute 与 Azure 虚拟机灾难恢复配置使用 | Microsoft Docs
description: 介绍如何结合使用 Azure ExpressRoute 与 Azure Site Recovery 来实现 Azure 虚拟机灾难恢复
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071581"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>将 ExpressRoute 与 Azure 虚拟机灾难恢复配置使用

使用 Microsoft Azure ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 本文介绍如何结合使用 ExpressRoute 与 Site Recovery 来实现 Azure 虚拟机的灾难恢复。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保了解：
-   ExpressRoute [线路](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [路由域](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Azure 虚拟机[复制体系结构](azure-to-azure-architecture.md)
-   为 Azure 虚拟机[设置复制](azure-to-azure-tutorial-enable-replication.md)
-   [故障转移](azure-to-azure-tutorial-failover-failback.md) Azure 虚拟机

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute 和 Azure 虚拟机复制

使用 Site Recovery 保护 Azure 虚拟机时，将会根据 Azure 虚拟机是否使用 [Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)，将复制数据发送到 Azure 存储帐户或目标 Azure 区域中的副本托管磁盘。 尽管复制终结点是公共终结点，但在默认情况下，无论源虚拟网络位于哪个 Azure 区域，Azure VM 复制的复制流量都不会遍历 Internet。

对于 Azure VM 灾难恢复，由于复制数据不会离开 Azure 边界，因此不需要使用 ExpressRoute 进行复制。 在将虚拟机故障转移到目标 Azure 区域后，可以使用[专用对等互连](../expressroute/expressroute-circuit-peerings.md#azure-private-peering)访问它们。

## <a name="replicating-azure-deployments"></a>复制 Azure 部署

前一篇[文章](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity)介绍了一个简单设置，其中的一个 Azure 虚拟网络通过 ExpressRoute 连接到客户的本地数据中心。 典型的企业部署在多个 Azure 虚拟网络之间拆分工作负荷，中央连接中心与 Internet 和本地部署建立外部连接。

本示例介绍企业部署中常见的中心和分支拓扑：
-   部署位于“Azure 东亚”区域，本地数据中心通过香港的合作伙伴边缘建立了 ExpressRoute 线路连接。
-   应用程序部署在两个分支虚拟网络上 – 地址空间为 10.1.0.0/24 的“源 VNet1”，以及地址空间为 10.2.0.0/24 的和“源 VNet2”。
-   地址空间为 10.10.10.0/24 的中心虚拟网络“源中心 VNet”充当守护设备。 跨子网的所有通信通过中心进行。
-   中心虚拟网络包含两个子网 – 地址空间为 10.10.10.0/25 的“NVA 子网”，以及地址空间为 10.10.10.128/25 的“网关子网”。
-   “NVA 子网”包含 IP 地址为 10.10.10.10 的网络虚拟设备。
-   “网关子网”包含连接到 ExpressRoute 的 ExpressRoute 网关，该网关通过专用对等互连路由域将流量路由到客户本地数据中心。
-   每个分支虚拟网络连接到中心虚拟网络，此网络拓扑中的所有路由通过 Azure 路由表 (UDR) 进行控制。 从一个 VNet 发往另一个 VNet 或本地数据中心的所有出站流量经过 NVA 路由。

![故障转移之前使用 ExpressRoute 建立本地到 Azure 的连接](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>中心和分支对等互连

分支到中心的对等互连采用以下配置：
-   允许虚拟网络地址：已启用
-   允许转发流量：已启用
-   允许网关传输：已禁用
-   使用删除网关：已启用

 ![分支到中心对等互连配置](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

中心到分支的对等互连采用以下配置：
-   允许虚拟网络地址：已启用
-   允许转发流量：已启用
-   允许网关传输：已启用
-   使用删除网关：已禁用

 ![中心到分支对等互连配置](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>为部署启用复制

对于上面的设置，首先使用 Site Recovery 为每个虚拟机[设置灾难恢复](azure-to-azure-tutorial-enable-replication.md)。 Site Recovery 可以在目标区域中创建副本虚拟网络（包括子网和网关子网），并在源与目标虚拟网络之间创建所需的映射。 还可以预先创建目标端网络和子网，并在启用复制时使用相同的网络和子网。

Site Recovery 不会复制路由表、虚拟网络网关、虚拟网络网关连接、虚拟网络对等互连，或者其他任何网络资源或连接。 [复制过程](azure-to-azure-architecture.md#replication-process)不包括的这些资源和其他资源，需要在连接到相关资源的情况下，在故障转移期间或之前创建。 可以结合自动化脚本，使用 Azure Site Recovery 的强大[恢复计划](site-recovery-create-recovery-plans.md)来自动创建和连接其他资源。

默认情况下，复制流量不会离开 Azure 边界。 通常，NVA 部署还会定义默认路由 (0.0.0.0/0)，用于强制出站 Internet 流量流经 NVA。 在这种情况下，如果所有复制流量通过 NVA，设备可能会受到限制。 使用默认路由将所有 Azure VM 流量路由到本地部署时，也存在这种情况。 我们建议在虚拟网络中为“存储”[创建一个网络服务终结点](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，这样复制流量就不会离开 Azure 边界。

## <a name="failover-models-with-expressroute"></a>使用 ExpressRoute 的故障转移模型

将 Azure 虚拟机故障转移到其他区域后，与源虚拟网络建立的现有 ExpressRoute 连接不会自动转换为恢复区域中的目标虚拟网络。 新连接需要将 ExpressRoute 连接到目标虚拟网络。

可根据[此处](azure-to-azure-support-matrix.md#region-support)的详述，将 Azure 虚拟机复制到同一地理群集中的任何 Azure 区域。 如果选择的目标 Azure 区域与源不在同一地缘政治区域，在为源和目标区域连接使用单个 ExpressRoute 线路的情况下，需要启用 ExpressRoute 高级版。 有关更多详细信息，请查看 [ExpressRoute 位置](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)和 [ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute/)。

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>两个不同 ExpressRoute 对等互连位置的两条 ExpressRoute 线路
-   若要应对 ExpressRoute 主要线路故障和大规模的区域灾难（这也可能会影响 ExpressRoute 对等互连位置，并中断 ExpressRoute 主要线路），此配置会很有用。
-   通常，连接到生产环境的线路用作主要线路，次要线路采用防故障设计，通常带宽较低。 发生灾难时，如果次要线程必须接管主要线程，可以提高次要线路的带宽。
-   使用这种配置，可以在故障转移后建立从 ExpressRoute 次要线路到目标虚拟网络的连接，或者建立可用于灾难声明的连接，以缩短总恢复时间。 与主要和目标区域虚拟网络同时建立连接后，请确保本地路由仅在故障转移后使用次要线路和连接。
-   可以根据要求，在故障转移时为使用 Site Recovery 保护的 VM 的源和目标虚拟网络配置相同或不同的 IP 地址。 在这两种情况下，都可在故障转移之前建立次要连接。

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>同一 ExpressRoute 对等互连位置的两条 ExpressRoute 线路
-   使用此配置可以应对 ExpressRoute 主要线路故障，但无法应对可能影响 ExpressRoute 对等互连位置的大规模区域灾难。 如果使用后一种配置，主要和次要线路可能会受到影响。
-   IP 地址和连接的其他条件与前一案例相同。 可以使用主要线程从本地数据中心同时连接到源虚拟网络，并使用次要线路同时连接到目标虚拟网络。 与主要和目标区域虚拟网络同时建立连接后，请确保本地路由仅在故障转移后使用次要线路和连接。
-   如果在同一对等位置创建了线路，则无法将这两条线路连接到同一虚拟网络。

### <a name="single-expressroute-circuit"></a>一条 ExpressRoute 线路
-   此配置无法应对可能影响 ExpressRoute 对等互连位置的大规模区域灾难。
-   使用一个 ExpressRoute 线路时，如果在目标区域中使用相同的 IP 地址空间，则无法同时将源和目标虚拟网络连接到线路。
-   在目标区域中使用相同的 IP 地址空间时，应断开源端连接，然后再建立目标端连接。 可在恢复计划中编写此连接变更的脚本。
-   在发生区域故障时，如果主要区域不可访问，则断开连接操作可能失败。 在目标虚拟网络上使用相同的 IP 地址空间时，这种服务中断可能会影响与目标区域建立连接。
-   如果在目标上成功建立连接，并且主要区域稍后已恢复，在同时连接尝试连接到同一地址空间的情况下，可能会出现丢包的情况。 为了防止丢包，应立即终止主要连接。 将虚拟机故障回复到主要区域后，可以在断开次要连接后再次建立主要连接。
-   如果在目标虚拟网络上使用不同的地址空间，则可以从同一 ExpressRoute 线路同时连接到源和目标虚拟网络。

## <a name="recovering-azure-deployments"></a>恢复 Azure 部署
假设故障转移模型在两个不同的对等互连位置部署了两条不同的 ExpressRoute 线路，并为受保护的 Azure 虚拟机保留了专用 IP 地址。 目标恢复区域是“Azure 东南亚”，ExpressRoute 次要线路连接是通过新加坡的合作伙伴边缘建立的。

若要自动恢复整个部署，除了复制虚拟机和虚拟网络以外，还必须创建其他相关的网络资源和连接。 对于前面所述的中心和分支网络拓扑，在执行[故障转移](azure-to-azure-tutorial-failover-failback.md)操作期间和之后，需要采取以下附加步骤：
1.  在目标区域中心虚拟网络内创建 Azure ExpressRoute 网关。 需使用 ExpressRoute 网关将目标中心虚拟网络连接到 ExpressRoute 线路。
2.  创建从目标中心虚拟网络到目标 ExpressRoute 线路的虚拟网络连接。
3.  在目标区域的中心与分支虚拟网络之间设置 VNet 对等互连。 目标区域中的对等互连属性与源区域中的属性相同。
4.  在中心 VNet 和两个分支 VNet 中设置 UDR。 使用相同的 IP 地址时，目标端 UDR 的属性与源端中的属性相同。 使用不同的目标 IP 地址时，应相应地修改 UDR。

可在[恢复计划](site-recovery-create-recovery-plans.md)中编写上述步骤的脚本。 根据应用程序连接和恢复时间要求，也可以在开始故障转移之前完成上述步骤。

恢复虚拟机并完成其他连接步骤后，恢复环境如下所示：![故障转移后使用 ExpressRoute 建立本地到 Azure 的连接](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

[此文](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity)详细介绍了在目标虚拟机上使用相同 IP，通过一条 ExpressRoute 线路对 Azure VM 进行灾难恢复的简单拓扑示例。

## <a name="recovery-time-objective-rto-considerations"></a>恢复时间目标 (RTO) 注意事项
若要缩短部署的总恢复时间，我们建议事先预配并部署更多的目标区域[网络组件](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment)，例如虚拟网络网关。 部署更多的资源往往可以减少停机时间，如果在规划期间不考虑此因素，这种停机可能会影响总恢复时间。

我们建议定期针对受保护的部署运行[灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)。 演练可以在不丢失数据或不停机的情况下验证复制策略，并且不会影响生产环境。 运行演练还能避免可能对恢复时间目标造成负面影响的最后一分钟配置问题。 建议使用单独的 Azure VM 网络进行测试故障转移，而不要使用启用复制时设置的默认网络。

如果使用一条 ExpressRoute 线路，我们建议对目标虚拟网络使用不同的 IP 地址空间，以免发生区域灾难期间建立连接出现问题。 如果无法对恢复的生产环境使用不同的 IP 地址，则应在使用不同 IP 地址的单独测试网络中执行灾难恢复演练测试故障转移，因为无法将两个使用重叠 IP 地址空间的虚拟网络连接到同一 ExpressRoute 线路。

## <a name="next-steps"></a>后续步骤
- 详细了解 [ExpressRoute 线路](../expressroute/expressroute-circuit-peerings.md)。
- 详细了解 [ExpressRoute 路由域](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)。
- 详细了解 [ExpressRoute 位置](../expressroute/expressroute-locations.md)。
- 详细了解如何使用[恢复计划](site-recovery-create-recovery-plans.md)自动执行应用程序故障转移。
