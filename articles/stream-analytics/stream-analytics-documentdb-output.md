---
title: Azure Cosmos DB 的 Azure 流分析输出
description: 本文介绍如何使用 Azure 流分析保存 Azure Cosmos DB 的输出以进行 JSON 输出，从而实现对非结构化 JSON 数据进行数据存档和低延迟查询。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986982"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 流分析输出  
Azure 流分析可将[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)用于 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 本文档介绍有关实现此配置的一些最佳做法。

如果你不熟悉 Azure Cosmos DB，请参阅[Azure Cosmos DB 文档](https://docs.microsoft.com/azure/cosmos-db/)。 

> [!Note]
> 目前，流分析仅支持通过*SQL API*连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果将流分析指向通过其他 Api 创建 Azure Cosmos DB 帐户，则数据可能无法正确存储。 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>作为输出目标的 Azure Cosmos DB 基础知识
流分析中的 Azure Cosmos DB 输出允许将流处理结果作为 JSON 输出写入到 Azure Cosmos DB 容器中。 

流分析不会在数据库中创建容器。 而是需要事先创建它们。 然后，你可以控制 Azure Cosmos DB 容器的计费费用。 还可以通过使用[Azure Cosmos DB api](https://msdn.microsoft.com/library/azure/dn781481.aspx)，直接调整容器的性能、一致性和容量。

> [!Note]
> 必须从 Azure Cosmos DB 防火墙中将 0.0.0.0 添加到允许的 IP 列表。

以下部分详细介绍了 Azure Cosmos DB 的一些容器选项。

## <a name="tuning-consistency-availability-and-latency"></a>优化一致性、可用性和延迟
为了满足您的应用程序要求，Azure Cosmos DB 允许您优化数据库和容器，并在一致性、可用性、延迟和吞吐量之间做出权衡。 

根据方案针对读写延迟需要什么级别的读取一致性，可以在数据库帐户上选择一致性级别。 可以通过在容器上增加请求单位（ru）来提高吞吐量。 

此外，默认情况下，Azure Cosmos DB 对容器中的每个 CRUD 操作启用同步索引。 这是另一个有用的选项，用来控制 Azure Cosmos DB 中的读/写性能。 

有关详细信息，请参阅[更改数据库和查询的一致性级别](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
流分析与 Azure Cosmos DB 的集成使你可以基于给定的**文档 ID**列插入或更新容器中的记录。 这也称为*upsert*。

流分析使用开放式 upsert 方法。 仅当插入操作因文档 ID 冲突而失败时，才会进行更新。 

由于兼容性级别为1.0，流分析会将此更新作为一种修补操作来执行，因此可对文档进行部分更新。 流分析添加新属性或增量替换现有属性。 但是，JSON 文档中数组属性的值发生更改将导致覆盖整个数组。 也就是说，不会合并数组。 

对于1.2，会修改 upsert 行为，以插入或替换文档。 有关兼容级别1.2 的后面部分进一步介绍了此行为。

如果传入的 JSON 文档具有现有的 ID 字段，则该字段将自动用作 Azure Cosmos DB 中的 "**文档 ID** " 列。 所有后续写入操作的处理方式如下：

- 唯一 Id 会导致插入。
- 设置为**ID**的重复 Id 和**文档 id**将导致 upsert。
- 在第一个文档后，未设置重复的 Id 和**文档 ID**导致错误。

如果要保存包含重复 ID 的*所有*文档，请重命名查询中的 ID 字段（通过使用**AS**关键字）。 让 Azure Cosmos DB 创建 ID 字段或将 ID 替换为其他列的值（通过使用**AS**关键字或使用**文档 ID**设置）。

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的数据分区
Azure Cosmos DB 会根据工作负荷自动缩放分区。 因此，建议[不要将](../cosmos-db/partition-data.md)容器作为分区数据的方法。 当流分析写入到无限制的容器时，它将使用任意数量的并行编写器作为上一个查询步骤或输入分区方案。

> [!NOTE]
> Azure 流分析仅支持在顶层具有分区键的无限制容器。 例如，支持 `/region`。 嵌套分区键（例如 `/region/name`）不受支持。 

根据所选的分区键，你可能会收到此_警告_：

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

选择包含多个非重复值的分区键属性，并使你可以在这些值之间平均分配工作负荷，这一点很重要。 作为分区的自然项目，涉及相同分区键的请求受到单个分区的最大吞吐量的限制。 

属于同一分区键的文档的存储大小限制为 10 GB。 理想的分区键是指经常作为查询中的筛选器，并且具有足够的基数以确保解决方案是可缩放的。

分区键也是 Azure Cosmos DB 的存储过程和触发器中的事务的边界。 应选择分区键，以便在事务中一起出现的文档共享相同的分区键值。 [Azure Cosmos DB 中的分区](../cosmos-db/partitioning-overview.md)一文提供了有关选择分区键的详细信息。

对于固定 Azure Cosmos DB 容器，流分析在填满后就不能增加或缩减。 它们的吞吐量上限为 10 GB，10000 RU/秒。 若要将数据从固定容器迁移到无限制容器（例如，至少具有 1000 RU/s 和分区键的容器），请使用[数据迁移工具](../cosmos-db/import-data.md)或[更改源库](../cosmos-db/change-feed.md)。

不推荐写入多个固定容器的功能。 不建议将其用于扩展流分析作业。

## <a name="improved-throughput-with-compatibility-level-12"></a>兼容性级别1.2 提高了吞吐量
由于兼容性级别为1.2，流分析支持本机集成，以大容量写入 Azure Cosmos DB。 通过这种集成，可以有效地 Azure Cosmos DB 编写，同时最大限度地提高吞吐量并有效处理限制请求。 

增强的写入机制在新的兼容级别下提供，因为 upsert 行为存在差异。 在1.2 之前的级别，upsert 行为是插入或合并文档。 对于1.2，会修改 upsert 行为，以插入或替换文档。

对于1.2 之前的级别，流分析使用自定义存储过程将每个分区键的文档大容量 upsert 到 Azure Cosmos DB 中。 在那里，批处理被编写为事务。 即使单个记录到达暂时性错误（限制），也必须重试整个批处理。 这使得具有合理限制的情况相对较慢。

下面的示例显示了两个相同的流分析作业，它们从同一 Azure 事件中心输入中读取。 这两个流分析作业均使用传递查询进行[完全分区](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)，并写入相同的 Azure Cosmos DB 容器。 左侧的指标来自配置为兼容级别1.0 的作业。 右侧的度量值配置了1.2。 Azure Cosmos DB 容器的分区键是来自输入事件的唯一 GUID。

![流分析指标的比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

事件中心内的传入事件速率比配置为采用 Azure Cosmos DB 容器（20000 ru）多两倍，因此应在 Azure Cosmos DB 中使用限制。 不过，使用1.2 的作业以更高的吞吐量（每分钟的输出事件）以较低的速度进行编写，且平均的 SU 百分比利用率较低。 在您的环境中，这种差异将取决于多个因素。 这些因素包括：选择事件格式、输入事件/消息大小、分区键和查询。

![Azure Cosmos DB 度量值比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用1.2，流分析更智能地利用了 Azure Cosmos DB 的可用吞吐量的100%，resubmissions 限制或速率限制极少。 这为在容器中运行的查询提供了更好的体验。 若要查看流分析如何使用 Azure Cosmos DB 作为每秒1000到10000消息的接收器进行扩展，请尝试[此 Azure 示例项目](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)。

Azure Cosmos DB 输出的吞吐量与1.0 和1.1 完全相同。 *强烈建议*在流分析中将兼容性级别1.2 与 Azure Cosmos DB 结合使用。

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON 输出的 Azure Cosmos DB 设置

使用 Azure Cosmos DB 作为流分析中的输出时，会生成以下提示信息。

![Azure Cosmos DB 输出流的信息字段](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|字段           | Description|
|-------------   | -------------|
|输出别名    | 用于在流分析查询中引用此输出的别名。|
|订阅    | Azure 订阅。|
|帐户 ID      | Azure Cosmos DB 帐户的名称或终结点 URI。|
|帐户密钥     | Azure Cosmos DB 帐户的共享访问密钥。|
|数据库        | Azure Cosmos DB 数据库名称。|
|容器名称 | 容器名称，如 `MyContainer`。 名为 `MyContainer` 的容器必须存在。  |
|文档 ID     | 可选。 输出事件中用作唯一键的列名称，插入或更新操作必须基于该键。 如果将其保留为空，则将插入所有事件，无更新选项。|

配置 Azure Cosmos DB 输出后，可以在查询中将其用作[INTO 语句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)的目标。 使用 Azure Cosmos DB 的输出时，[需要显式设置分区键](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks)。 

输出记录必须包含一个区分大小写的列，该列在 Azure Cosmos DB 中的分区键后命名。 若要实现更大的并行化，语句可能需要使用同一列的[PARTITION by 子句](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)。

下面是一个示例查询：

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>错误处理和重试

如果在流分析将事件发送到 Azure Cosmos DB 时出现暂时性故障、服务不可用或限制，则流分析会无限期重试以成功完成操作。 但它不会尝试重试，原因如下：

- 未经授权（HTTP 错误代码401）
- NotFound （HTTP 错误代码404）
- 禁止（HTTP 错误代码403）
- BadRequest （HTTP 错误代码400）
