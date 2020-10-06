---
title: Azure Cosmos DB 中的高可用性
description: 本文介绍 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 85ffff1a38d42aac00ce122a1e51d79515b0c51b
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762019"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Azure Cosmos DB 如何提供高可用性？ 

Azure Cosmos DB 以透明方式在与 Azure Cosmos 帐户关联的所有 Azure 区域之间复制数据。 Azure Cosmos DB 对数据采用多层冗余，如下图所示：

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="物理分区" border="false":::

- Azure Cosmos 容器中的数据[已水平分区](partitioning-overview.md)。

- 分区集是多个副本集的集合。 在每个区域中，每个分区受副本集的保护，该副本集中的大多数副本将复制并以持久方式提交所有写入内容。 副本分布在最多 10 到 20 个容错域中。

- 将复制所有区域中的每个分区。 每个区域包含某个 Azure Cosmos 容器的所有数据分区，可接受写入并维护读取。  

如果 Azure Cosmos 帐户分布在 N 个 Azure 区域之间，则所有数据至少有 N x 4 个副本。 通常情况下，在超过 2 个区域中拥有 Azure Cosmos 帐户可提高应用程序的可用性，并在相关区域之间提供较低的延迟。 

## <a name="slas-for-availability"></a>可用性 SLA

作为一种全球分布式数据库，Azure Cosmos DB 提供全面的 Sla，其中包含吞吐量、99% 的延迟、一致性和高可用性。 下表显示 Azure Cosmos DB 针对单区域和多区域帐户提供的高可用性保证。 为实现高可用性，请始终将 Azure Cosmos 帐户配置为具有多个写入区域。

|操作类型  | 单区域 |多区域（单区域写入）|多区域（多区域写入） |
|---------|---------|---------|-------|
|写入    | 99.99    |99.99   |99.999|
|读取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 在实践中，有限过期、会话、一致前缀和最终一致性模型的实际写入可用性明显高于发布的 SLA。 所有一致性级别的实际读取可用性明显高于发布的 SLA。

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>使用 Azure Cosmos DB 在发生区域性服务中断时提供高可用性

对于区域性服务中断的罕见情况，Azure Cosmos DB 可确保你的数据库始终保持高可用性。 下面根据 Azure Cosmos 帐户配置详细介绍 Azure Cosmos DB 在服务中断期间的行为：

- 使用 Azure Cosmos DB 时，在客户端确认写入操作之前，数据将由接受写入操作的区域中的副本仲裁持续提交。

- 配置有多个写入区域的多区域帐户对于写入和读取都将具有高可用性。 区域性故障转移可在瞬间完成，不需要在应用程序中进行任何更改。

- 发生区域性服务中断时，单区域帐户可能会失去可用性。 始终建议对 Azure Cosmos 帐户至少设置两个区域（最好至少设置两个写入区域），以确保始终保持高可用性。

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>配置为使用单个写入区域的多区域帐户（写入区域服务中断）

