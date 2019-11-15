---
title: 在 Azure VM 故障转移后保留 IP 地址 Azure Site Recovery
description: 介绍如何在使用 Azure Site Recovery 将用于灾难恢复的 Azure VM 故障转移到次要区域时保留 IP 地址
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083681"
---
# <a name="retain-ip-addresses-during-failover"></a>在故障转移期间保留 IP 地址

[Azure Site Recovery](site-recovery-overview.md) 支持针对 Azure VM 进行灾难恢复，方法是：将 VM 复制到其他 Azure 区域，在发生故障时进行故障转移，并在一切恢复正常后故障恢复到主要区域。

在故障转移期间，可能想要将 IP 地址保留在与源区域相同的目标区域中：

- 默认情况下，为 Azure VM 启动灾难恢复时，Site Recovery 将根据源资源设置创建目标资源。 对于配置有静态 IP 地址的 Azure VM，Site Recovery 将尝试对目标 VM 预配相同的 IP 地址（如果未占用）。 有关 Site Recovery 如何处理寻址的完整介绍，[请参阅本文](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。
- 对于简单的应用程序，默认配置就足够了。 对于较复杂的应用，可能需要预配其他资源，以连接在确保故障转移后按预期方式工作。


本文提供在较复杂的示例方案中保留 IP 地址的一些示例。 示例包括：

- 针对在 Azure 中运行所有资源的公司的故障转移
- 针对使用混合部署并同时在本地和 Azure 中运行资源的公司的故障转移

## <a name="resources-in-azure-full-failover"></a>Azure 中的资源：完全故障转移

公司 A 在 Azure 中运行其所有应用。

### <a name="before-failover"></a>在故障转移之前

下方是故障转移之前的体系结构。

- 公司 A 在源和目标 Azure 区域中具有相同的网络和子网。
- 为了缩短恢复时间目标（RTO），公司对 SQL Server Always On、域控制器等使用副本节点。这些副本节点在目标区域中的另一个 VNet 中，因此它们可以在源区域和目标区域之间建立 VPN 站点到站点连接。 如果源和目标中使用相同的 IP 地址，则无法建立连接。  
- 故障转移前，网络体系结构如下所示：
    - 主要区域是“Azure 东亚”
        - 东亚包含的一个 VNet（源 VNet）的地址空间为 10.1.0.0/16。
        - 东亚的工作负荷拆分在 VNet 中的三个子网中：
            - **子网 1**：10.1.1.0/24
            - **子网 2**： 10.1.2.0/24
            - **子网 3**：10.1.3.0/24
    - 次要（目标）区域是“Azure 东南亚”
        - 东南亚具有与“源 VNet”相同的恢复 VNet（名为“恢复 VNet”）。
        - 东南亚具有地址空间为 10.2.0.0/16 的额外 VNet (Azure VNet)。
        - Azure VNet 包含地址空间为 10.2.4.0/24 的子网（子网 4）。
        - SQL Server Always On、域控制器等的副本节点位于子网 4 中。
    - 源 VNet 和 Azure VNet 通过 VPN 站点到站点连接建立连接。
    - **恢复 VNet** 未与其他任何虚拟网络相连接。
    - 公司 A 分配/验证已复制项的目标 IP 地址。 每个 VM 的目标 IP 均与源 IP 相同。

![完全故障转移前 Azure 中的资源](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>在故障转移之后

如果源区域发生故障，公司 A 可将其所有资源故障转移到目标区域。

- 如果目标 IP 地址在故障转移前已就位，公司 A 可安排故障转移以及故障转移后自动在恢复 VNet 和 Azure VNet 之间建立连接。 下图对此做了演示.
- 根据应用的要求，可以在故障转移之前、期间（作为中间步骤）或之后，在目标区域中的两个 VNet（恢复 VNet 和 Azure VNet）之间建立连接。
  - 该公司可以使用[恢复计划](site-recovery-create-recovery-plans.md)来指定何时建立连接。
  - 他们可以使用 VNet 对等互连或站点到站点 VPN 来在 VNet 之间进行连接。
      - VNet 对等互连不使用 VPN 网关，并且具有不同的约束。
      - VNet 对等互连[定价](https://azure.microsoft.com/pricing/details/virtual-network)的计算不同于 VNet 到 VNet VPN 网关[定价](https://azure.microsoft.com/pricing/details/vpn-gateway)的计算。 对于故障转移，我们通常建议使用与源网络相同的连接方法（包括连接类型），以最大程度减少不可预测的网络事件。

    ![Azure 完全故障转移中的资源](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Azure 中的资源：独立应用故障转移

可能需要在应用级别进行故障转移。 例如，对位于专用子网中的特定应用或特定应用层进行故障转移。

- 在此方案中，尽管可以保留 IP 地址，但通常不建议这样做，因为这将增加连接不一致的可能性。 此外，还将与同一 Azure VNet 中的其他子网断开子网连接。
- 对子网级别应用执行故障转移的更好方法是：使用不同的目标 IP 地址进行故障转移（如果需要与源 VNet 上的其他子网建立连接），或者在源区域中将每个应用隔离在其自己的专用 VNet 中。 使用后一种方法可以在源区域中的网络之间建立连接，并且在故障转移到目标区域时可以模拟相同的行为。  

在此示例中，公司 A 将源区域中的应用放置在专用 VNet 中，并在这些 VNet 之间建立连接。 借助此设计，他们可以执行独立应用故障转移，并在目标网络中保留源专用 IP 地址。

### <a name="before-failover"></a>在故障转移之前

故障转移前，体系结构如下所示：

- 应用程序 VM 托管在主要的 Azure 东亚区域中：
    - App1 VM 位于 VNet“源 VNet1”10.1.0.0/16 中。
    - App2 VM 位于 VNet “源 VNet 2”10.2.0.0/16 中。
    - 源 VNet 1 包含两个子网。
    - 源 VNet 2 包含两个子网。
- 次要（目标）区域为“Azure 东南亚”- 东南亚具有与源 VNet 1 和源 VNet 2 相同的恢复 VNet（恢复 VNet 1 和恢复 VNet 2）。
        - 恢复 VNet 1 和恢复 VNet 2 各自具有 2 个与源 VNet 1 和源 VNet 2 中的子网匹配的子网 - 东南亚具有地址空间为 10.3.0.0/16 的额外 VNet (Azure VNet)。
        - Azure VNet 包含地址空间为 10.3.4.0/24 的子网（子网 4）。
        -SQL Server Always On，域控制器等的副本节点位于**子网 4**中。
- 存在大量站点到站点 VPN 连接： 
    - 源 VNet 1 和 Azure VNet
    - 源 VNet 2 和 Azure VNet
    - 源 VNet 1 和源 VNet 2 通过站点到站点 VPN 进行连接
- 恢复 VNet 1 和恢复 VNet 2 不会连接到其他任何 VNet。
- 公司 A 对恢复 VNet 1 和恢复 VNet 2 配置 VPN 网关以减少 RTO。  
- **恢复 VNet1** 和**恢复 VNet2** 未与其他任何虚拟网络相连接。
- 为了降低恢复时间目标 (RTO)，在故障转移之前，可在**恢复 VNet1** 和**恢复 VNet2** 上配置 VPN 网关。

    ![应用故障转移前 Azure 中的资源](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>在故障转移之后

如果发生影响单个应用（本示例中的源 VNet 2）的故障或问题，公司 A 可按如下所示恢复受影响的应用：


- 断开源 VNet1 与源 VNet2，以及源 VNet2 与 Azure VNet 之间的 VPN 连接。
- 在源 VNet1 与恢复 VNet2，以及恢复 VNet2 与 Azure VNet 之间建立 VPN 连接。
- 将源 VNet2 中的 VM 故障转移到恢复 VNet2。

![Azure 应用故障转移中的资源](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- 可以延伸此示例，以包括更多的应用程序和网络连接。 从源故障转移到目标时，我们建议尽可能地遵循相似性连接模式。
- VPN 网关利用公共 IP 地址和网关跃点建立连接。 如果不想使用公共 IP 地址，或希望避免额外的跃点，可以使用 [Azure VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)在[受支持的 Azure 区域](../virtual-network/virtual-network-manage-peering.md#cross-region)之间将虚拟网络对等互连。

## <a name="hybrid-resources-full-failover"></a>混合资源：完全故障转移

在此方案中，公司 B 实行混合部署，其在 Azure 中运行一部分应用程序基础结构，并在本地运行剩余的基础结构。 

### <a name="before-failover"></a>在故障转移之前

下面是故障转移之前网络体系结构的外观。

- 应用程序 VM 托管在 Azure 东亚。
- 东亚包含的一个 VNet（源 VNet）的地址空间为 10.1.0.0/16。
  - 东亚的工作负荷拆分在源 VNet 中中的三个子网中：
    - **子网 1**：10.1.1.0/24
    - **子网 2**： 10.1.2.0/24
    - **子网 3**： 10.1.3.0/24，利用地址空间为 10.1.0.0/16 的 Azure 虚拟网络。 此虚拟网络名为“源 VNet”
      - 次要（目标）区域是“Azure 东南亚”：
  - 东南亚具有与“源 VNet”相同的恢复 VNet（名为“恢复 VNet”）。
- 东亚中的 Vm 使用 Azure ExpressRoute 或站点到站点 VPN 连接到本地数据中心。
- 为了降低 RTO，公司 B 在故障转移之前，在 Azure 东南亚的恢复 VNet 中预配了网关。
- 公司 B 分配/验证已复制 VM 的目标 IP 地址。 每个 VM 的目标 IP 地址均与源 IP 地址相同。


![故障转移之前的本地到 Azure 连接](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>在故障转移之后


如果源区域发生故障，公司 B 可将其所有资源故障转移到目标区域。

- 如果目标 IP 地址在故障转移前已就位，公司 B 可安排故障转移以及故障转移后自动在**恢复 VNet** 和 **Azure VNet** 之间建立连接。
- 根据应用的要求，可以在故障转移之前、期间（作为中间步骤）或之后，在目标区域中的两个 VNet（恢复 VNet 和 Azure VNet）之间建立连接。 该公司可以使用[恢复计划](site-recovery-create-recovery-plans.md)来指定何时建立连接。
- 在 Azure 东南亚与本地数据中心之间之间建立连接之前，应断开 Azure 东亚与本地数据中心之间的原始连接。
- 本地路由将重新配置为在故障转移之后指向目标区域和网关。

![故障转移之后的本地到 Azure 连接](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>混合资源：独立应用故障转移

公司 B 不能在子网级别对独立应用进行故障转移。 这是因为，源和恢复 VNet 中的地址空间是相同的，并且源到本地的原始连接处于活动状态。

 - 为了复原应用，公司 B 需要将每个应用放置于其自己的专用 Azure VNet 中。
 - 每个应用都位于单独的 VNet 中后，公司 B 可故障转移独立应用，并将源连接路由到目标区域。

## <a name="next-steps"></a>后续步骤

了解[恢复计划](site-recovery-create-recovery-plans.md)。
