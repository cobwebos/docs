---
title: "将 Azure 虚拟机故障转移到另一个 Azure 区域时保留 IP 地址 | Microsoft Docs"
description: "介绍在使用 Azure Site Recovery 进行 Azure 到 Azure 的故障转移方案时如何保留 IP 地址"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 15f87ba87d90cee765f52d3188796bc1ff7b8a35
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>为 Azure 虚拟机故障转移保留 IP 地址

Azure Site Recovery 支持对 Azure VM 进行灾难恢复。 在从一个 Azure 区域故障转移到另一个区域时，客户通常需要保留其 IP 配置。 默认情况下，在目标区域中创建源虚拟网络和子网的结构时，Site Recovery 可模拟这些资源。 对于配置了静态专用 IP 地址的 Azure VM，Site Recovery 还会尽量尝试在目标 VM 上预配相同的专用 IP（如果该 IP 尚未由 Azure 资源或复制的 VM 阻止）。

对于简单的应用程序，只需上面所述的默认配置。 对于较复杂的企业应用程序，客户可能需要预配其他网络资源才能确保在故障转移后能够与基础结构的其他组件保持连接。 本文介绍在不同区域之间故障转移 Azure VM，同时保留 VM IP 地址所要满足的网络要求。

## <a name="azure-to-azure-connectivity"></a>Azure 到 Azure 的连接

对于第一种方案，我们假设**公司 A** 的所有应用程序基础结构在 Azure 中运行。 出于业务连续性和法规遵从的原因，**公司 A** 决定使用 Azure Site Recovery 来保护其应用程序。

在满足 IP 保留要求（例如应用程序绑定）的情况下，公司 A 在目标区域中预配了相同的虚拟网络和子网结构。 为了进一步降低恢复时间目标 (RTO)，**公司 A** 为 SQL Always ON、域控制器等组件使用了副本节点，这些节点放置在目标区域中的不同虚拟网络内。 **公司 A** 为副本节点使用不同的地址空间，可在源与目标区域之间建立 VPN 站点到站点连接；如果在两端使用相同的地址空间，则无法做到这一点。

下面是故障转移之前网络体系结构的外观：
- 应用程序 VM 托管在 Azure 东亚，利用地址空间为 10.1.0.0/16 的 Azure 虚拟网络。 此虚拟网络名为**源 VNet**。
- 应用程序工作负荷在以下三个子网之间拆分 – 10.1.0.0/24、10.1.1.0/24 和 10.1.2.0/24，它们分别名为**子网 1**、**子网 2** 和**子网 3**。
- Azure 东南亚是目标区域，包含一个用于模仿源中地址空间和子网配置的恢复虚拟网络。 此虚拟网络名为**恢复 VNet**。
- 副本节点（例如 Always On、域控制器等组件所需的节点）放置在地址为 20.1.0.0/24 的子网 4 中地址空间为 20.1.0.0/16 的虚拟网络内。 此虚拟网络名为 **Azure VNet**，位于 Azure 东南亚。
- **源 VNet** 和 **Azure VNet** 通过 VPN 站点到站点连接建立连接。
- **恢复 VNet** 未与其他任何虚拟网络相连接。
- **公司 A** 分配/验证复制项的目标 IP 地址。 在此示例中，每个 VM 的目标 IP 与源 IP 相同。

