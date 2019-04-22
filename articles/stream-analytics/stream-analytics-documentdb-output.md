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
ms.openlocfilehash: 734cf09869e5a2df5f9a505a3cb8ccc7bc2338d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495970"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 流分析输出  
流分析可以针对 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 进行 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 本文档介绍有关实现此配置的一些最佳做法。

不熟悉 Cosmos DB 的人员可以查看 [Azure Cosmos DB 的学习路径](https://azure.microsoft.com/documentation/learning-paths/documentdb/)开始上手。 

> [!Note]
> 目前，Azure 流分析仅支持使用 **SQL API** 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>作为输出目标的 Cosmos DB 基础知识
使用流分析中的 Azure Cosmos DB 输出可以将流处理结果作为 JSON 输出写入到 Cosmos DB 集合中。 流分析不会在数据库中创建集合，而需要你预先创建这些集合。 这是为了让你控制 Cosmos DB 集合的计费成本，以便可直接使用 [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 调整集合的性能、一致性和容量。

> [!Note]
> 必须从 Azure Cosmos DB 防火墙中将 0.0.0.0 添加到允许的 IP 列表。

下面详细介绍一些 Cosmos DB 集合选项。

## <a name="tune-consistency-availability-and-latency"></a>调整一致性、可用性和延迟
为了满足应用程序的需求，Azure Cosmos DB 允许微调数据库和集合并进行一致性、 可用性和延迟之间的权衡。 根据方案针对读写延迟需要什么级别的读取一致性，可以在数据库帐户上选择一致性级别。 另外，默认情况下，Azure Cosmos DB 对集合的每个 CRUD 操作启用了同步索引。 这是另一个有用选项，可控制 Azure Cosmos DB 中的读/写性能。 有关详细信息，请参阅[更改数据库和查询的一致性级别](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
由于流分析与 Azure Cosmos DB 集成，因此可以在集合中基于给定的文档 ID 列插入或更新记录。 这也称为 *Upsert*。

流分析使用乐观 Upsert 方法，即仅当由于文档 ID 冲突而插入失败时才进行更新。 使用兼容性级别 1.0，此更新会执行修补程序，因此它可以对该文档的部分更新，它是、 添加新属性或替换现有属性以增量方式执行。 但是，JSON 文档中数组属性的值的更改会导致整个数组被覆盖，即不会合并数组。 使用 1.2，修改 upsert 行为来插入或替换文档。 这是下面的兼容性级别 1.2 部分中作了进一步介绍。

如果传入的 JSON 文档具有现有 ID 字段，则该字段将自动用作 Cosmos DB 中的“文档 ID”列，并且任何后续写入都将按此处理，从而导致出现以下情况之一：
- 唯一ID 导致插入
- 重复的 ID 和设置为“ID”的“文档 ID”导致 upsert
- 在第一个文档之后，重复的 ID 和未设置的“文档 ID”导致错误

如果要保存<i>所有</i>文档（包括具有重复 ID 的文档），请在查询中重命名 ID 字段（使用 AS 关键字），并让 Cosmos DB 创建 ID 字段或将 ID 替换为其他列的值（使用 AS 关键字或使用“文档 ID”设置）。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的数据分区
建议使用 Azure Cosmos DB [unlimited](../cosmos-db/partition-data.md) 容器将数据分区，因为 Azure Cosmos DB 可根据工作负荷自动缩放分区。 写入到无限制的容器时，流分析会使用先前查询步骤或输入分区方案中一样多的并行写入器。
> [!Note]
> 目前，Azure 流分析仅支持顶级分区键的无限集合。 例如，支持 `/region`。 不支持嵌套分区键（例如 `/region/name`）。 

对于固定的 Azure Cosmos DB 集合，在这些集合已满后，流分析不允许通过任何方式进行纵向或横向扩展。 这些集合的大小上限为 10 GB，吞吐量上限为 10,000 RU/秒。  若要将数据从固定的容器迁移到无限制容器（例如，吞吐量至少为 1,000 RU/秒，且具有分区键），则需使用[数据迁移工具](../cosmos-db/import-data.md)或[更改源库](../cosmos-db/change-feed.md)。

写入多个固定容器的做法已过时，不建议使用此方法来横向扩展流分析作业。 [Cosmos DB 中的分区和缩放](../cosmos-db/sql-api-partition-data.md)一文提供了更多详细信息。

## <a name="improved-throughput-with-compatibility-level-12"></a>改进了的吞吐量的兼容性级别 1.2
使用兼容性级别 1.2，大容量的 Stream Analytics 支持本机集成写入到 Cosmos DB。 这可以有效地对最大化吞吐量和高效地处理限制请求与 Cosmos DB 的写入。 改进了的编写机制是在由于 upsert 行为差异新兼容性级别下可用。  在 1.2 之前, upsert 行为是插入或合并文档。 使用 1.2，修改 upsert 行为来插入或替换文档。 

1.2 之前, 使用 Cosmos DB 中，一批作为一个事务的写入位置对每个分区键的批量更新插入文档的自定义存储的过程。 甚至当一条记录遇到暂时性错误 （限制），则必须重试整个批处理。 这使得即使合理限制相对较慢的方案。 以下比较显示了此类作业会随 1.2 的行为方式。

安装程序下面显示了读取同一输入 （事件中心） 的两个相同 Stream Analytics 作业。 这两个 Stream Analytics 作业[已完全分区](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)直接传递查询和写入相同的 CosmosDB 集合。 在左侧的指标是从兼容性级别 1.0 的配置作业在右侧的是使用和配置 1.2。 Cosmos DB 集合分区键是来自输入事件的唯一 guid。

![流分析指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

在事件中心的传入事件率为 2 x 不是 Cosmos DB 集合 （20k 的 ru 来讲） 配置为获取，以便限制应在 Cosmos DB 中，更高版本。 但是，编写一致地使用 1.2，作业，按更高的吞吐量 （输出事件/分钟） 并使用较低的平均 SU %利用率。 在环境中，这种差异取决于几个更多因素，例如选择的事件格式、 输入的事件/消息大小、 分区键、 查询等。

![cosmos db 指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用 1.2，Stream Analytics 是更智能中使用 100%的 Cosmos DB 中的可用吞吐量与从速率限制/限制很少重新提交。 这同时在集合上运行的查询等其他工作负载提供更好的体验。 如果您需要尝试如何 ASA 横向扩展使用 Cosmos DB 为接收器的 10 万到 1 k 消息/秒，下面是[azure 示例项目](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)，可以做到这一点。
请注意，Cosmos DB 输出吞吐量并使用 1.0 和 1.1 完全相同。 由于 1.2 当前不是默认值，你可以[设置兼容性级别](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)Stream Analytics 作业使用门户或使用[创建作业 REST API 调用](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)。 它具有*强烈建议*使用 Cosmos DB 用于在 ASA 中的兼容性级别 1.2。 



## <a name="cosmos-db-settings-for-json-output"></a>JSON 输出的 Cosmos DB 设置

创建 Cosmos DB 作为流分析中的输出时，会生成如下所示的信息提示。 本部分提供属性定义的说明。

![documentdb 流分析输出屏幕](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|字段           | 描述|
|-------------   | -------------|
|输出别名    | 用于在 ASA 查询中引用此输出的别名。|
|订阅    | 选择 Azure 订阅。|
|帐户 ID      | Azure Cosmos DB 帐户的名称或终结点 URI。|
|帐户密钥     | Azure Cosmos DB 帐户的共享访问密钥。|
|数据库        | Azure Cosmos DB 数据库名称。|
|集合名称模式 | 要使用的集合的集合名称。 `MyCollection` 是有效的输入示例 - 必须存在一个名为 `MyCollection` 的集合。  |
|文档 ID     | 可选。 输出事件中用作唯一键的列名称，插入或更新操作必须基于该键。 如果留空，则插入所有事件，但不使用更新选项。|