- 在写入区域服务中断期间，如果在 Azure Cosmos 帐户上配置了“启用自动故障转移”，则 Azure Cosmos 帐户会自动将次要区域提升为新的主要写入区域。 当启用后，将按您指定的区域优先级顺序故障转移到其他区域。
- 当上一个受影响的区域重新联机时，可以通过[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)使用该区域发生故障时未复制的任何写入数据。 应用程序可以读取冲突源，根据应用程序特定的逻辑解决冲突，并相应地将更新后的数据写回 Azure Cosmos 容器。
- 以前受影响的写入区域恢复后，它将自动用作读取区域。 可以切换回到用作写入区域的已恢复区域。 可以使用 [PowerShell、Azure CLI 或 Azure 门户](how-to-manage-database-account.md#manual-failover)来切换区域。 在切换写入区域之前、期间或之后，**不会丢失数据或可用性**，应用程序将继续保持高可用性。

> [!IMPORTANT]
> 强烈建议将用于生产工作负载的 Azure Cosmos 帐户配置为“启用自动故障转移”  。 手动故障转移要求在辅助写入区域与主要写入区域之间进行连接来完成一致性检查，确保在故障转移期间不会丢失数据。 如果主要区域不可用，则此一致性检查无法完成，手动故障转移不会成功，导致不可写入。

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>配置为使用单个写入区域的多区域帐户（读取区域服务中断）

- 在读取区域服务中断期间，使用任何一致性级别或强一致性且具有三个或更多读取区域的 Azure Cosmos 帐户仍将对读取和写入保持高可用性。
- Azure Cosmos 帐户使用具有两个或更少读取区域的强一致性 (其中包括读取 & 写入区域) 会在读取区域中断期间丢失读取写入可用性。
- 受影响的区域将自动断开连接，并标记为脱机。 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 会将读取调用重定向到首选区域列表中的下一个可用区域。
- 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。
- 处理读取区域服务中断不需要对应用程序代码进行更改。 当受影响的读取区域重新联机时，它会自动与当前写入区域同步，并再次可用于为读取请求提供服务。
- 后续的读取会重定向到恢复的区域，不需更改应用程序代码。 在故障转移和重新加入之前发生故障的区域期间，Azure Cosmos DB 会继续提供读取一致性保证。
- 即使在发生了 Azure 区域永久无法恢复的罕见不幸事件中，如果为多区域 Azure Cosmos 帐户配置了强一致性，也不会丢失数据。 如果出现永久不可恢复的写入区域，对于配置了有限过期一致性的多区域 Azure Cosmos 帐户，潜在的数据丢失时段限制为过期时段（K 或 T），其中 K = 100,000 次更新，T = 5 分钟。 对于会话、一致前缀和最终一致性级别，潜在的数据丢失时段限制为最多 15 分钟。 有关 Azure Cosmos DB 的 RTO 和 RPO 目标的详细信息，请参阅[一致性级别和数据持续性](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>可用性区域支持

除了跨区域复原功能，现在还可以在选择要与 Azure Cosmos 数据库相关联的区域时启用 **区域冗余** 。

通过可用性区域支持，Azure Cosmos DB 确保将副本放置在给定区域内的多个区域，以便在发生区域性故障时提供高可用性和复原能力。 此配置中不存在对延迟和其他 Sla 的更改。 如果发生单一区域故障，区域冗余将使用 RPO = 0 提供完整的数据持续性，并使用 RTO = 0 实现可用性。

区域冗余是[在多区域写入功能中进行复制](how-to-multi-master.md)的*补充功能*。 只有区域冗余才能实现区域复原。 例如，如果区域中断或跨区域的低延迟访问，则建议除了区域冗余以外，还有多个写区域。

为你的 Azure Cosmos 帐户配置多区域写入时，你可以选择不额外收费的区域冗余。 否则，请参阅下面的说明，了解有关区域冗余支持的定价。 可以通过删除区域并将其重新添加到已启用的区域冗余，来在 Azure Cosmos 帐户的现有区域启用区域冗余。

此功能在以下区域中提供： *英国南部、东南亚、美国东部、美国东部2、美国中部、西欧、美国西部2、日本东部、北欧、法国中部、澳大利亚东部、美国东部 2 EUAP* 。

下表总结了各种帐户配置的高可用性功能：

|KPI  |无可用性区域 (非 AZ) 的单个区域  |具有可用性区域 (AZ 的单个区域)   |使用可用性区域 (AZ、2个区域) 进行多区域写入–最建议的设置 |
|---------|---------|---------|---------|
|写入可用性 SLA | 99.99% | 99.99% | 99.999% |
|读取可用性 SLA  | 99.99% | 99.99% | 99.999% |
|价格 | 单区域计费率 | 单区域可用性区域计费率 | 多区域计费率 |
|区域故障–数据丢失 | 数据丢失 | 无数据丢失 | 无数据丢失 |
|区域故障–可用性 | 可用性损失 | 无可用性损失 | 无可用性损失 |
|读取延迟 | 跨区域 | 跨区域 | 低 |
|写入延迟 | 跨区域 | 跨区域 | 低 |
|地区性中断–数据丢失 | 数据丢失 |  数据丢失 | 数据丢失 <br/><br/> 在对多个写入区域和多个区域使用有限过期一致性时，数据丢失限制为在帐户上配置的界限过期 <br /><br />通过配置与多个区域的强一致性，可避免在区域性中断期间发生数据丢失。 此选项附带会影响可用性和性能的权衡。 只能对为单区域写入配置的帐户进行配置。 |
|地区性中断–可用性 | 可用性损失 | 可用性损失 | 无可用性损失 |
|吞吐量 | X RU/秒预配吞吐量 | X RU/s 预配吞吐量 * 1.25 | 已预配 2X RU/秒的吞吐量 <br/><br/> 与具有可用性区域的单个区域相比，此配置模式需要两倍的吞吐量，因为有两个区域。 |

> [!NOTE]
> 若要为多区域 Azure Cosmos 帐户启用可用性区域支持，帐户必须启用多区域写入。

将区域添加到新的或现有的 Azure Cosmos 帐户时，可以启用区域冗余。 若要在 Azure Cosmos 帐户上启用区域冗余，应将标志设置为，以 `isZoneRedundant` `true` 指定特定位置。 可以在 "位置" 属性中设置此标志。 例如，以下 PowerShell 代码片段为 "东南亚" 区域启用区域冗余：

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

以下命令显示了如何为 "EastUS" 和 "WestUS2" 区域启用区域冗余：

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

创建 Azure Cosmos 帐户时，可以通过使用 Azure 门户启用可用性区域。 创建帐户时，请确保启用 **异地冗余**、 **多区域写入**，并选择支持可用性区域的区域：

:::image type="content" source="./media/high-availability/enable-availability-zones-using-portal.png" alt-text="物理分区"::: 

## <a name="building-highly-available-applications"></a>生成高可用性应用程序

- 查看 [Azure Cosmos sdk](troubleshoot-sdk-availability.md) 在这些事件中的预期行为，以及这些事件的影响配置。

- 若要确保较高的写入和读取可用性，请将 Azure Cosmos 帐户配置为跨越至少两个区域并使用多个写入区域。 对于读取和写入，此配置都可提供由 SLA 作为保障的最高可用性、最低延迟和最佳可伸缩性。 若要了解详细信息，请参阅如何[将 Azure Cosmos 帐户配置为使用多个写入区域](tutorial-global-distribution-sql-api.md)。

- 对于配置为使用单个写入区域的多区域 Azure Cosmos 帐户，请[使用 Azure CLI 或 Azure 门户启用自动故障转移](how-to-manage-database-account.md#automatic-failover)。 启用自动故障转移后，每当发生区域性灾难时，Cosmos DB 都会自动故障转移你的帐户。  

- 即使 Azure Cosmos 帐户具有高可用性，应用程序也不一定能够正常保持高可用性。 若要测试应用程序的端到端高可用性，请在应用程序测试或灾难恢复 (DR) 演练过程中，暂时禁用帐户的自动故障转移功能，[使用 PowerShell、Azure CLI 或 Azure 门户调用手动故障转移](how-to-manage-database-account.md#manual-failover)，然后监视应用程序的故障转移。 完成后，可以故障回复到主区域，然后还原该帐户的自动故障转移。

- 在全球分布式数据库环境中，一致性级别与数据持续性之间的直接关系是在发生区域范围的服务中断。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为恢复点目标 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 和 RTO，请参阅[一致性级别和数据持续性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>后续步骤

接下来可以阅读以下文章：

- [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
- [全局缩放预配的吞吐量](scaling-throughput.md)
- [全球分布 - 揭秘](global-dist-under-the-hood.md)
- [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
- [如何将 Cosmos 帐户配置为使用多个写入区域](how-to-multi-master.md)
- [Multiregional 环境中的 SDK 行为](troubleshoot-sdk-availability.md)