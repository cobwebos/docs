---
title: Azure Cosmos DB 中的高可用性
description: 本文介绍 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 23273084826775b47170753dff3e5cf5ed8ae45f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063563"
---
# <a name="high-availability-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 实现高可用性

Azure Cosmos DB 以透明方式在与 Cosmos 帐户关联的所有 Azure 区域之间复制数据。 Cosmos DB 对数据采用多层冗余，如下图所示：

![物理分区](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos 容器内的数据是[水平分区](partitioning-overview.md)。

- 在每个区域中，每个分区受副本集的保护，该副本集中的大多数副本将复制并以持久方式提交所有写入内容。 副本分布在最多 10 到 20 个容错域中。

- 将复制所有区域中的每个分区。 每个区域包含某个 Cosmos 容器的所有数据分区，可接受写入并为读取提供服务。  

如果 Cosmos 帐户分布*N* Azure 区域，将有至少*N* x 的所有数据的 4 个副本。 除了提供低延迟数据访问和跨与 Cosmos 帐户关联的区域缩放读/写吞吐量，具有更多的区域 (更高版本*N*) 进一步提高了可用性。  

## <a name="slas-for-availability"></a>可用性 SLA

作为全局分布式数据库，Cosmos DB 提供综合性的 SLA，涵盖了吞吐量、99% 时间内的延迟、一致性和高可用性。 下表显示了 Cosmos DB 针对单区域和多区域帐户提供的高可用性保证。 以实现高可用性，始终将 Cosmos 帐户配置创建多个写入区域。

|操作类型  | 单区域 |多区域（单区域写入）|多区域（多区域写入） |
|---------|---------|---------|-------|
|写入    | 99.99    |99.99   |99.999|
|读取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 在实践中，对于有限的过期、 会话、 一致前缀和最终一致性模型的实际写入可用性是明显高于已发布的 Sla。 所有一致性级别的实际读取可用性明显高于发布的 SLA。

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>使用 Cosmos DB 发生区域故障时的高可用性

区域性中断时不是通常情况下，并且 Azure Cosmos DB 可确保您的数据库始终是高度可用。 以下详细信息在出现故障，具体取决于你的 Cosmos 帐户配置期间捕获 Cosmos DB 行为：

- 使用 Cosmos DB 时，在客户端确认写入操作之前，数据将由接受写入操作的区域中的副本仲裁持久提交。

- 配置有多个写入区域的多区域帐户对于写入和读取都将具有高可用性。 区域性故障转移可在瞬间完成，不需要在应用程序中进行任何更改。

- **与单写入区域 （写入区域中断） 的多区域帐户：** 在发生写入区域服务中断期间，这些帐户将保持很高的读取可用性。 但是，对于写入您必须 **"启用自动故障转移"** 上你 Cosmos 帐户添加到故障转移到另一个区域，受影响的区域。 故障转移将按指定的区域优先级进行。 当受影响的区域回到联机状态时，在服务中断期间受影响的写入区域中有非复制的数据可通过[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)。 应用程序可以读取冲突源、 解决基于特定于应用程序逻辑中，冲突，然后将更新后的数据写回到根据 Cosmos 容器。 以前受影响的写入区域恢复后，它将自动用作读取区域。 可以调用手动故障转移，并将受影响的区域配置为写入区域。 再次执行手动故障转移操作通过使用[Azure CLI 或 Azure 门户](how-to-manage-database-account.md#manual-failover)。 在手动故障转移之前、期间或之后，**没有数据丢失或可用性丢失**。 应用程序仍然高度可用。 

- **与单写入区域 （读取的区域中断） 的多区域帐户：** 在发生读取区域服务中断期间，这些帐户将保持很高的读写可用性。 受影响的区域将自动从写入区域断开连接，并标记为脱机。 [Cosmos DB Sdk](sql-api-sdk-dotnet.md)读取到首选的区域列表中的下一个可用区域的调用将重定向。 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。 处理读取区域服务中断不需要对应用程序代码进行更改。 最终，当受影响区域重新联机时，以前受影响的读取区域将自动与当前写入区域同步，并再次可用于为读取请求提供服务。 后续的读取会重定向到恢复的区域，不需更改应用程序代码。 在故障转移和重新加入的以前失败的区域，期间读取的一致性保证继续遵守此 Cosmos DB。

- 发生区域性服务中断时，单区域帐户可能会失去可用性。 我们始终建议设置**至少两个区域**（最好是至少两个写入区域） 与 Cosmos 帐户以确保在所有时间的高可用性。

- 甚至在出现罕见且令人遗憾的 Azure 区域是永久且无法恢复时不会丢失数据如果你的多区域 Cosmos 帐户配置的默认一致性级别*强*。 潜在数据丢失的时段发生永久不可恢复的写入区域，配置具有有限过期一致性的多区域 Cosmos 帐户时仅限于停滞窗口 (*K*或*T*);为会话、 一致前缀和最终一致性级别，潜在数据丢失窗口被限制为最多五秒。 

## <a name="availability-zone-support"></a>可用性区域支持

Azure Cosmos DB 是全球分布的多主数据库服务，在区域中断期间提供高可用性和复原能力。 除了跨区域复原能力，你现在可以启用**区域冗余**时选择要将与你的 Azure Cosmos 数据库相关联的区域。 

使用可用性区域支持，Azure Cosmos DB 将确保跨多个区域在区域发生故障期间提供高可用性和复原能力在给定区域内放置副本。 没有延迟并且在此配置中其他 Sla 更改。 事件的单个区域故障时，区域冗余提供完整的数据持久性的 RPO = 0 和可用性 RTO = 0。 

区域冗余很*补充功能*到[多主机复制](how-to-multi-master.md)功能。 不能依赖单独的区域冗余，以实现区域复原能力。 例如，在发生区域性中断或跨区域的低延迟访问，建议创建除了区域冗余的多个写入区域。 

在配置多区域写入 Azure Cosmos 帐户时，你可以选择加入区域冗余，无需额外付费。 否则，请参阅下面有关区域冗余支持的定价的说明。 删除区域并将其添加回与已启用的区域冗余，可以启用现有区域的 Azure Cosmos 帐户上的区域冗余。

此功能在以下 Azure 区域均提供：

* 英国南部
* 东南亚 

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

将区域添加到新的或现有 Azure Cosmos 帐户时，可以启用区域冗余。 目前，仅可以通过使用 PowerShell 或 Azure 资源管理器模板启用区域冗余。 若要启用你的 Azure Cosmos 帐户上的区域冗余，应设置`isZoneRedundant`标记，用于`true`的特定位置。 可以设置此标志中的位置属性。 例如，以下 powershell 代码段，"东南亚"区域的区域冗余：

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

## <a name="building-highly-available-applications"></a>生成高可用性应用程序

- 为确保较高的写入和读取可用性，请将 Cosmos 帐户配置为跨越多个写入区域中的至少两个区域。 此配置将提供最高可用性、 最低的延迟，并获得最佳的可伸缩性，同时读取和写入由 Sla 提供支持。 详细了解如何[将 Cosmos 帐户配置为使用多个写入区域](tutorial-global-distribution-sql-api.md)。

- 对于配置为使用单个写入区域的多区域 Cosmos 帐户，请[使用 Azure CLI 或 Azure 门户中启用自动故障转移](how-to-manage-database-account.md#automatic-failover)。 启用自动故障转移后，每当发生区域性灾难时，Cosmos DB 都会自动故障转移你的帐户。  

- 即使 Cosmos 帐户具有高可用性，应用程序也不一定能够正常保持高可用性。 若要测试你的应用程序的端到端高可用性，定期调用[通过使用 Azure CLI 或 Azure 门户的手动故障转移](how-to-manage-database-account.md#manual-failover)，作为一部分应用程序测试或灾难恢复 (DR) 演练。

- 在全球分布式的数据库环境中，区域范围中断的情况下一致性级别和数据持续性之间没有直接关系。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为恢复点目标 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 和 RTO，请参阅[一致性级别和数据持续性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>后续步骤

接下来可以阅读以下文章：

* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [全局缩放预配的吞吐量](scaling-throughput.md)
* [全球分布 - 揭秘](global-dist-under-the-hood.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [如何配置 Cosmos 帐户具有多个写入区域](how-to-multi-master.md)
