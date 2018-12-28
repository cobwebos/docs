---
title: 使用 Azure Site Recovery 服务将 Azure ExpressRoute 与 Azure VM 的灾难恢复相集成 | Microsoft Docs
description: 介绍如何使用 Azure Site Recovery 和 Azure ExpressRoute 来设置 Azure VM 的灾难恢复
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 5a16b81abb9cc95f46bd61f6c0232a28f3cda0ff
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875393"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>将 Azure ExpressRoute 与 Azure VM 的灾难恢复相集成


本文介绍在将 Azure VM 的灾难恢复设置为次要 Azure 区域时，如何将 Azure ExpressRoute 与 [Azure Site Recovery](site-recovery-overview.md) 相集成。

Site Recovery 通过将 Azure VM 数据复制到 Azure 来实现 Azure VM 的灾难恢复。

- 如果 Azure VM 使用 [Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)，则 VM 数据将复制到次要区域中复制的托管磁盘。
- 如果 Azure VM 没有使用托管磁盘，则 VM 数据将复制到 Azure 存储帐户。
- 复制终结点是公共终结点，但 Azure VM 的复制流量不会通过 Internet。

使用 ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft Azure 云。 如果配置了 ExpressRoute，则它与 Site Recovery 的集成如下所示：

- **在 Azure 区域之间进行复制期间**：Azure VM 灾难恢复的复制流量仅存在于 Azure 中，无需 ExpressRoute 或不使用 ExpressRoute 进行复制。 但是，如果从本地站点连接到主要 Azure 站点中的 Azure VM，则在为这些 Azure VM 设置灾难恢复时，需要注意许多问题。
- **Azure 区域之间的故障转移**：在服务发生中断时，可将 Azure VM 从主要 Azure 区域故障转移到次要 Azure 区域。 在故障转移到次要区域后，要使用 ExpressRoute 访问次要区域中的 Azure VM，需执行多个步骤。


## <a name="before-you-begin"></a>开始之前

在开始之前，请确保了解以下概念：

