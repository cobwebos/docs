---
title: 在 Azure 流分析中使用查询并行化和缩放功能
description: 本文介绍如何通过配置输入分区、优化查询定义和设置作业流单元来缩放流分析作业。
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: d1afb6037b5fc290de93faba405982ebd1fb68ea
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254334"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>利用 Azure 流分析中的查询并行化
本文说明了如何利用 Azure 流分析中的并行化。 了解如何通过配置输入分区和调整分析查询定义来缩放流分析作业。
作为先决条件，建议先熟悉[了解并调整流式处理单位](stream-analytics-streaming-unit-consumption.md)中所述的流式处理单位的概念。

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>流分析作业的组成部分有哪些？
流分析作业定义包括输入、查询和输出。 输入是作业读取数据流的地方。 查询是用于转换数据输入流的一种方式，而输出则是作业将作业结果发送到的地方。

若要对数据进行流式处理，作业需要至少一个输入源。 可将数据流输入源存储在 Azure 事件中心或 Azure Blob 存储中。 有关详细信息，请参阅 [Azure 流分析简介](stream-analytics-introduction.md)和[开始使用 Azure 流分析](stream-analytics-real-time-fraud-detection.md)。

## <a name="partitions-in-sources-and-sinks"></a>源和接收器中的分区
通过缩放流分析作业，可利用输入或输出中的分区。 利用分区，可根据分区键将数据分为多个子集。 使用数据（例如流分析作业）的进程可以并行利用和写入不同的分区，从而增加吞吐量。 

### <a name="inputs"></a>输入
所有 Azure 流分析输入都可以利用分区：
-   EventHub（需要使用 PARTITION BY 关键字显式设置分区键）
-   IoT 中心（需要使用 PARTITION BY 关键字显式设置分区键）
-   Blob 存储

### <a name="outputs"></a>Outputs

处理流分析时，可利用输出中的分区：
-   Azure Data Lake 存储
-   Azure Functions
-   Azure 表
-   Blob 存储（可显式设置分区键）
-   Cosmos DB（需显式设置分区键）
-   事件中心（需显式设置分区键）
-   IoT 中心（需显式设置分区键）
-   服务总线
- 使用可选分区的 SQL 和 SQL 数据仓库：请在[“输出到 Azure SQL 数据库”页](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf)中查看详细信息。

