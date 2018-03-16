---
title: "将 Azure 流量管理器与 Azure Site Recovery 配合使用 | Microsoft Docs"
description: "介绍如何结合使用 Azure Site Recovery 和 Azure 流量管理器来实现灾难恢复和迁移"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 8305a354588875926cab52a55d99d3a29bcfb509
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>将 Azure 流量管理器与 Azure Site Recovery 配合使用

使用 Azure 流量管理器可以控制流量在应用程序终结点之间的分布。 终结点可以是托管在 Azure 内部或外部的任何面向 Internet 的服务。

流量管理器根据流量路由方法和终结点的运行状况，使用域名系统 (DNS) 将客户端请求定向到最合适的终结点。 流量管理器提供多种[流量路由方法](../traffic-manager/traffic-manager-routing-methods.md)和[终结点监视选项](../traffic-manager/traffic-manager-monitoring.md)来满足不同的应用程序需求和自动故障转移模型。 客户端直接连接到选定的终结点。 流量管理器不是代理或网关，看不到流量在客户端与服务之间传递。

本文介绍如何将 Azure 流量监视器的智能路由与 Azure Site Recovery 的强大灾难恢复和迁移功能结合使用。

## <a name="on-premises-to-azure-failover"></a>本地到 Azure 的故障转移

对于第一种方案，我们假设**公司 A** 的所有应用程序基础结构在其本地环境中运行。 出于业务连续性和法规遵从的原因，**公司 A** 决定使用 Azure Site Recovery 来保护其应用程序。

**公司 A** 使用公共终结点运行应用程序，希望在发生灾难时能够无缝将流量重定向到 Azure。 公司 A 可以使用 Azure 流量管理器中的[优先级](../traffic-manager/traffic-manager-configure-priority-routing-method.md)流量路由方法来轻松实现此故障转移模式。

设置如下：
- **公司 A** 创建[流量管理器配置文件](../traffic-manager/traffic-manager-create-profile.md)。
- **公司 A** 利用**优先级**路由方法创建两个终结点 – 针对本地的**主要**终结点，针对 Azure 的**故障转移**终结点。 为**主要**终结点分配优先级 1，为**故障转移**终结点分配优先级 2。
- 由于**主要**终结点托管在 Azure 外部，因此该终结点创建为[外部](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)终结点。
- 使用 Azure Site Recovery 时，Azure 站点在故障转移之前不会运行任何虚拟机或应用程序。 因此，**故障转移**终结点也创建为**外部**终结点。
- 用户流量默认定向到本地应用程序，因为该终结点的关联优先级最高。 如果**主要**终结点处于正常状态，则不会将任何流量路由到 Azure。

