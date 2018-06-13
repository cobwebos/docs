---
title: 在 Azure 流分析中使用查询并行化和缩放功能
description: 本文介绍如何通过配置输入分区、优化查询定义和设置作业流单元来缩放流分析作业。
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 44a7c0721d8a0683162d2219bff0e4a4ecb117e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777621"
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
-   CosmosDB（需显式设置分区键）
-   EventHub（需显式设置分区键）
-   IoT 中心（需显式设置分区键）
-   服务总线

PowerBI、SQL 和 SQL 数据仓库的输出不支持分区。 但仍可对输入进行分区，如[本节](#multi-step-query-with-different-partition-by-values)中所述 

若要深入了解分区，请参阅以下文章：

* [事件中心功能概述](../event-hubs/event-hubs-features.md#partitions)
* [Data partitioning](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)（数据分区）


## <a name="embarrassingly-parallel-jobs"></a>易并行作业
易并行作业是 Azure 流分析中最具可缩放性的方案。 它将查询的一个实例的输入的一个分区连接到输出的一个分区。 实现此并行需满足以下要求：

1. 如果查询逻辑取决于同一个查询实例正在处理的相同键，则必须确保事件转到输入的同一个分区。 对于事件中心或 IoT 中心，这意味着事件数据必须已设置 **PartitionKey** 值。 或者，可以使用已分区的发件人。 对于 Blob 存储，这意味着事件将发送到相同的分区文件夹。 如果查询逻辑不需要由同一个查询实例处理相同的键，则可忽略此要求。 举例来说，简单的选择项目筛选器查询就体现了此逻辑。  

2. 在输入端布置数据后，务必确保查询已进行分区。 这需要在所有步骤中使用 PARTITION BY。 允许采用多个步骤，但必须使用相同的键对其进行分区。 目前，必须将分区键设置为“PartitionId”才能实现完全并行作业。  

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
* 输出：具有 8 个分区的事件中心

查询：

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

此查询是一个简单的筛选器。 因此，无需担心对发送到事件中心的输入进行分区。 请注意，该查询包含 PARTITION BY PartitionId，因此其满足上述要求 #2。 对于输出，需要在作业中配置事件中心输出，将分区键设置为“PartitionId”。 最后一项检查是确保输入分区数等于输出分区数。

### <a name="query-with-a-grouping-key"></a>带分组键的查询

* 输入：具有 8 个分区的事件中心
* 输出：Blob 存储

查询：

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

此查询具有分组键。 因此，组合在一起的事件必须被发送到相同事件中心分区。 由于在此示例中我们按 TollBoothID 进行分组，因此应确保在将事件发送到事件中心时，将 TollBoothID 用作分区键。 然后在 ASA 中，可以使用 PARTITION BY PartitionId 继承此分区方案并启用完全并行化。 由于输出是 Blob 存储，因此如要求 #4 所述，无需担心如何配置分区键值。

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>不易并行的示例方案

上一节介绍了一些易并行方案。 本节将介绍不满足实现易并行所需全部要求的方案。 

### <a name="mismatched-partition-count"></a>分区计数不匹配
* 输入：具有 8 个分区的事件中心
* 输出：具有 32 个分区的事件中心

在此情况下，查询是什么并不重要。 如果输入分区数不等于输出分区数，则拓扑不易并行。但是，我们仍可获得一些层级或并行度。

### <a name="query-using-non-partitioned-output"></a>使用非分区输出进行查询
* 输入：具有 8 个分区的事件中心
* 输出：PowerBI

PowerBI 输出当前不支持分区。 因此，此方案不易并行。

### <a name="multi-step-query-with-different-partition-by-values"></a>使用不同 PARTITION BY 值进行多步骤查询
* 输入：具有 8 个分区的事件中心
* 输出：具有 8 个分区的事件中心

查询：

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

正如所见，第二步使用 **TollBoothId** 作为分区键。 此步骤与第一步不同，因此需要执行随机选择。 

前述示例介绍了一些符合（或不符合）易并行拓扑的流分析作业。 如果符合易并行拓扑，则有可能达到最大规模。 对于不适合其中一个配置文件的作业，未来更新中将提供缩放指南。 现在，请使用以下各节中的常规指南。

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>计算作业的最大流式处理单位数
流分析作业所能使用的流式处理单位总数取决于为作业定义的查询中的步骤数，以及每一步的分区数。

### <a name="steps-in-a-query"></a>查询中的步骤
查询可以有一个或多个步骤。 每一步都是由 WITH 关键字定义的子查询。 位于 WITH 关键字外的查询（仅 1 个查询）也计为一步，例如以下查询中的 SELECT 语句：

查询：

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

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

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

若要对查询使用更多 SU，必须对输入数据流和查询进行分区。 由于数据流分区设置为 3，因此可将以下经修改的查询增加到 18 个 SU：

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

对查询进行分区后，会在独立的分区组中处理和聚合输入事件。 此外，还会为每个组生成输出事件。 在输入数据流中，当“分组方式”字段不是分区键时，执行分区可能会导致某些意外的结果。 例如，在前面的查询中，TollBoothId 字段不是 Input1 的分区键。 因此，可以将 TollBooth #1 中的数据分布到多个分区。

流分析会分开处理每个 Input1 分区。 因此，将在相同的翻转窗口为同一收费亭创建多个关于车辆数的记录。 如果不能更改输入分区键，则可通过添加不分区步骤以跨分区聚合值来解决此问题，如下例所示：

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

此查询可增加到 24 个 SU。

> [!NOTE]
> 若要联接两个流，请务必按创建联接所用列的分区键对流进行分区。 还需确保两个流中的分区数相同。
> 
> 





## <a name="get-help"></a>获取帮助
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

