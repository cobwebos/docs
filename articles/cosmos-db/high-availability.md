---
title: Azure Cosmos DB 中的高可用性
description: 本文介绍 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 928c943e21e7d00b87ac1e506b98d47107ac4348
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508549"
---
# <a name="high-availability-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 实现高可用性

Azure Cosmos DB 以透明方式在与 Cosmos 帐户关联的所有 Azure 区域之间复制数据。 Cosmos DB 对数据采用多层冗余，如下图所示：

![物理分区](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos 容器中的数据[已水平分区](partitioning-overview.md)。

- 在每个区域中，每个分区受副本集的保护，该副本集中的大多数副本将复制并以持久方式提交所有写入内容。 副本分布在最多 10 到 20 个容错域中。

- 将复制所有区域中的每个分区。 每个区域包含某个 Cosmos 容器的所有数据分区，可接受写入并为读取提供服务。  

如果 Cosmos 帐户分布在 *N* 个 Azure 区域之间，则所有数据至少有 *N* x 4 个副本。 除了在与 Cosmos 帐户关联的区域之间提供低延迟数据访问和缩放写入/读取吞吐量以外，部署更多的区域（*N* 值较高）还可进一步提高可用性。  

## <a name="slas-for-availability"></a>可用性 SLA

作为全局分布式数据库，Cosmos DB 提供综合性的 SLA，涵盖了吞吐量、99% 时间内的延迟、一致性和高可用性。 下表显示了 Cosmos DB 针对单区域和多区域帐户提供的高可用性保证。 为实现高可用性，请始终将 Cosmos 帐户配置为使用多个写入区域。

|操作类型  | 单区域 |多区域（单区域写入）|多区域（多区域写入） |
|---------|---------|---------|-------|
|写入    | 99.99    |99.99   |99.999|
|读取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 在实践中，有限过期、会话、一致前缀和最终一致性模型的实际写入可用性明显高于发布的 SLA。 所有一致性级别的实际读取可用性明显高于发布的 SLA。

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>使用 Cosmos DB 在遇到区域性服务中断时提供高可用性

区域性服务中断并不少见，而 Azure Cosmos DB 可确保你的数据库始终保持高可用性。 下面根据 Cosmos 帐户配置详细汇总了 Cosmos DB 在服务中断期间的行为：

- 使用 Cosmos DB 时，在客户端确认写入操作之前，数据将由接受写入操作的区域中的副本仲裁持久提交。

- 配置有多个写入区域的多区域帐户对于写入和读取都将具有高可用性。 区域性故障转移可在瞬间完成，不需要在应用程序中进行任何更改。

- **配置为使用单个写入区域的多区域帐户（写入区域服务中断）：** 在发生写入区域服务中断期间，这些帐户将保持很高的读取可用性。 但是，对于写入您必须 **"启用自动故障转移"** 上你 Cosmos 帐户添加到故障转移到另一个区域，受影响的区域。 故障转移将按指定的区域优先级进行。 当受影响的区域重新联机时，将通过[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)提供在服务中断期间保留在受影响写入区域中的未复制数据。 应用程序可以读取冲突源，根据应用程序特定的逻辑解决冲突，并相应地将更新后的数据写回 Cosmos 容器。 以前受影响的写入区域恢复后，它将自动用作读取区域。 可以调用手动故障转移，并将受影响的区域配置为写入区域。 同样，可以使用 [Azure CLI 或 Azure 门户](how-to-manage-database-account.md#manual-failover)执行手动故障转移。 在手动故障转移之前、期间或之后，**没有数据丢失或可用性丢失**。 应用程序仍然高度可用。 

- **配置为使用单个写入区域的多区域帐户（读取区域服务中断）：** 在发生读取区域服务中断期间，这些帐户将保持很高的读写可用性。 受影响的区域将自动从写入区域断开连接，并标记为脱机。 [Cosmos DB SDK](sql-api-sdk-dotnet.md) 会将读取调用重定向到首选区域列表中的下一个可用区域。 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。 处理读取区域服务中断不需要对应用程序代码进行更改。 最终，当受影响区域重新联机时，以前受影响的读取区域将自动与当前写入区域同步，并再次可用于为读取请求提供服务。 后续的读取会重定向到恢复的区域，不需更改应用程序代码。 在故障转移和重新加入以前发生故障的区域期间，Cosmos DB 会持续遵循读取一致性保证。

- 发生区域性服务中断时，单区域帐户可能会失去可用性。 建议始终对 Cosmos 帐户**至少设置两个区域**（最好是至少设置两个写入区域），以确保始终保持高可用性。

- 即使在极少数和令人遗憾事件的 Azure 区域是永久且无法恢复时不会丢失数据如果你的多区域 Cosmos 帐户配置的默认一致性级别*强*。 在写入区域永久无法恢复的情况下，对于配置了有限过期一致性的多区域 Cosmos 帐户，可能的数据丢失时间范围限于过期时间范围（*K* 或 *T*）；对于会话、一致的前缀和最终一致性级别，可能的数据丢失时间范围限于最多五秒。 

## <a name="availability-zone-support"></a>可用性区域支持

Azure Cosmos DB 是全球分布的多主数据库服务，在区域中断期间提供高可用性和复原能力。 除了跨区域复原能力，你现在可以启用**区域冗余**时选择要将与你的 Azure Cosmos 数据库相关联的区域。 

使用可用性区域支持，Azure Cosmos DB 将确保跨多个区域在区域发生故障期间提供高可用性和复原能力在给定区域内放置副本。 没有延迟并且在此配置中其他 Sla 更改。 事件的单个区域故障时，区域冗余提供完整的数据持久性的 RPO = 0 和可用性 RTO = 0。 

区域冗余很*补充功能*到[多主机复制](how-to-multi-master.md)功能。 不能依赖单独的区域冗余，以实现区域复原能力。 例如，在发生区域性中断或跨区域的低延迟访问，建议创建除了区域冗余的多个写入区域。 

在配置多区域写入 Azure Cosmos 帐户时，你可以选择加入区域冗余，无需额外付费。 否则，请参阅下面有关区域冗余支持的定价的说明。 删除区域并将其添加回与已启用的区域冗余，可以启用现有区域的 Azure Cosmos 帐户上的区域冗余。

此功能在以下 Azure 区域均提供：

* 英国南部
* 东南亚 
* East US
* 美国东部 2 
* 美国中部

> [!NOTE] 
> 为单区域 Azure Cosmos 帐户启用可用性区域会导致相当于将其他区域添加到你的帐户的费用。 有关定价的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)并[多区域 Azure Cosmos DB 中的成本](optimize-cost-regions.md)文章。 

下表总结了不同的帐户配置的高可用性功能： 

|KPI  |不使用可用性区域 (非 AZ) 的单个区域  |单个区域与可用性区域 (AZ)  |多区域写入凭借可用性区域 （AZ，2 个区域） – 大多数建议的设置 |
|---------|---------|---------|---------|
|编写的可用性 SLA     |   99.99%      |    99.99%     |  99.999%  |
|读取可用性 SLA   |   99.99%      |   99.99%      |  99.999%       |
|价格  |  单个区域计费费率 |  单个区域可用性区域计费费率 |  多区域计费费率       |
|区域故障 – 数据丢失   |  数据丢失  |   无数据丢失 |   无数据丢失  |
|区域失败-可用性 |  可用性丢失  | 无可用性丢失  |  无可用性丢失  |
|读取延迟    |  跨区域    |   跨区域   |    低  |
|写入延迟时间    |   跨区域   |  跨区域    |   低   |
|区域性服务中断时 – 数据丢失    |   数据丢失      |  数据丢失       |   数据丢失 <br/><br/> 使用有限过期一致性的多主机和多个区域组成，数据丢失时，限制为你的帐户上配置的有限过期。 <br/><br/> 通过使用多个区域配置较强的一致性，可以避免区域性服务中断期间的数据丢失。 此选项附带影响可用性和性能的权衡。      |
|区域性服务中断-可用性  |  可用性丢失       |  可用性丢失       |  无可用性丢失  |
|Throughput    |  X RU/s 预配的吞吐量      |  X RU/s 预配的吞吐量       |  2 倍的 RU/s 预配的吞吐量 <br/><br/> 此配置模式需要两次量进行比较时到单个区域使用可用性区域因为有两个区域的吞吐量。   |

> [!NOTE] 
> 若要启用的可用性区域支持，Azure Cosmos DB 帐户必须具有多主/多区域写入已启用。 

将区域添加到新的或现有 Azure Cosmos 帐户时，可以启用区域冗余。 目前，只能启用区域冗余通过使用 Azure 门户、 PowerShell 和 Azure 资源管理器模板。 若要启用你的 Azure Cosmos 帐户上的区域冗余，应设置`isZoneRedundant`标记，用于`true`的特定位置。 可以设置此标志中的位置属性。 例如，以下 powershell 代码段，"东南亚"区域的区域冗余：

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

可以通过使用 Azure 门户创建 Azure Cosmos 帐户时启用可用性区域。 当创建一个帐户时，请确保启用**异地冗余**，**多区域写入**，并选择支持可用性区域的位置的区域： 

![启用使用 Azure 门户的可用性区域](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>生成高可用性应用程序

- 为确保较高的写入和读取可用性，请将 Cosmos 帐户配置为跨越多个写入区域中的至少两个区域。 对于读取和写入，此配置都可提供由 SLA 作为保障的最高可用性、最低延迟和最佳可伸缩性。 详细了解如何[将 Cosmos 帐户配置为使用多个写入区域](tutorial-global-distribution-sql-api.md)。

- 对于配置为使用单个写入区域的多区域 Cosmos 帐户，请[使用 Azure CLI 或 Azure 门户中启用自动故障转移](how-to-manage-database-account.md#automatic-failover)。 启用自动故障转移后，每当发生区域性灾难时，Cosmos DB 都会自动故障转移你的帐户。  

- 即使 Cosmos 帐户具有高可用性，应用程序也不一定能够正常保持高可用性。 若要测试应用程序的端到端高可用性，请在应用程序测试或灾难恢复 (DR) 演练过程中，定期[使用 Azure CLI 或 Azure 门户调用手动故障转移](how-to-manage-database-account.md#manual-failover)。

- 在全球分布式的数据库环境中，区域范围中断的情况下一致性级别和数据持续性之间没有直接关系。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为恢复点目标 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 和 RTO，请参阅[一致性级别和数据持续性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>后续步骤

接下来可以阅读以下文章：

* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [全局缩放预配的吞吐量](scaling-throughput.md)
* [全球分布 - 揭秘](global-dist-under-the-hood.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [如何将 Cosmos 帐户配置为使用多个写入区域](how-to-multi-master.md)
