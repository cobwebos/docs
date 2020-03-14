---
title: Azure Cosmos DB 中的高可用性
description: 本文介绍 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 0f024bac535ed792d8480c991e470cf5d85932b8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247418"
---
# <a name="high-availability-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 实现高可用性

Azure Cosmos DB 以透明方式在与 Cosmos 帐户关联的所有 Azure 区域之间复制数据。 Cosmos DB 对数据采用多层冗余，如下图所示：

![物理分区](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos 容器中的数据进行[水平分区](partitioning-overview.md)。

- 在每个区域中，每个分区受副本集的保护，该副本集中的大多数副本将复制并以持久方式提交所有写入内容。 副本分布在最多 10 到 20 个容错域中。

- 将复制所有区域中的每个分区。 每个区域包含某个 Cosmos 容器的所有数据分区，可接受写入并为读取提供服务。  

如果你的 Cosmos 帐户分布在*N*个 Azure 区域中，则你的所有数据将至少有*N* x 4 个副本。 除了提供低延迟的数据访问和在与 Cosmos 帐户关联的区域之间扩展写入/读取吞吐量之外，还有更多区域（更高的*N*）可以进一步提高可用性。  

## <a name="slas-for-availability"></a>可用性 SLA

作为全局分布式数据库，Cosmos DB 提供综合性的 SLA，涵盖了吞吐量、99% 时间内的延迟、一致性和高可用性。 下表显示了 Cosmos DB 针对单区域和多区域帐户提供的高可用性保证。 为实现高可用性，请始终将 Cosmos 帐户配置为具有多个写入区域。

|操作类型  | 单区域 |多区域（单区域写入）|多区域（多区域写入） |
|---------|---------|---------|-------|
|写入    | 99.99    |99.99   |99.999|
|读取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 在实践中，受限过期、会话、一致前缀和最终一致性模型的实际写入可用性明显高于发布的 Sla。 所有一致性级别的实际读取可用性明显高于发布的 SLA。

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>在发生区域性中断时具有 Cosmos DB 的高可用性

区域性服务中断并不少见，而 Azure Cosmos DB 可确保你的数据库始终高度可用。 以下详细信息会在中断期间捕获 Cosmos DB 行为，具体取决于你的 Cosmos 帐户配置：

- 使用 Cosmos DB 时，在客户端确认写入操作之前，数据将由接受写入操作的区域中的副本仲裁持久提交。

- 配置有多个写入区域的多区域帐户对于写入和读取都将具有高可用性。 区域性故障转移可在瞬间完成，不需要在应用程序中进行任何更改。

- 发生区域性服务中断时，单区域帐户可能会失去可用性。 始终建议使用 Cosmos 帐户至少设置**两个区域**（最好至少是两个），以确保在任何时候都能实现高可用性。

- **具有单一写入区域（写入区域中断）的多区域帐户：**
  - 在写入区域中断期间，如果在 Azure Cosmos 帐户上配置了 "**启用自动故障转移**"，则 Cosmos 帐户会将次要区域自动升级为新的主要写入区域。 启用后，按指定的区域优先级顺序将故障转移到另一个区域。
  - 客户还可以选择使用**手动故障转移**，并使用自己构建的代理自行监视其 Cosmos 写入终结点 URL。 对于具有复杂、复杂的运行状况监视需求的客户，这可以提供在写入区域发生故障时的 RTO。
  - 当上一个受影响的区域重新联机时，在该区域发生故障时，任何未复制的写入数据都将通过[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)提供。 应用程序可以读取冲突源，根据应用程序特定的逻辑解决冲突，并根据需要将更新后的数据写入到 Azure Cosmos 容器。
  - 以前受影响的写入区域恢复后，它将自动用作读取区域。 可以切换回作为写入区域的已恢复区域。 您可以使用[Azure CLI 或 Azure 门户](how-to-manage-database-account.md#manual-failover)来切换区域。 在切换写入区域之前、之后或之后，应用程序将继续高度可用之前，不会出现**数据或可用性损失**。

- **具有单一写入区域（读取区域中断）的多区域帐户：**
  - 在发生读取区域服务中断期间，这些帐户将保持很高的读写可用性。
  - 受影响的区域会自动断开连接，并将标记为脱机。 [Azure Cosmos DB sdk](sql-api-sdk-dotnet.md)会将读取调用重定向到首选区域列表中的下一个可用区域。
  - 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。
  - 处理读取区域服务中断不需要对应用程序代码进行更改。 最终，当受影响区域重新联机时，以前受影响的读取区域将自动与当前写入区域同步，并再次可用于为读取请求提供服务。
  - 后续的读取会重定向到恢复的区域，不需更改应用程序代码。 在以前失败的区域的故障转移和重新加入期间，读取一致性保证会继续 Cosmos DB。

- 即使在不常发生的事件中，当 Azure 区域永久恢复时，如果多区域 Cosmos 帐户配置了*强*一致性，则不会丢失数据。 在永久无法恢复的写入区域（配置有有限过期一致性的多区域 Cosmos 帐户）的情况下，可能的数据丢失时段限制为过期时段（*k*或*t*），其中 k = 100000 更新，t = 5 分钟。 对于会话、一致前缀和最终一致性级别，潜在的数据丢失时段限制为最多15分钟。 有关 Azure Cosmos DB 的 RTO 和 RPO 目标的详细信息，请参阅[一致性级别和数据持续](consistency-levels-tradeoffs.md#rto)性

## <a name="availability-zone-support"></a>可用性区域支持

除了跨区域复原功能，现在还可以在选择要与 Azure Cosmos 数据库相关联的区域时启用**区域冗余**。

通过可用性区域支持，Azure Cosmos DB 确保将副本放置在给定区域内的多个区域，以便在发生区域性故障时提供高可用性和复原能力。 此配置中不存在对延迟和其他 Sla 的更改。 如果发生单一区域故障，区域冗余将使用 RPO = 0 提供完整的数据持续性，并使用 RTO = 0 实现可用性。

区域冗余是[多主机复制](how-to-multi-master.md)功能的*补充功能*。 只有区域冗余才能实现区域复原。 例如，如果区域中断或跨区域的低延迟访问，则建议除了区域冗余以外，还有多个写区域。

为你的 Azure Cosmos 帐户配置多区域写入时，你可以选择不额外收费的区域冗余。 否则，请参阅下面的说明，了解有关区域冗余支持的定价。 可以通过删除区域并将其重新添加到已启用的区域冗余，来在 Azure Cosmos 帐户的现有区域启用区域冗余。

此功能在以下 Azure 区域中提供：

- 英国南部

- 东南亚

- 美国东部

- 美国东部 2

- 美国中部

- 西欧

- 美国西部 2

> [!NOTE]
> 为单个区域的 Azure Cosmos 帐户启用可用性区域将产生与将其他区域添加到帐户相同的费用。 有关定价的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)和[Azure Cosmos DB 文章中的多区域成本](optimize-cost-regions.md)。

下表总结了各种帐户配置的高可用性功能：

|KPI  |无可用性区域的单个区域（非 AZ）  |单区域与可用性区域（AZ）  |使用可用性区域（AZ，2个区域）进行多区域写入–最建议的设置 |
|---------|---------|---------|---------|
|写入可用性 SLA | 99.99% | 99.99% | 99.999% |
|读取可用性 SLA  | 99.99% | 99.99% | 99.999% |
|价格 | 单区域计费率 | 单区域可用性区域计费率 | 多区域计费率 |
|区域故障–数据丢失 | 数据丢失 | 无数据丢失 | 无数据丢失 |
|区域故障–可用性 | 可用性损失 | 无可用性损失 | 无可用性损失 |
|读取延迟 | 跨区域 | 跨区域 | 低 |
|写入延迟 | 跨区域 | 跨区域 | 低 |
|地区性中断–数据丢失 | 数据丢失 |  数据丢失 | 数据丢失 <br/><br/> 在对多个主区域和多个区域使用有限过期一致性时，数据丢失限制为帐户上配置的界限过期 <br /><br />通过配置与多个区域的强一致性，可避免在区域性中断期间发生数据丢失。 此选项附带会影响可用性和性能的权衡。 只能对为单区域写入配置的帐户进行配置。 |
|地区性中断–可用性 | 可用性损失 | 可用性损失 | 无可用性损失 |
|吞吐量 | X RU/秒预配吞吐量 | X RU/秒预配吞吐量 | 已预配 2X RU/秒的吞吐量 <br/><br/> 与具有可用性区域的单个区域相比，此配置模式需要两倍的吞吐量，因为有两个区域。 |

> [!NOTE]
> 若要为多区域 Azure Cosmos 帐户启用可用性区域支持，帐户必须启用多主机写入。

将区域添加到新的或现有的 Azure Cosmos 帐户时，可以启用区域冗余。 若要在 Azure Cosmos 帐户上启用区域冗余，应将 `isZoneRedundant` 标志设置为特定位置 `true`。 可以在 "位置" 属性中设置此标志。 例如，以下 powershell 代码片段为 "东南亚" 区域启用区域冗余：

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1 }
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

创建 Azure Cosmos 帐户时，可以通过使用 Azure 门户启用可用性区域。 创建帐户时，请确保启用**异地冗余**、**多区域写入**，并选择支持可用性区域的区域：

![使用 Azure 门户启用可用性区域](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>生成高可用性应用程序

- 为确保较高的写入和读取可用性，请将 Cosmos 帐户配置为跨越多个写入区域中的至少两个区域。 此配置将为 Sla 支持的读取和写入提供最高的可用性、最低延迟和最大的可伸缩性。 详细了解如何[将 Cosmos 帐户配置为使用多个写入区域](tutorial-global-distribution-sql-api.md)。

- 对于配置为使用单个写入区域的多区域 Cosmos 帐户，请[使用 Azure CLI 或 Azure 门户中启用自动故障转移](how-to-manage-database-account.md#automatic-failover)。 启用自动故障转移后，每当发生区域性灾难时，Cosmos DB 都会自动故障转移你的帐户。  

- 即使 Cosmos 帐户具有高可用性，应用程序也不一定能够正常保持高可用性。 若要测试应用程序的端到端高可用性，作为应用程序测试或灾难恢复（DR）演练的一部分，请暂时禁用该帐户的自动故障转移，[通过使用 Azure CLI 或 Azure 门户调用手动故障](how-to-manage-database-account.md#manual-failover)转移，然后监视应用程序的故障转移。 完成后，可以故障回复到主要区域，并还原该帐户的自动故障转移。

- 在全球分布式数据库环境中，一致性级别与数据持续性之间的直接关系是在发生区域范围的服务中断。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为恢复点目标 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 和 RTO，请参阅[一致性级别和数据持续性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>后续步骤

接下来可以阅读以下文章：

- [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
- [全局缩放预配的吞吐量](scaling-throughput.md)
- [全球分布 - 揭秘](global-dist-under-the-hood.md)
- [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
- [如何配置包含多个写入区域的 Cosmos 帐户](how-to-multi-master.md)