- ExpressRoute [线路](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute [路由域](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [位置](../expressroute/expressroute-locations.md)。
- Azure VM [复制体系结构](azure-to-azure-architecture.md)
- 如何为 Azure VM [设置复制](azure-to-azure-tutorial-enable-replication.md)。
- 如何[故障转移](azure-to-azure-tutorial-failover-failback.md) Azure VM。


## <a name="general-recommendations"></a>一般建议

为采取最佳做法，并确保有效的灾难恢复恢复时间目标 (RTO)，建议在设置 Site Recovery 以与 ExpressRoute 集成时执行以下操作：

- 在故障转移到次要区域前预配网络组件：
    - 为 Azure VM 启用复制时，Site Recovery 可以根据源网络设置自动部署目标 Azure 区域中的网络资源，如网络、子网和网关。
    - Site Recovery 无法自动设置网络资源，如 VNet 网关。
    - 建议在故障转移前预配这些额外的网络资源。 此部署会导致较短的停机时间，如果在部署规划期间未考虑此停机时间，则它可能会影响整体恢复时间。
- 定期进行灾难恢复演练：
    - 演练如何在不丢失数据或不停机的情况下验证复制策略，并且不影响生产环境。 它有助于避免可能对 RTO 产生负面影响的最新配置问题。
    - 在演练测试故障转移时，建议使用单独的 Azure VM 网络，而不是启用复制时设置的默认网络。
- 如果具有单一 ExpressRoute 线路，请使用不同的 IP 地址空间。
    - 建议为目标虚拟网络使用不同的 IP 地址空间。 这可避免在区域中断期间建立连接时出现的问题。
    - 如果无法使用单独的地址空间，请确保在具有不同 IP 地址的独立测试网络上运行灾难恢复演练测试故障转移。 无法将具有重叠 IP 地址空间的两个 VNet 连接到同一 ExpressRoute 线路。

## <a name="replicate-azure-vms-when-using-expressroute"></a>使用 ExpressRoute 时复制 Azure VM


如果要在主站点中为 Azure VM 设置复制，并且要通过 ExpressRoute 从本地站点连接到这些 VM，则需要执行以下操作：

1. 为每个 Azure VM [启用复制](azure-to-azure-tutorial-enable-replication.md)。
2. （可选）让 Site Recovery 设置网络：
    - 配置和启用复制时，Site Recovery 会在目标 Azure 区域中设置网络、子网和网关子网，以匹配源区域中的网络、子网和网关子网。 此外，Site Recovery 还会在源虚拟网络和目标虚拟网络之间进行映射。
    - 如果不希望 Site Recovery 自动执行此操作，请在启用复制前创建目标端网络资源。
3. 创建其他网络元素：
    - Site Recovery 不会在次要区域中创建路由表、VNet 网关、VNet 网关连接、VNet 对等互连或其他网络资源和连接。
    - 在从主要区域运行故障转移之前，需要在次要区域中创建这些其他网络元素。
    - 可以使用[恢复计划](site-recovery-create-recovery-plans.md)和自动化脚本来设置和连接这些网络资源。
1. 如果为控制网络流量而部署了网络虚拟设备 (NVA)，请注意：
    - Azure 的 Azure VM 复制的默认系统路由为 0.0.0.0/0。
    - 通常，NVA 部署还会定义默认路由 (0.0.0.0/0)，用于强制出站 Internet 流量流经 NVA。 如果找不到其他特定路由配置，则将使用默认路由。
    - 在这种情况下，如果所有复制流量都通过 NVA，则 NVA 可能会过载。
    - 使用默认路由将所有 Azure VM 流量路由到本地部署时，也存在这种限制。
    - 在本方案中，建议在虚拟网络中为 Microsoft.Storage 服务[创建网络服务终结点](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，以便复制流量不会离开 Azure 边界。

## <a name="replication-example"></a>复制示例

通常，企业部署会在多个 Azure VNet 之间拆分工作负荷，其中央连接中心用于与 Internet 和本地站点建立外部连接。 中心和分支拓扑通常与 ExpressRoute 一起使用。

![故障转移之前使用 ExpressRoute 建立本地到 Azure 的连接](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **区域**。 应用部署在“Azure 东亚”区域。
- **分支 VNet**。 应用部署在两个分支 vNet 中：
    - **源 vNet1**：10.1.0.0/24。
    - **源 vNet2**：10.2.0.0/24。
    - 每个分支虚拟网络都连接到“中心 vNet”。
- **中心 vNet**。 **中心 vNet“源中心 vNet”：** 10.10.10.0/24。
    - 此中心 vNet 充当网关守卫。
    - 跨子网的所有通信都通过此中心进行。
 - ****中心 vNet 子网**。 中心 vNet 具有两个子网：
     - **NVA 子网**：10.10.10.0/25。 此子网包含 NVA (10.10.10.10)。
     - **网关子网**：10.10.10.128/25。 此子网包含连接到 ExpressRoute 连接的 ExpressRoute 网关，该连接通过专用对等互连路由域路由到本地站点。
- 本地数据中心通过香港的合作伙伴边缘建立了 ExpressRoute 线路连接。
- 所有路由都通过 Azure 路由表 (UDR) 进行控制。
- vNet 之间或流向本地数据中心的所有出站流量都经过 NVA 路由。

### <a name="hub-and-spoke-peering-settings"></a>中心和分支对等互连设置

#### <a name="spoke-to-hub"></a>分支到中心

**Direction** | **设置** | **State**
--- | --- | ---
分支到中心 | 允许虚拟网络地址 | 已启用
分支到中心 | 允许转发流量 | 已启用
分支到中心 | 允许网关传输 | 已禁用
分支到中心 | 使用删除网关 | 已启用

 ![分支到中心对等互连配置](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>中心到分支

**Direction** | **设置** | **State**
--- | --- | ---
中心到分支 | 允许虚拟网络地址 | 已启用
中心到分支 | 允许转发流量 | 已启用
中心到分支 | 允许网关传输 | 已启用
中心到分支 | 使用删除网关 | 已禁用

 ![中心到分支对等互连配置](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>示例步骤

在本示例中，当在源网络中为 Azure VM 启用复制时，将发生以下情况：

1. 为 VM [启用复制](azure-to-azure-tutorial-enable-replication.md)。
2. Site Recovery 将在目标区域中创建副本 vNet、子网和网关子网。
3. Site Recovery 将在源网络和它创建的副本目标网络之间创建映射。
4. 手动创建虚拟网络网关、虚拟网络网关连接、虚拟网络对等互连，或者其他任何网络资源或连接。


## <a name="fail-over-azure-vms-when-using-expressroute"></a>在使用 ExpressRoute 时对 Azure VM 进行故障转移

在使用 Site Recovery 将 Azure VM 故障转移到目标 Azure 区域后，可以使用 ExpressRoute [专用对等互连](../expressroute/expressroute-circuit-peerings.md#privatepeering)对其进行访问。

- 需要使用新连接将 ExpressRoute 连接到目标 vNet。 现有的 ExpressRoute 连接不会自动传输。
- 将 ExpressRoute 连接设置为目标 vNet 的方式取决于 ExpressRoute 拓扑。


### <a name="access-with-two-circuits"></a>使用两条线路进行访问

#### <a name="two-circuits-with-two-peering-locations"></a>具有两个对等互连位置的两条线路

此配置有助于保护 ExpressRoute 线路免遭区域性灾难。 如果主要对等互连位置发生故障，可以从其他位置继续连接。

- 连接到生产环境的线路通常为主要线路。 次要线路的带宽通常较低，如果发生灾难，可以增加此带宽。
- 故障转移后，可以建立从次要 ExpressRoute 线路到目标 vNet 的连接。 或者，可以在发生灾难时设置并准备好连接，以减少总体恢复时间。
- 在同时连接到主要和目标 vNet 时，请确保本地路由仅在故障转移后才使用次要线路和连接。
- 故障转移后，源和目标 vNet 可以接收新的 IP 地址，或保留相同的 IP 地址。 在这两种情况下，都可在故障转移之前建立次要连接。


#### <a name="two-circuits-with-single-peering-location"></a>具有单个对等互连位置的两条线路

此配置有助于防止主要 ExpressRoute 线路出现故障，但如果单个 ExpressRoute 对等互连位置发生故障，则会影响两条线路。

- 可以从本地数据中心同时使用主要线路连接到源 vNEt 和使用次要线路连接到目标 vNet。
- 在同时连接到主要和目标 vNet 时，请确保本地路由仅在故障转移后才使用次要线路和连接。
-   如果在同一对等互连位置创建了线路，则无法将这两条线路连接到同一 vNet。

### <a name="access-with-a-single-circuit"></a>使用单条线路进行访问

在此配置中，只有一条 Expressroute 线路。 虽然在某条线路出现故障的情况下，线路具有冗余连接，但如果对等互连区域发生故障，则单条路由线路将无法提供恢复能力。 请注意：

- 可以将 Azure VM 复制到[相同地理位置](azure-to-azure-support-matrix.md#region-support)中的任何 Azure 区域。 如果目标 Azure 区域与源不在同一位置，并且使用的是单条 ExpressRoute 线路，则需要启用 ExpressRoute 高级版。 请了解 [ExpressRoute 位置](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)和 [ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute/)。
- 如果在目标区域上使用了相同的 IP 地址空间，则无法将源和目标 vNet 同时连接到线路。 在本方案中：    
    -  断开源侧连接，然后建立目标侧连接。 可在 Site Recovery 恢复计划中编写此连接变更的脚本。 请注意：
        - 在发生区域故障时，如果主要区域不可访问，则断开连接操作可能失败。 这可能会影响到目标区域的连接创建。
        - 如果在目标区域中创建了连接，并且主要区域稍后已恢复，在同时连接尝试连接到同一地址空间的情况下，可能会出现丢包的情况。
        - 若要防止发生此情况，请立即终止主要连接。
        - 在 VM 故障回复到主要区域后，可以在断开次要连接后再次建立主要连接。
-   如果在目标 vNet 上使用不同的地址空间，则可以从同一 ExpressRoute 线路同时连接到源和目标 vNet。


## <a name="failover-example"></a>故障转移示例

在本示例中，我们使用以下拓扑：

- 两个不同对等互连位置中的两条不同 ExpressRoute 线路。
- 故障转移后保留 Azure VM 的专用 IP 地址。
- 目标恢复区域是“Azure 东南亚”。
- ExpressRoute 次要线路连接是通过新加坡的合作伙伴边缘建立的。

有关在故障转移后使用具有相同 IP 地址的单条 ExpressRoute 线路的简单拓扑，请[查看本文](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)。

### <a name="example-steps"></a>示例步骤
若要在本示例中自动执行恢复，需要执行以下操作：

1. 按照[设置复制](#azure-vm-replication-steps)的步骤执行操作。
2. 在故障转移期间或之后按照这些附加步骤[对 Azure VM 进行故障转移](azure-to-azure-tutorial-failover-failback.md)。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在目标区域中心 VNet 内创建 Azure ExpressRoute 网关。 这需要将目标中心 vNet 连接到 ExpressRoute 线路。

    b. 创建从目标中心 vNet 到目标 ExpressRoute 线路的连接。

    c. 在目标区域的中心与分支虚拟网络之间设置 VNet 对等互连。 目标区域中的对等互连属性与源区域中的属性相同。

    d. 在中心 VNet 和两个分支 VNet 中设置 UDR。

    - 使用相同的 IP 地址时，目标端 UDR 的属性与源端中的属性相同。
    - 使用不同的目标 IP 地址时，应相应地修改 UDR。


可在[恢复计划](site-recovery-create-recovery-plans.md)中编写上述步骤的脚本。 根据应用程序连接和恢复时间要求，也可以在开始故障转移之前完成上述步骤。

#### <a name="after-recovery"></a>灾难恢复后

恢复 VM 并完成连接后，恢复环境如下。

![故障转移之后使用 ExpressRoute 建立本地到 Azure 的连接](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>后续步骤

详细了解如何使用[恢复计划](site-recovery-create-recovery-plans.md)自动执行应用故障转移。
