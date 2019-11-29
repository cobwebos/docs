---
title: Cosmos DB 的 Azure 流分析输出
description: 本文介绍如何使用 Azure 流分析保存 Azure Cosmos DB 的输出以进行 JSON 输出，从而实现对非结构化 JSON 数据进行数据存档和低延迟查询。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: aa4ac011a7b6258958ac1ac176fd63b18a4ef856
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560185"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 流分析输出  
流分析可以针对 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 进行 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 本文档介绍有关实现此配置的一些最佳做法。

不熟悉 Cosmos DB 的人员可以查看 [Azure Cosmos DB 的学习路径](https://azure.microsoft.com/documentation/learning-paths/documentdb/)开始上手。 

> [!Note]
> 目前，Azure 流分析仅支持使用 **SQL API** 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>作为输出目标的 Cosmos DB 基础知识
流分析中的 Azure Cosmos DB 输出允许将流处理结果作为 JSON 输出写入到 Cosmos DB 容器中。 流分析不会在数据库中创建容器，而是需要事先创建容器。 这是为了使 Cosmos DB 容器的计费成本受您控制，因此您可以使用[Cosmos DB api](https://msdn.microsoft.com/library/azure/dn781481.aspx)直接调整容器的性能、一致性和容量。

> [!Note]
> 必须从 Azure Cosmos DB 防火墙中将 0.0.0.0 添加到允许的 IP 列表。

下面详细介绍了某些 Cosmos DB 容器选项。

## <a name="tune-consistency-availability-and-latency"></a>调整一致性、可用性和延迟
为了满足您的应用程序要求，Azure Cosmos DB 允许您优化数据库和容器，并在一致性、可用性、延迟和吞吐量之间做出权衡。 根据方案针对读写延迟需要什么级别的读取一致性，可以在数据库帐户上选择一致性级别。 可以通过在容器上增加请求单位（ru）来提高吞吐量。 此外，默认情况下，Azure Cosmos DB 对容器中的每个 CRUD 操作启用同步索引。 这是另一个有用选项，可控制 Azure Cosmos DB 中的读/写性能。 有关详细信息，请参阅[更改数据库和查询的一致性级别](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
流分析与 Azure Cosmos DB 的集成使你可以基于给定的文档 ID 列插入或更新容器中的记录。 这也称为 *Upsert*。

流分析使用乐观 Upsert 方法，即仅当由于文档 ID 冲突而插入失败时才进行更新。 由于兼容性级别为1.0，此更新以修补程序的形式执行，因此它启用对文档的部分更新（即添加新属性或替换现有属性）以增量方式执行。 但是，JSON 文档中数组属性的值的更改会导致整个数组被覆盖，即不会合并数组。 对于1.2，会修改 upsert 行为，以插入或替换文档。 下面的兼容级别1.2 部分对此进行了进一步说明。

如果传入的 JSON 文档具有现有 ID 字段，则该字段将自动用作 Cosmos DB 中的“文档 ID”列，并且任何后续写入都将按此处理，从而导致出现以下情况之一：
- 唯一ID 导致插入
- 重复的 ID 和设置为“ID”的“文档 ID”导致 upsert
- 在第一个文档之后，重复的 ID 和未设置的“文档 ID”导致错误

如果要保存<i>所有</i>文档（包括具有重复 ID 的文档），请在查询中重命名 ID 字段（使用 AS 关键字），并让 Cosmos DB 创建 ID 字段或将 ID 替换为其他列的值（使用 AS 关键字或使用“文档 ID”设置）。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的数据分区
建议使用 Azure Cosmos DB [unlimited](../cosmos-db/partition-data.md) 容器将数据分区，因为 Azure Cosmos DB 可根据工作负荷自动缩放分区。 写入到无限制的容器时，流分析会使用先前查询步骤或输入分区方案中一样多的并行写入器。
> [!NOTE]
> 目前，Azure 流分析仅支持在顶层具有分区键的无限制容器。 例如，支持 `/region`。 不支持嵌套分区键（例如 `/region/name`）。 

根据所选的分区键，你可能会收到此_警告_：

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

请务必选择包含多个非重复值的分区键属性，并使你可以在这些值之间平均分配工作负荷。 作为分区的自然项目，涉及相同分区键的请求受到单个分区的最大吞吐量的限制。 此外，属于相同分区键的文档的存储大小限制为 10 GB。 理想的分区键可以作为筛选器频繁出现在查询中，并具有足够的基数，以确保解决方案可缩放。

分区键也是 DocumentDB 的存储过程和触发器中的事务的边界。 应选择分区键，以便在事务中一起出现的文档共享相同的分区键值。 [Cosmos DB 中的分区](../cosmos-db/partitioning-overview.md)一文提供了有关选择分区键的详细信息。

对于固定 Azure Cosmos DB 容器，流分析使其在填满后无法扩展或缩小。 这些集合的大小上限为 10 GB，吞吐量上限为 10,000 RU/秒。  若要将数据从固定的容器迁移到无限制容器（例如，吞吐量至少为 1,000 RU/秒，且具有分区键），则需使用[数据迁移工具](../cosmos-db/import-data.md)或[更改源库](../cosmos-db/change-feed.md)。

写入多个固定容器的功能已被弃用，不建议在向外扩展流分析作业。

## <a name="improved-throughput-with-compatibility-level-12"></a>兼容性级别1.2 提高了吞吐量
由于兼容性级别为1.2，流分析支持本机集成，以大容量写入 Cosmos DB。 这使得能够有效地将 Cosmos DB 写入到最大化吞吐量并有效处理限制请求。 由于 upsert 的行为差异，改进的书写机制在新的兼容级别下可用。  在1.2 之前，upsert 行为是插入或合并文档。 对于1.2，会修改 upsert 行为，以插入或替换文档。

在1.2 之前，使用自定义存储过程将每个分区键的文档大容量 upsert 到 Cosmos DB，其中批作为事务写入。 即使单个记录到达暂时性错误（限制），也必须重试整个批处理。 这种情况下，具有合理限制的情况相对较慢。 以下比较说明了此类作业在1.2 中的行为方式。

下面的示例显示了两个相同的流分析作业，它们从相同的事件中心输入中读取。 这两个流分析作业均使用传递查询进行[完全分区](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)，并写入到相同的 CosmosDB 容器。 左侧的指标来自配置为兼容级别为1.0 的作业，而右侧的指标配置为1.2。 Cosmos DB 容器的分区键是来自输入事件的唯一 GUID。

![流分析指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

事件中心内的传入事件率大于 Cosmos DB 容器（20K ru）被配置为进气的2倍，因此 Cosmos DB 中应有限制。 不过，使用1.2 的作业一直以更高的吞吐量（输出事件/分钟）编写，并且具有较低的平均 SU 百分比利用率。 在您的环境中，这种差异将取决于多种因素，例如选择事件格式、输入事件/消息大小、分区键、查询等。

![cosmos db 指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用1.2，流分析更智能地利用了 Cosmos DB 中的可用吞吐量的100%，resubmissions 限制/速率限制极少。 这为在容器中运行的查询提供了更好的体验。 如果需要尝试使用 Cosmos DB 作为用于1k 到10k 消息/秒的接收器来进行扩展的方式，请参阅下面的[azure 示例项目](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)。
请注意，Cosmos DB 输出吞吐量与1.0 和1.1 完全相同。 由于1.2 当前不是默认值，因此可以通过使用门户或使用[create job REST API 调用](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)来设置流分析作业的[兼容性级别](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)。 *强烈建议*在 COSMOS DB 的 ASA 中使用兼容性级别1.2。



## <a name="cosmos-db-settings-for-json-output"></a>JSON 输出的 Cosmos DB 设置

创建 Cosmos DB 作为流分析中的输出时，会生成如下所示的信息提示。 本部分提供属性定义的说明。

![documentdb 流分析输出屏幕](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|字段           | 描述|
|-------------   | -------------|
|输出别名    | 用于在 ASA 查询中引用此输出的别名。|
|Subscription    | 选择 Azure 订阅。|
|帐户 ID      | Azure Cosmos DB 帐户的名称或终结点 URI。|
|帐户密钥     | Azure Cosmos DB 帐户的共享访问密钥。|
|数据库        | Azure Cosmos DB 数据库名称。|
|容器名称 | 要使用的容器名称。 `MyContainer` 是一个示例有效的输入-一个名为 `MyContainer` 的容器必须存在。  |
|文档 ID     | 可选。 输出事件中用作唯一键的列名称，插入或更新操作必须基于该键。 如果留空，则插入所有事件，但不使用更新选项。|

配置 Cosmos DB 输出后，可以在查询中将其用作[INTO 语句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)的目标。 使用 Cosmos DB 输出时，[需要显式设置分区键](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks)。 输出记录必须包含一个区分大小写的列，该列在 Cosmos DB 中的分区键后命名。 若要实现更大的并行化，语句可能需要使用同一列的[PARTITION by 子句](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)。

**示例查询**：

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>错误处理和重试

如果发生暂时性故障，服务不可用或阻止在将事件发送到 Cosmos DB 时，流分析会无限期重试以成功完成此操作。 不过，有些失败不会进行重试，如下所示：

- 未经授权（Http 错误代码401）
- NotFound （Http 错误代码404）
- 禁止（Http 错误代码403）
- BadRequest （Http 错误代码400）
