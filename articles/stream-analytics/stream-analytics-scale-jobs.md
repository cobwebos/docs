---
title: Azure 流分析作业的纵向扩展和横向扩展
description: 本文介绍如何通过分布输入数据、优化查询以及设置作业流单元来缩放流分析作业。
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 1438ffa34652268572fe89dc63583cc25607d722
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>扩展 Azure 流分析作业以增加吞吐量
本文介绍如何优化流分析查询，增加流分析作业的吞吐量。 可以使用以下指南来扩展作业，以便处理较高负载并充分利用更多的系统资源（如更多带宽、更多 CPU 资源、更多内存）。
作为先决条件，你可能需要阅读以下文章：
-   [了解和调整流式处理单元](stream-analytics-streaming-unit-consumption.md)
-   [创建可并行的作业](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>案例 1 – 在各个输入分区中，查询本质上是完全可并行的
如果在各个输入分区中，查询本质上是完全可并行的，则可以按照以下步骤操作：
1.  通过使用 PARTITION BY 关键字来创作查询使之易并行。 请参阅[此页](stream-analytics-parallelization.md)易并行作业部分中的更多详细信息。
2.  根据查询中使用的输出类型，某些输出可能是不可并行的，或者需要进一步配置来实现易并行。 例如，SQL、SQL DW 和 PowerBI 输出都是不可并行的。 请始终先合并输出，然后再将其发送到输出接收器。 Blob、表、ADLS、服务总线和 Azure Function 会自动并行化。 CosmosDB 和事件中心都需要设置 PartitionKey 配置来匹配 PARTITION BY 字段（通常是 PartitionId）。 对于事件中心，还要格外注意匹配所有输入和所有输出的分区数量，以避免分区之间的交叉。 
3.  使用 6 SU（即单个计算节点的全部容量）来运行查询，以度量最大可实现的吞吐量，如果你使用的是 GROUP BY，则度量作业能处理的组数（基数）。 达到系统资源限制的作业，一般症状将如下所示。
    - SU 利用率指标超过 80%。 该指示内存使用率较高。 [此处](stream-analytics-streaming-unit-consumption.md)描述了导致此指标增加的因素。 
    -   输出时间戳滞后于时钟时间。 根据查询逻辑，输出时间戳可能与时钟时间之间存在一个逻辑偏差。 但是，它们应该以大致相同的速度增进。 如果输出时间戳进一步滞后，则指示系统工作时间过长。 它可能是由于下游输出接收器限制，或高 CPU 利用率所致。 我们目前没有提供 CPU 利用率指标，因此很难区分两者。
        - 如果该问题是由接收器限制导致，则可能需要增加输出分区数（以及输入分区，以此使作业保持完全可并行化），或增加接收器的资源量（例如，CosmosDB 的请求单位数）。
    - 在作业关系图中，每个输出都有一个分区积压工作 (backlog) 事件指标。 如果积压工作事件指标持续增加，则同样表示系统资源受到约束（由于输出接收器限制或高 CPU）。
4.  在确定 6 SU 作业可以达到的上限之后，可以在添加更多的 SU 时线性推断出作业的处理容量，前提是你没有任何使某些分区“紧迫”的数据倾斜。
>[!Note]
> 选择适当数量的流式处理单元：由于流分析为每个添加的 6 SU 创建一个处理节点，因此，最好将节点数作为输入分区数的除数，以便分区可以均匀分布在各节点上。
> 例如，你已经度量出 6 SU 作业可以实现 4 MB/秒的处理速率，且输入分区计数为 4。 可以选择使用 12 SU 来运行作业，以达到大约 8 MB/秒的处理速率，或使用 24 SU 来实现 16 MB/秒的处理速率。 然后，你可以决定何时增加作业的 SU 数量以及增加至多少，以作为输入速率的一个函数。


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>案例 2 - 如果查询不是易并行。
如果查询不是易并行，则可以执行以下步骤。
1.  首先使用不带 PARTITION BY 的查询来避免分区复杂性，然后使用 6 SU 运行查询，以度量最大负载，如[案例 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) 中所示。
2.  如果能在吞吐量方面达到预期负载，则操作完成。 或者，你可以选择度量在 3 SU 和 1 SU 上运行的相同作业，以找到适用于你方案的 SU 的最小数目。
3.  如果不能实现所需的吞吐量，请尝试尽可能地将查询分解为多个步骤，如果没有多个步骤，则在查询中为每个步骤分配最多6 个 SU。 例如，如果你有 3 个步骤，则在“规模”选项中分配 18 个 SU。
4.  在运行此类作业时，流分析会将各步骤分配到自己包含专用 6 SU 资源的节点上。 
5.  如果仍未实现负载目标，可以尝试使用 PARTITION BY，从更接近输入的步骤开始。 对于不可自然分区的 GROUP BY 运算符，可以使用本地/全局聚合模式来执行分区的 GROUP BY，然后执行非分区 GROUP BY。 例如，如果想要计算每 3 分钟有多少车辆通过每个收费站，以及超出 6 SU 能够处理范围的数据量。

查询：

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

在上述查询中，计算每个分区每个收费站的车辆数，然后将所有分区中的计数相加。

分区后，对于该步骤的每个分区，最多分配 6 个 SU，每个分区最多有 6 个 SU，因此，每个分区都可以置于其自己的处理节点上。

> [!Note]
> 如果无法对查询进行分区，则在多步骤查询中添加额外的 SU 可能无法始终有效地提高吞吐量。 获得性能的一种方法是，使用本地/全局聚合模式来减少初始步骤的数量，如前面的步骤 5 中所述。

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>案例 3 - 在作业中运行大量的独立查询。
对于某些 ISV 用例，如果在单个作业中处理来自多个租户的数据更具经济效益，则可为每个租户使用单独的输入和输出，你可能最终会在单个作业中运行相当多的（例如 20 个）独立查询。 假设条件是每个此类子查询的负载都相对较小。 在这种情况下，可以按照以下步骤操作。
1.  在这种情况下，请勿在查询中使用 PARTITION BY
2.  如果使用事件中心，则将输入分区计数减少到可能的最低值 2。
3.  使用 6 SU 运行查询。 通过每个子查询的预期负载，尽可能多地添加此类子查询，直到作业达到系统资源上限。 有关发生这种情况时的症状，请参阅[案例 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)。
4.  一旦达到以上度量的子查询上限，可开始向新作业添加子查询。 作为独立查询数量的函数运行的作业数应是标准线性的，前提是你没有任何负载偏移。 然后，可以预测你需要多少个 6 SU 作业，作为你想要提供的租户数的函数运行。
5.  将引用数据联接与此类查询结合使用时，应在使用相同引用数据进行联接之前将输入合并在一起，然后再根据需要拆分事件。 否则，每个引用数据联接会在内存中保留一份引用数据，可能导致不必要的内存使用。

> [!Note] 
> 每个作业中要放置多少租户？
> 此查询模式通常具有大量子查询，并导致非常大且复杂的拓扑。 作业控制器可能无法处理此类大型拓扑。 根据经验，1 SU 作业应保持在 40 个租户以下，3 SU 和 6 SU 作业则为 60 个租户。 如果即将超出控制器的容量，则不会成功启动作业。


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>下面举例说明了基于规模的流分析吞吐量
为了帮助你了解如何缩放流分析作业，我们基于 Raspberry Pi 设备中的输入进行了一项试验。 通过此试验，可以发现输入对多个流式处理单元和分区的吞吐量的影响。

在此方案中，设备将传感器数据（客户端）发送到事件中心。 流分析处理数据，并将警报或统计信息作为输出发送到另一个事件中心。 

客户端以 JSON 格式发送传感器数据。 数据输出也采用 JSON 格式。 数据如下所示：

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

以下查询用于在关灯时发送警报：

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>衡量吞吐量

在这种情况下，吞吐量是指由流分析在固定时间内处理的输入数据量。 （测量时长为 10 分钟。）为了使输入数据达到最佳的处理吞吐量，我们对数据流输入和查询进行了分区。 还在查询中添加了 COUNT()，以便测量已处理的输入事件数。 为了确保作业不会单纯地等待输入事件的到来，输入事件中心的每个分区均预先加载了约 300 MB 的输入数据。

下表显示结果，可以发现流式处理单元数和事件中心的相应分区数均有所增加。  

<table border="1">
<tr><th>输入分区数</th><th>输出分区数</th><th>流式处理单位数</th><th>持续的吞吐量
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/秒</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/秒</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/秒</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/秒</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/秒</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/秒</td>
</tr>
</table>

另外，下图直观地显示了 SU 和吞吐量之间的关系。

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

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