![故障转移之前的 Azure 到 Azure 连接](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>整个区域的故障转移

在发生区域性服务中断时，**公司 A** 可以使用 Azure Site Recovery 的强大[恢复计划](site-recovery-create-recovery-plans.md)快速轻松地恢复其整个部署。 由于在故障转移之前已经为每个 VM 设置了目标 IP 地址，**公司 A** 可以在恢复 VNet 与 Azure VNet 之间协调故障转移和自动建立连接，如下图所示。

![Azure 到 Azure 连接 - 整个区域的故障转移](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover.png)

根据应用程序的要求，可以在故障转移之前、期间（作为中间步骤）或之后，在目标区域中的两个 VNet 之间建立连接。 使用[恢复计划](site-recovery-create-recovery-plans.md)可以添加脚本和定义故障转移顺序。

公司 A 还可以选择使用 VNet 对等互连或站点到站点 VPN 在恢复 VNet 与 Azure VNet 之间建立连接。 VNet 对等互连不使用 VPN 网关，并且有不同的约束。 另外，[VNet 对等互连定价](https://azure.microsoft.com/pricing/details/virtual-network)的计算不同于 [VNet 到 VNet VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway)的计算。 对于故障转移，我们通常建议模仿源连接（包括连接类型），以便最大程度地减少因网络更改而产生不可预知的事件。

### <a name="isolated-application-failover"></a>隔离的应用程序故障转移

在某些情况下，用户可能需要故障转移其一部分应用程序基础结构。 一个例子是故障转移专用子网中托管的某个特定应用程序或层。 尽管可以在保留 IP 的情况下进行子网故障转移，但在大多数情况下我们不建议这样做，因为这会明显增加连接不一致的情况。 此外，还会与同一 Azure 虚拟网络中的其他子网断开子网连接。

应对子网级应用程序故障转移要求的更好方法是使用不同的目标 IP 地址进行故障转移（如果需要与源虚拟网络中的其他子网建立连接），或者将每个应用程序隔离在源中其自身的专用虚拟网络内。 使用后一种方法可以在源中建立网络间连接，同时，在故障转移到目标区域时可以模拟相同的连接。

若要构建具有复原能力的单个应用程序，我们建议将应用程序托管在其自身的专用虚拟网络中，并根据需要这些虚拟网络之间建立连接。 这样，便可以实现隔离的应用程序故障转移，同时保留原始专用 IP 地址。

故障转移之前的配置如下所示：
- 应用程序 VM 托管在 Azure 东亚，对第一个和第二个应用程序分别使用地址空间为 10.1.0.0/16 和 15.1.0.0/16 的 Azure 虚拟网络。 第一个和第二个应用程序的虚拟网络分别名为**源 VNet1** 和**源 VNet2**。
- 每个 VNet 进一步划分为两个子网。
- Azure 东南亚是目标区域，包含恢复虚拟网络“恢复 VNet1”和“恢复 VNet2”。
- 副本节点（例如 Always On、域控制器等组件所需的节点）放置在地址为 20.1.0.0/24 的**子网 4** 中地址空间为 20.1.0.0/16 的虚拟网络内。 此虚拟网络名为 Azure VNet，位于 Azure 东南亚。
- **源 VNet1** 和 **Azure VNet** 通过 VPN 站点到站点连接建立连接。 同样，**源 VNet2** 和 **Azure VNet** 也通过 VPN 站点到站点连接建立连接。
- 本示例中的**源 VNet1** 和**源 VNet2** 还通过 S2S VPN 建立连接。 由于两个 VNet 位于同一个区域，因此还使用了 VNet 对等互连，而未使用 S2S VPN。
- **恢复 VNet1** 和**恢复 VNet2** 未与其他任何虚拟网络相连接。
- 为了降低恢复时间目标 (RTO)，在故障转移之前，可在**恢复 VNet1** 和**恢复 VNet2** 上配置 VPN 网关。

![Azure 到 Azure 连接 - 故障转移之前隔离的应用程序](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover.png)

如果发生了只影响一个应用程序（在本示例中，该应用程序托管在源 VNet2 中），公司 A 可按如下所述恢复受影响的应用程序：
- 断开**源 VNet1** 与**源 VNet2**，以及**源 VNet2** 与 **Azure VNet** 之间的 VPN 连接。
- 在**源 VNet1** 与**恢复 VNet2**，以及**恢复 VNet2** 与 **Azure VNet** 之间建立 VPN 连接。
- 将 VM 从**源 VNet2** 故障转移到**恢复 VNet2**。

![Azure 到 Azure 连接 - 故障转移之后隔离的应用程序](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover.png)

可以延伸上述隔离故障转移示例，以包括更多的应用程序和网络连接。 从源故障转移到目标时，我们建议尽可能地遵循相似性连接模式。

### <a name="further-considerations"></a>其他注意事项

VPN 网关利用公共 IP 地址和网关跃点建立连接。 如果不想要使用公共 IP 和/或想要避免额外的跃点，可以使用全局 VNet 对等互连在 Azure 区域之间建立虚拟网络对等互连。

此功能目前以公共预览版提供，正在扩展到更多支持区域 - 它可以实现直接的 VM 到 VM 连接，且无需任何公共 Internet 或额外的跃点。

有关详细信息，请参阅[对等互连文档](../virtual-network/tutorial-connect-virtual-networks-portal.md#register)和[定价](https://azure.microsoft.com/pricing/details/virtual-network/)。

## <a name="on-premises-to-azure-connectivity"></a>本地到 Azure 的连接

对于第二种方案，假设**公司 B** 在 Azure 中运行其一部分应用程序基础结构，在本地运行剩余的部分。 出于业务连续性和法规遵从的原因，**公司 B** 决定使用 Azure Site Recovery 来保护 Azure 中运行的应用程序。

下面是故障转移之前网络体系结构的外观：
- 应用程序 VM 托管在 Azure 东亚，利用地址空间为 10.1.0.0/16 的 Azure 虚拟网络。 此虚拟网络名为**源 VNet**。
- 应用程序工作负荷在以下三个子网之间拆分 – 10.1.0.0/24、10.1.1.0/24 和 10.1.2.0/24，它们分别名为**子网 1**、**子网 2** 和**子网 3**。
- Azure 东南亚是目标区域，包含一个用于模仿源中地址空间和子网配置的恢复虚拟网络。 此虚拟网络名为**恢复 VNet**。
- Azure 东亚的 VM 通过 ExpressRoute 或站点到站点 VPN 连接到本地数据中心。
- 为了降低恢复时间目标 (RTO)，公司 B 在故障转移之前，在 Azure 东南亚的恢复 VNet 中预配了网关。
- **公司 B** 分配/验证复制项的目标 IP 地址。 在此示例中，每个 VM 的目标 IP 与源 IP 相同

![故障转移之前的本地到 Azure 连接](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>整个区域的故障转移

在发生区域性服务中断时，**公司 B** 可以使用 Azure Site Recovery 的强大[恢复计划](site-recovery-create-recovery-plans.md)快速轻松地恢复其整个部署。 由于在故障转移之前已经为每个 VM 设置了目标 IP 地址，**公司 B** 可以在恢复 VNet 与本地数据中心之间协调故障转移和自动建立连接，如下图所示。

在 Azure 东南亚与本地数据中心之间之间建立连接之前，应断开 Azure 东亚与本地数据中心之间的原始连接。 此外，本地路由重新配置为在故障转移之后指向目标区域和网关。

![故障转移之后的本地到 Azure 连接](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover.png)

### <a name="subnet-failover"></a>子网故障转移

与针对**公司 A** 所述的 Azure 到 Azure 方案不同，在这种情况下，**公司 B** 无法实现子网级故障转移。这是因为，源和恢复虚拟网络中的地址空间都相同，并且源到本地的原始连接处于活动状态。

为了实现应用程序复原，我们建议将每个应用程序托管在其自身的专用 Azure 虚拟网络中。 然后，可根据上面所述，在隔离环境中故障转移应用程序，并将所需的本地到源连接路由到目标区域。

## <a name="next-steps"></a>后续步骤
- 详细了解[恢复计划](site-recovery-create-recovery-plans.md)。