Power BI 不支持分区。 但仍可对输入进行分区，如[本节](#multi-step-query-with-different-partition-by-values)中所述 

若要深入了解分区，请参阅以下文章：

* [事件中心功能概述](../event-hubs/event-hubs-features.md#partitions)
* [Data partitioning](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)（数据分区）


## <a name="embarrassingly-parallel-jobs"></a>易并行作业
易并行作业是 Azure 流分析中最具可缩放性的方案。 它将查询的一个实例的输入的一个分区连接到输出的一个分区。 实现此并行需满足以下要求：

1. 如果查询逻辑取决于同一个查询实例正在处理的相同键，则必须确保事件转到输入的同一个分区。 对于事件中心或 IoT 中心，这意味着事件数据必须已设置 **PartitionKey** 值。 或者，可以使用已分区的发件人。 对于 Blob 存储，这意味着事件将发送到相同的分区文件夹。 如果查询逻辑不需要由同一个查询实例处理相同的键，则可忽略此要求。 举例来说，简单的选择项目筛选器查询就体现了此逻辑。  

2. 在输入端布置数据后，务必确保查询已进行分区。 这需要在所有步骤中使用 PARTITION BY。 允许采用多个步骤，但必须使用相同的键对其进行分区。 在兼容级别1.0 和1.1 下，必须将分区键设置为**PartitionId** ，以便使作业完全并行。 对于 compatility 级别为1.2 和更高的作业，可以在输入设置中将自定义列指定为分区键，并且即使没有 PARTITION BY 子句，也会自动 paralellized 作业。 对于事件中心输出，必须将属性 "分区键列" 设置为使用 "PartitionId"。

3. 大多数输出都可以利用分区，但如果使用不支持分区的输出类型，作业将不会实现完全并行。 有关详细信息，请参阅[输出部分](#outputs)。

4. 输入分区数必须等于输出分区数。 Blob 存储输出可支持分区，并继承上游查询的分区方案。 指定 Blob 存储的分区键时，数据将按每个输入分区进行分区，因此结果仍然是并行的。 下面是允许完全并行作业的分区值示例：

   * 8 个事件中心输入分区和 8 个事件中心输出分区
   * 8 个事件中心输入分区和 Blob 存储输出
   * 8 个事件中心输入分区和 Blob 存储输出由具有任意基数的自定义字段进行分区
   * 8 个 Blob 存储输入分区和 Blob 存储输出
   * 8 个 Blob 存储输入分区和 8 个事件中心输出分区

以下各节将介绍一些易并行的示例方案。

### <a name="simple-query"></a>简单查询

* 输入：具有 8 个分区的事件中心
* 输出：具有8个分区的事件中心（"分区键列" 必须设置为使用 "PartitionId"）

查询：

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

此查询是一个简单的筛选器。 因此，无需担心对发送到事件中心的输入进行分区。 请注意，在1.2 之前兼容级别的作业必须包含**PARTITION By PartitionId**子句，才能满足前面 #2 的要求。 对于输出，需要在作业中配置事件中心输出，将分区键设置为“PartitionId”。 最后一项检查是确保输入分区数等于输出分区数。

### <a name="query-with-a-grouping-key"></a>带分组键的查询

* 输入：具有 8 个分区的事件中心
* 输出：Blob 存储

查询：

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

此查询具有分组键。 因此，组合在一起的事件必须被发送到相同事件中心分区。 由于在此示例中我们按 TollBoothID 进行分组，因此应确保在将事件发送到事件中心时，将 TollBoothID 用作分区键。 然后在 ASA 中，可以使用 PARTITION BY PartitionId 继承此分区方案并启用完全并行化。 由于输出是 Blob 存储，因此如要求 #4 所述，无需担心如何配置分区键值。

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>不易并行的示例方案

上一节介绍了一些易并行方案。 本节将介绍不满足实现易并行所需全部要求的方案。 

### <a name="mismatched-partition-count"></a>分区计数不匹配
* 输入：具有 8 个分区的事件中心
* 输出：具有 32 个分区的事件中心

在此情况下，查询是什么并不重要。 如果输入分区数不等于输出分区数，则拓扑不易并行。但是，我们仍可获得一些层级或并行度。

### <a name="query-using-non-partitioned-output"></a>使用非分区输出进行查询
* 输入：具有 8 个分区的事件中心
* 输出： Power BI

Power BI 输出当前不支持分区。 因此，此方案不易并行。

### <a name="multi-step-query-with-different-partition-by-values"></a>使用不同 PARTITION BY 值进行多步骤查询
* 输入：具有 8 个分区的事件中心
* 输出：具有 8 个分区的事件中心

查询：

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

正如所见，第二步使用 **TollBoothId** 作为分区键。 此步骤与第一步不同，因此需要执行随机选择。 

前述示例介绍了一些符合（或不符合）易并行拓扑的流分析作业。 如果符合易并行拓扑，则有可能达到最大规模。 对于不适合其中一个配置文件的作业，未来更新中将提供缩放指南。 现在，请使用以下各节中的常规指南。

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>兼容级别 1.2-具有不同的分区 BY 值的多步骤查询 
* 输入：具有 8 个分区的事件中心
* 输出：具有8个分区的事件中心（"分区键列" 必须设置为使用 "TollBoothId"）

查询：

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

默认情况下，兼容性级别1.2 启用并行查询执行。 例如，只要将 "TollBoothId" 列设置为输入分区键，就会 parttioned 上一部分的查询。 不需要 PARTITION BY ParttionId 子句。

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>计算作业的最大流式处理单位数
流分析作业所能使用的流式处理单位总数取决于为作业定义的查询中的步骤数，以及每一步的分区数。

### <a name="steps-in-a-query"></a>查询中的步骤
查询可以有一个或多个步骤。 每一步都是由 WITH 关键字定义的子查询。 位于 WITH 关键字外的查询（仅 1 个查询）也计为一步，例如以下查询中的 SELECT 语句：

查询：

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

此查询有两步。

> [!NOTE]
> 本文后面部分将详细介绍此查询。
>  

### <a name="partition-a-step"></a>对步骤进行分区
对步骤进行分区需要下列条件：

* 输入源必须进行分区。 
* 查询的 **SELECT** 语句必须从分区的输入源读取。
* 步骤中的查询必须有 PARTITION BY 关键字。

对查询进行分区后，需在独立的分区组中处理和聚合输入事件，并为每个组生成输出事件。 如果需要对聚合进行组合，则必须创建另一个不分区的步骤来进行聚合。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>计算作业的最大流式处理单位数
所有未分区的步骤总共可将每个流分析作业增加到最多 6 个流式处理单元 (SU)。 除此之外，可以在分区步骤中为每个分区添加 6 个 SU。
下表是一些示例。

| 查询                                               | 作业的最大 SU 数 |
| --------------------------------------------------- | ------------------- |
| <ul><li>该查询包含一个步骤。</li><li>该步骤未分区。</li></ul> | 6 |
| <ul><li>输入数据流被分为 16 个分区。</li><li>该查询包含一个步骤。</li><li>该步骤已分区。</li></ul> | 96（6 * 16 个分区） |
| <ul><li>该查询包含两个步骤。</li><li>这两个步骤都未分区。</li></ul> | 6 |
| <ul><li>输入数据流被分为 3 个分区。</li><li>该查询包含两个步骤。 输入步骤进行了分区，第二个步骤未分区。</li><li>SELECT 语句从已分区输入中读取数据。</li></ul> | 24（18 个用于已分区步骤 + 6 个用于未分区步骤） |

### <a name="examples-of-scaling"></a>缩放示例

以下查询计算 3 分钟时段内通过收费站（共 3 个收费亭）的车辆数。 此查询可增加到 6 个 SU。

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

若要对查询使用更多 SU，必须对输入数据流和查询进行分区。 由于数据流分区设置为 3，因此可将以下经修改的查询增加到 18 个 SU：

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

对查询进行分区后，会在独立的分区组中处理和聚合输入事件。 此外，还会为每个组生成输出事件。 在输入数据流中，当“分组方式”字段不是分区键时，执行分区可能会导致某些意外的结果。 例如，在前面的查询中，TollBoothId 字段不是 Input1 的分区键。 因此，可以将 TollBooth #1 中的数据分布到多个分区。

流分析会分开处理每个 Input1 分区。 因此，将在相同的翻转窗口为同一收费亭创建多个关于车辆数的记录。 如果不能更改输入分区键，则可通过添加不分区步骤以跨分区聚合值来解决此问题，如下例所示：

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

此查询可增加到 24 个 SU。

> [!NOTE]
> 若要联接两个流，请务必按创建联接所用列的分区键对流进行分区。 还需确保两个流中的分区数相同。
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>大规模实现更高的吞吐量

[易并行](#embarrassingly-parallel-jobs)作业是必需的，但不能满足规模较高的吞吐量。 每个存储系统及其对应的流分析输出都具有如何实现最佳写入吞吐量的不同之处。 对于任何大规模方案，都有一些挑战可以通过使用正确的配置来解决。 本部分讨论几个常见输出的配置，并提供有关每秒的1K

以下观测值使用带无状态（passthrough）查询的流分析作业，这是一个基本的 JavaScript UDF，可写入事件中心、Azure SQL DB 或 Cosmos DB。

#### <a name="event-hub"></a>事件中心

|引入速率（每秒事件数） | 流式处理单位数 | 输出资源  |
|--------|---------|---------|
| 1K     |    1    |  2 TU   |
| 5K     |    6    |  6 TU   |
| 10K    |    12   |  10 TU  |

[事件中心](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs)解决方案在流式处理单元（SU）和吞吐量方面呈线性伸缩，使其成为分析和流式传输流分析中的数据的最有效的方法。 作业可扩展到 192 SU，这大致会转换为每日最多 200 MB/秒或19000000000000个事件。

#### <a name="azure-sql"></a>Azure SQL
|引入速率（每秒事件数） | 流式处理单位数 | 输出资源  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[AZURE SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql)支持并行编写，称为继承分区，但默认情况下未启用。 但是，启用继承分区以及完全并行查询可能不足以实现更高的吞吐量。 SQL write 吞吐量明显依赖于 SQL Azure 数据库配置和表架构。 [SQL 输出性能](./stream-analytics-sql-output-perf.md)一文提供了有关可最大程度地提高写入吞吐量的参数的详细信息。 如 azure[流分析输出到 AZURE SQL 数据库](./stream-analytics-sql-output-perf.md#azure-stream-analytics)一文中所述，此解决方案不能线性缩放为超过8个分区的完全并行管道，并且可能[需要在 SQL](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)输出之前重新分区（请参阅）。 需要高级 Sku 来维持高 IO 率，以及每隔几分钟进行日志备份的开销。

#### <a name="cosmos-db"></a>Cosmos DB
|引入速率（每秒事件数） | 流式处理单位数 | 输出资源  |
|-------|-------|---------|
|  1K   |  3    | 20K RU  |
|  5K   |  24   | 60K RU  |
|  10K  |  48   | 12万条 RU |

流分析[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)输出已更新为使用[兼容性级别 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)下的本机集成。 兼容性级别1.2 实现明显更高的吞吐量，与1.1 （这是新作业的默认兼容级别）相比，它可以减少 RU 消耗。 解决方案使用在/deviceId 上分区的 CosmosDB 容器，而解决方案的其余部分是相同的配置。

所有[大规模流式传输 azure 示例](https://github.com/Azure-Samples/streaming-at-scale)使用通过将测试客户端作为输入模拟来进行传输的事件中心。 每个输入事件都是一个 1KB JSON 文档，可轻松地将配置的引入速率转换为吞吐率（1MB/秒、5MB/s 和 10MB/s）。 事件会模拟发送到最多1K 个设备的以下 JSON 数据（以缩短形式提供）的 IoT 设备：

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> 由于解决方案中使用的各种组件，这些配置可能会发生更改。 若要获得更准确的估计值，请根据你的方案自定义示例。

### <a name="identifying-bottlenecks"></a>确定瓶颈

使用 Azure 流分析作业中的 "指标" 窗格识别管道中的瓶颈。 检查吞吐量和["水印延迟"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)的**输入/输出事件**，或查看**囤积的事件**，查看作业是否与输入速率保持一致。 对于事件中心指标，请查找**限制的请求**并相应地调整阈值单位。 对于 Cosmos DB 度量值，查看 "吞吐量" 下**每个分区键范围内使用的最大 RU 数/秒**，以确保对分区键范围进行统一使用。 对于 Azure SQL DB，监视**日志 IO**和**CPU**。

## <a name="get-help"></a>获取帮助

如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