![本地到 Azure 故障转移之前](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

在发生灾难时，公司 A 可以触发到 Azure 的[故障转移](site-recovery-failover.md)，并在 Azure 上恢复其应用程序。 当 Azure 流量管理器检测到**主要**终结点不再正常时，会自动在 DNS 响应中使用**故障转移**终结点，用户将连接到 Azure 上恢复的应用程序。

![本地到 Azure 故障转移之后](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

根据业务要求，在发生灾难时，**公司 A** 可以选择以更高或更低的[探测频率](../traffic-manager/traffic-manager-monitoring.md)在本地与 Azure 之间切换，并确保将用户停机时间减到最小。

遏制灾难后，**公司 A** 可以使用 Azure Site Recovery 从 Azure 故障回复到其本地环境（[VMware](vmware-azure-failback.md) 或 [Hyper-V](hyper-v-azure-failback.md)）。 当流量管理器检测到**主要**终结点再次正常后，可在其 DNS 响应中自动利用**主要**终结点。

## <a name="on-premises-to-azure-migration"></a>本地到 Azure 的迁移

除灾难恢复以外，Azure Site Recovery 还支持[迁移到 Azure](migrate-overview.md)。 使用 Azure Site Recovery 的强大测试故障转移功能，客户可以在 Azure 上评估应用程序性能，而不影响其本地环境。 当客户准备好迁移时，可以选择统一迁移整个工作负荷，或选择逐渐迁移和缩放。

Azure 流量管理器的[加权](../traffic-manager/traffic-manager-configure-weighted-routing-method.md)路由方法可用于将一部分传入流量定向到 Azure，同时将大部分流量定向到本地环境。 此方法有助于评估缩放性能，因为在将越来越多的工作负荷迁移到 Azure 时，可以不断地增大分配给 Azure 的权重。

例如，**公司 B** 可以选择分阶段迁移：转移一部分应用程序环境，同时将剩余的部分保留在本地。 在初始阶段，当大部分环境位于本地时，可向本地环境分配一个较大的权重。 流量管理器根据分配给可用终结点的权重返回终结点。

![本地到 Azure 的迁移](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

在迁移期间，两个终结点处于活动状态，大部分流量定向到本地环境。 在迁移过程中，可向 Azure 上的终结点分配一个较大的权重，迁移后，最终可以停用本地终结点。

## <a name="azure-to-azure-failover"></a>Azure 到 Azure 的故障转移

对于此示例，我们假设**公司 C** 的所有应用程序基础结构在 Azure 中运行。 出于业务连续性和法规遵从的原因，**公司 C** 决定使用 Azure Site Recovery 来保护其应用程序。

**公司 C** 使用公共终结点运行应用程序，希望在发生灾难时能够无缝将流量重定向到不同的 Azure 区域。 **公司 C** 客户可以通过[优先级](../traffic-manager/traffic-manager-configure-priority-routing-method.md)流量路由方法来轻松实现此故障转移模式。

设置如下：
- **公司 C** 创建[流量管理器配置文件](../traffic-manager/traffic-manager-create-profile.md)。
- **公司 C** 利用**优先级**路由方法创建两个终结点 – 针对源区域（Azure 东亚）的**主要**终结点，以及针对恢复区域（Azure 东南亚）的**故障转移**终结点。 为**主要**终结点分配优先级 1，为**故障转移**终结点分配优先级 2。
- 由于**主要**终结点托管于 Azure 中，因此它可以用作 [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) 终结点。
- 使用 Azure Site Recovery 时，Azure 恢复站点在故障转移之前不会运行任何虚拟机或应用程序。 因此，**故障转移**终结点可创建为[外部](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)终结点。
- 用户流量默认定向到源区域（东亚）应用程序，因为该终结点的关联优先级最高。 如果**主要**终结点处于正常状态，则不会将任何流量路由到恢复区域。

![Azure 到 Azure 故障转移之前](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

在发生灾难时，**公司 C** 可以触发[故障转移](azure-to-azure-tutorial-failover-failback.md)，并在恢复 Azure 区域中恢复其应用程序。 当 Azure 流量管理器检测到主要终结点不再正常时，会自动在 DNS 响应中使用**故障转移**终结点，用户将连接到恢复 Azure 区域（东南亚）中已恢复的应用程序。

![Azure 到 Azure 故障转移之后](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

根据业务要求，**公司 C** 可以选择以更高或更低的[探测频率](../traffic-manager/traffic-manager-monitoring.md)在源区域与恢复区域之间切换，并确保将用户停机时间减到最小。

遏制灾难后，**公司 C** 可以使用 Azure Site Recovery 从恢复 Azure 区域故障回复到源 Azure 区域。 当流量管理器检测到**主要**终结点再次正常后，可在其 DNS 响应中自动利用**主要**终结点。

## <a name="protecting-multi-region-enterprise-applications"></a>保护多区域企业应用程序

全球的企业往往通过定制其应用程序来满足区域需求，从而提高客户体验。 借助本地化和更低的延迟，可以在不同的区域之间拆分应用程序基础结构。 此外，企业需要遵守某些区域的数据法规，因此，需要选择将一部分应用程序基础结构隔离在区域边界内。  

假设**公司 D** 拆分了其应用程序终结点，以便为德国和其他区域的客户提供服务。 **公司 D** 利用 Azure 流量管理器的[地理](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)路由方法完成了此设置。 源自德国的所有流量将定向到**终结点 1**，源自德国境外的所有流量将定向到**终结点 2**。

此设置的问题在于，如果**终结点 1** 出于任何原因停止工作，则不会将任何流量重定向到**终结点 2**。 来自德国的流量持续定向到**终结点 1**，而不管该终结点的运行状况如何，因此，德国用户无法访问**公司 D** 的应用程序。 同样，如果**终结点 2** 脱机，则不会将任何流量重定向到**终结点 1**。

![设置前的多区域应用程序](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

为了避免发生此问题并确保应用程序的复原能力，**公司 D** 通过 Azure Site Recovery 使用了[嵌套式流量管理器配置文件](../traffic-manager/traffic-manager-nested-profiles.md)。 在嵌套式配置文件设置中，流量不会定向到单个终结点，而是定向到其他流量管理器配置文件。 下面是此设置的工作原理：
- **公司 D** 对流量管理器配置文件使用地理路由，而不是对单个终结点使用地理路由。
- 每个子流量管理器配置文件对主要和恢复终结点使用**优先级**路由，因此在**地理**路由中嵌套了**优先级**路由。
- 为了实现应用程序复原能力，在发生灾难时，每个工作负荷分布区利用 Azure Site Recovery 到故障转移到恢复区域。
- 当父流量管理器收到 DNS 查询时，会使用可用的终结点将该查询定向到与它对应的相关子流量管理器。

![设置后的多区域应用程序](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

例如，如果德国中部的终结点发生故障，应用程序可以快速恢复到德国东北部。 新终结点处理来自德国的流量，只会给用户造成极短的停机。 同样，如果西欧发生终结点服务中断，则可以将应用程序工作负荷恢复到北欧，同时，让 Azure 流量管理器将 DNS 重定向到可用的终结点。

可以扩展上述设置，以根据需要包含区域和终结点的多种组合。 流量管理器最多允许 10 种级别的嵌套配置文件，但不允许在嵌套配置中使用循环。

## <a name="recovery-time-objective-rto-considerations"></a>恢复时间目标 (RTO) 注意事项

在大多数组织，DNS 记录的添加或修改由独立的团队或组织外部的某人处理。 因此，更改 DNS 记录的任务极有难度。 管理 DNS 基础结构的其他团队或组织更新 DNS 记录所需的时间根据不同的组织而异，并会影响应用程序的 RTO。

如果利用流量管理器，可以减少 DNS 更新所需的工作。 在实际故障转移时，无需任何手动或脚本操作。 在发生灾难时，此方法有助于快速切换（因而降低 RTO），并避免耗时的 DNS 更改发生错误和造成不菲的成本。 使用流量管理器甚至可将故障回复自动化，否则必须单独管理故障回复。

通过基本或快速间隔运行状况检查设置正确的[探测间隔](../traffic-manager/traffic-manager-monitoring.md)可在故障转移期间大幅降低 RTO，并减少用户停机时间。

此外，可为流量管理器配置文件优化 DNS 生存时间 (TTL) 值。 TTL 是客户端缓存的 DNS 条目值。 在 TTL 范围内查询某条记录时，系统不会查询 DNS 两次。 每条 DNS 记录都有一个关联的 TTL。 减小此值会导致流量管理器的 DNS 查询次数增多，但由于发现中断的速度更快，因此可以降低 RTO。

如果客户端与权威 DNS 服务器之间的 DNS 解析程序数目增加，则客户端获得的 TTL 不会增加。 DNS 解析程序将 TTL“倒计时”，只会传递一个反映自缓存记录以来已消逝时间的 TTL 值。 这可以确保在 TTL 过后能够在客户端上刷新 DNS 记录，而不管链中的 DNS 解析程序数目如何。

## <a name="next-steps"></a>后续步骤
- 详细了解流量管理器的[路由方法](../traffic-manager/traffic-manager-routing-methods.md)。
- 详细了解[嵌套式流量管理器配置文件](../traffic-manager/traffic-manager-nested-profiles.md)。
- 详细了解[终结点监视](../traffic-manager/traffic-manager-monitoring.md)。
- 详细了解如何使用[恢复计划](site-recovery-create-recovery-plans.md)自动执行应用程序故障转移。
