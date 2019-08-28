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
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062012"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB 的 Azure 流分析输出  
流分析可以针对 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 进行 JSON 输出，从而支持对非结构化 JSON 数据进行数据存档和低延迟查询。 本文档介绍有关实现此配置的一些最佳做法。

不熟悉 Cosmos DB 的人员可以查看 [Azure Cosmos DB 的学习路径](https://azure.microsoft.com/documentation/learning-paths/documentdb/)开始上手。 

> [!Note]
> 目前，Azure 流分析仅支持使用 **SQL API** 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>作为输出目标的 Cosmos DB 基础知识
使用流分析中的 Azure Cosmos DB 输出可以将流处理结果作为 JSON 输出写入到 Cosmos DB 容器中。 流分析不会在数据库中创建容器，而需要你预先创建这些容器。 这是为了让你控制 Cosmos DB 容器的计费成本，以及让你可以直接使用 [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 调整容器的性能、一致性和容量。

> [!Note]
> 必须从 Azure Cosmos DB 防火墙中将 0.0.0.0 添加到允许的 IP 列表。

下面详细介绍一些 Cosmos DB 容器选项。

## <a name="tune-consistency-availability-and-latency"></a>调整一致性、可用性和延迟
为了满足您的应用程序要求, Azure Cosmos DB 允许您优化数据库和容器, 并在一致性、可用性、延迟和吞吐量之间做出权衡。 根据方案针对读写延迟需要什么级别的读取一致性，可以在数据库帐户上选择一致性级别。 可以通过纵向扩展容器中的请求单位 (RU) 来提高吞吐量。 另外，默认情况下，Azure Cosmos DB 对容器的每个 CRUD 操作启用了同步索引。 这是另一个有用选项，可控制 Azure Cosmos DB 中的读/写性能。 有关详细信息，请参阅[更改数据库和查询的一致性级别](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>从流分析进行 Upsert 操作
由于流分析与 Azure Cosmos DB 集成，因此可以在容器中基于给定的文档 ID 列插入或更新记录。 这也称为 *Upsert*。

流分析使用乐观 Upsert 方法，即仅当由于文档 ID 冲突而插入失败时才进行更新。 使用兼容性级别 1.0 时，此更新以修补程序的形式执行，因此，对文档的部分更新（即添加新属性或替换现有属性）以增量方式执行。 但是，JSON 文档中数组属性的值的更改会导致整个数组被覆盖，即不会合并数组。 在版本 1.2 中，更新插入行为已被修改，可以插入或替换文档。 下面的“兼容性级别 1.2”部分会进一步对此进行说明。

如果传入的 JSON 文档具有现有 ID 字段，则该字段将自动用作 Cosmos DB 中的“文档 ID”列，并且任何后续写入都将按此处理，从而导致出现以下情况之一：
- 唯一ID 导致插入
- 重复的 ID 和设置为“ID”的“文档 ID”导致 upsert
- 在第一个文档之后，重复的 ID 和未设置的“文档 ID”导致错误

如果要保存<i>所有</i>文档（包括具有重复 ID 的文档），请在查询中重命名 ID 字段（使用 AS 关键字），并让 Cosmos DB 创建 ID 字段或将 ID 替换为其他列的值（使用 AS 关键字或使用“文档 ID”设置）。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的数据分区
建议使用 Azure Cosmos DB [unlimited](../cosmos-db/partition-data.md) 容器将数据分区，因为 Azure Cosmos DB 可根据工作负荷自动缩放分区。 写入到无限制的容器时，流分析会使用先前查询步骤或输入分区方案中一样多的并行写入器。
> [!NOTE]
> 目前，Azure 流分析仅支持顶级分区键的无限容器。 例如，支持 `/region`。 不支持嵌套分区键（例如 `/region/name`）。 

根据所选的分区键, 你可能会收到此_警告_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

必须选择有许多不同值的分区键属性，并跨这些值均匀分配工作负荷。 就分区的自然属性来说，涉及同一分区键的请求数受到单个分区的最大吞吐量的限制。 此外, 属于相同分区键的文档的存储大小限制为 10 GB。 理想的分区键可以作为筛选器频繁出现在查询中，并具有足够的基数，以确保解决方案可缩放。

分区键也是 DocumentDB 的存储过程和触发器中的事务处理的边界。 在选择分区键时，应确保在事务处理中出现在一起的文档共享同一分区键值。 [Cosmos DB 中的分区](../cosmos-db/partitioning-overview.md)一文更详细地介绍了如何选择分区键。

对于固定的 Azure Cosmos DB 容器，在这些容器已满后，流分析不允许通过任何方式进行纵向或横向扩展。 这些集合的大小上限为 10 GB，吞吐量上限为 10,000 RU/秒。  若要将数据从固定的容器迁移到无限制容器（例如，吞吐量至少为 1,000 RU/秒，且具有分区键），则需使用[数据迁移工具](../cosmos-db/import-data.md)或[更改源库](../cosmos-db/change-feed.md)。

写入多个固定容器的功能已过时，不建议使用它来横向扩展流分析作业。

## <a name="improved-throughput-with-compatibility-level-12"></a>使用兼容性级别 1.2 改进了吞吐量
借助兼容性级别 1.2，流分析支持使用本机集成来批量写入到 Cosmos DB。 这可以有效地对 Cosmos DB 进行写入，同时可以最大程度地提高吞吐量和有效处理限制请求。 新兼容性级别改变了更新插入行为，提供一种改进的写入机制。  在版本 1.2 之前，更新插入行为是插入或合并文档。 在版本 1.2 中，更新插入行为已被修改，可以插入或替换文档。

在版本 1.2 之前，需使用自定义的存储过程将文档按分区键批量更新插入到 Cosmos DB 中，其中的批作为事务写入。 即使只有一条记录遇到暂时性错误（限制），也必须重试整个批。 因此，即使受到合理限制的方案的运行速度也会变得相对缓慢。 以下比较结果显示了此类作业在版本 1.2 中的行为。

以下示例显示了从同一事件中心输入读取数据的两个相同流分析作业。 这两个流分析作业已使用直通查询[完全分区](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)，并写入到相同的 CosmosDB 容器。 左侧的指标来自配置了兼容性级别 1.0 的作业，右侧的指标来自配置了版本 1.2 的作业。 Cosmos DB 容器的分区键是来自输入事件的唯一 GUID。

![流分析指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

事件中心的传入事件速率是配置引入的 Cosmos DB 容器 (20K RU) 的 2 倍，因此，预期 Cosmos DB 中会进行限制。 但是，使用版本 1.2 的作业一贯以更高的吞吐量（输出事件数/分钟）写入，并且其平均 SU% 利用率更低。 在你的环境中，这种差异取决于其他几项因素，例如选择的事件格式、输入事件/消息大小、分区键、查询等。

![Cosmos DB 指标比较](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用版本 1.2 时，流分析可以更智能地利用 Cosmos DB 中 100% 的可用吞吐量，并且在发生节流限制/速率限制的情况下，只需重新提交极少的次数。 对于其他工作负荷（例如，同时针对容器运行的查询），这可以提供更好的体验。 如果需要体验如何使用 Cosmos DB 来横向扩展 ASA，使其作为一个接收器来每秒处理 1K 到 10K 的消息，请使用此 [Azure 示例项目](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)。
请注意，使用版本 1.0 和 1.1 的 Cosmos DB 的输出吞吐量完全相同。 由于 1.2 目前并非默认版本，你可以使用门户或使用[创建作业 REST API 调用](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)来为流分析作业[设置兼容性级别](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)。 *强烈建议*在 COSMOS DB 的 ASA 中使用兼容性级别1.2。



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
|容器名称 | 要使用的容器名称。 `MyContainer` 是示例性的有效输入 - 必须存在一个名为 `MyContainer` 的容器。  |
|文档 ID     | 可选。 输出事件中用作唯一键的列名称，插入或更新操作必须基于该键。 如果留空，则插入所有事件，但不使用更新选项。|

## <a name="error-handling-and-retries"></a>错误处理和重试

如果发生暂时性故障, 服务不可用或阻止在将事件发送到 Cosmos DB 时, 流分析会无限期重试以成功完成此操作。 不过，有些失败不会进行重试，如下所示：

- 未经授权 (Http 错误代码 401)
- NotFound (Http 错误代码 404)
- 禁止 (Http 错误代码 403)
- BadRequest (Http 错误代码 400)
