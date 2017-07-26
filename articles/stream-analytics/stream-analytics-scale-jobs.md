---
title: "扩展流分析作业以增加吞吐量 | Microsoft 文档"
description: "了解如何通过配置输入分区、细化查询定义和设置作业流式处理单位来扩展流分析作业。"
keywords: "数据流式处理, 流数据处理, 优化分析"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9a2b16fc6dff687e2a1fa03c9194d50711f53476
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017


---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>扩展 Azure 流分析作业，以增加流数据处理吞吐量
本文介绍如何优化流分析查询，增加流分析作业的吞吐量。 了解如何通过配置输入分区、优化分析查询定义，以及计算和设置作业流式处理单位 (SU) 来缩放流分析作业。 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>流分析作业的组成部分有哪些？
流分析作业定义包括输入、查询和输出。 输入是作业读取数据流的地方。 查询是用于转换数据输入流的一种方式，而输出则是作业将作业结果发送到的地方。  

若要对数据进行流式处理，作业需要至少一个输入源。 可将数据流输入源存储在 Azure 事件中心或 Azure Blob 存储中。 有关详细信息，请参阅 [Azure 流分析简介](stream-analytics-introduction.md)和[开始使用 Azure 流分析](stream-analytics-real-time-fraud-detection.md)。

## <a name="partitions-in-event-hubs-and-azure-storage"></a>事件中心和 Azure 存储中的分区
通过缩放流分析作业，可利用输入或输出中的分区。 利用分区，可根据分区键将数据分为多个子集。 使用数据（例如流分析作业）的进程可以并行利用和写入不同的分区，从而增加吞吐量。 处理流分析时，可利用事件中心和 Blob 存储中的分区。 

若要深入了解分区，请参阅以下文章：

* [事件中心功能概述](../event-hubs/event-hubs-features.md#partitions)
* [Data partitioning](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)（数据分区）


## <a name="streaming-units-sus"></a>流式处理单位 (SU)
流式处理单位 (SU) 代表执行 Azure 流分析作业所需的资源和计算能力。 在已经对 CPU、内存以及读取和写入速率进行测量的情况下，可以使用 SU 来描述相对的事件处理能力。 每个 SU 大致相当于 1MB/秒的吞吐量。 

选择特定作业所需的 SU 数目时，需根据输入的分区配置以及为作业定义的查询来决定。 可为作业选择的最大数目为 SU 配额。 默认情况下，每个 Azure 订阅的配额为最多 50 个 SU，这适用于特定区域的所有分析作业。 若要增加订阅的 SU 数，使其超过此配额，请联系 [Microsoft 支持部门](http://support.microsoft.com)。 每个作业的 SU 有效值以 1、3、6 开始，往上再按 6 递增。

## <a name="embarrassingly-parallel-jobs"></a>易并行作业
易并行作业是 Azure 流分析中最具可缩放性的方案。 它将查询的一个实例的输入的一个分区连接到输出的一个分区。 实现此并行需满足以下要求：

1. 如果查询逻辑取决于同一个查询实例正在处理的相同键，则必须确保事件转到输入的同一个分区。 对于事件中心，这意味着事件数据必须具有 PartitionKey 值集。 或者，可以使用已分区的发件人。 对于 Blob 存储，这意味着事件将发送到相同的分区文件夹。 如果查询逻辑不需要由同一个查询实例处理相同的键，则可忽略此要求。 举例来说，简单的选择项目筛选器查询就体现了此逻辑。  

2. 在输入端布置数据后，务必确保查询已进行分区。 这需要在所有步骤中使用 Partition By。 允许采用多个步骤，但必须使用相同的键对其进行分区。 目前，必须将分区键设置为“PartitionId”才能实现完全并行作业。  

3. 当前仅事件中心和 Blob 存储支持已分区的输出。 对于事件中心输出，必须将分区键配置为“PartitionId”。 对于 Blob 存储输出，不必执行任何操作。  

4. 输入分区数必须等于输出分区数。 Blob 存储输出当前不支持分区。 不过没关系，因为它会继承上游查询的分区方案。 下面是允许完全并行作业的分区值示例：  

   * 8 个事件中心输入分区和 8 个事件中心输出分区
   * 8 个事件中心输入分区和 Blob 存储输出  
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

此查询是一个简单的筛选器。 因此，无需担心对发送到事件中心的输入进行分区。 请注意，该查询包含“按 PartitionId 分区”，因此其满足上述要求 #2。 对于输出，需要在作业中配置事件中心输出，将分区键设置为“PartitionId”。 最后一项检查是确保输入分区数等于输出分区数。

### <a name="query-with-a-grouping-key"></a>带分组键的查询

* 输入：具有 8 个分区的事件中心
* 输出：Blob 存储

查询：

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

此查询具有分组键。 因此，相同的键需要由同一个查询实例进行处理，这意味着必须以分区的方式将事件发送到事件中心。 但是，应使用哪个键？ PartitionId 是作业逻辑概念。 我们真正关心的键是 TollBoothId，因此事件数据的 PartitionKey 值应为 TollBoothId。 为此，请在查询中将“Partition By”设置为“PartitionId”。 由于输出是 Blob 存储，因此如要求 #4 所述，无需担心如何配置分区键值。

### <a name="multi-step-query-with-a-grouping-key"></a>带分组键的多步骤查询
* 输入：具有 8 个分区的事件中心
* 输出：具有 8 个分区的事件中心实例

查询：

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

此查询具有分组键，因此相同的键需要由同一个查询实例进行处理。 可以使用与上一示例相同的策略。 在此情况下，查询包含多个步骤。 是否每个步骤都包含“按 PartitionId 分区”？ 是，因此该查询满足要求 #3。 对于输出，需要如上文所述，将分区键设置为“PartitionId”。 还可以看到，其分区数与输入的分区数相同。

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>不易并行的示例方案

上一节介绍了一些易并行方案。 本节将介绍不满足实现易并行所需全部要求的方案。 

### <a name="mismatched-partition-count"></a>分区计数不匹配
* 输入：具有 8 个分区的事件中心
* 输出：具有 32 个分区的事件中心

在此情况下，查询是什么并不重要。 如果输入分区数不等于输出分区数，则拓扑不易并行。

### <a name="not-using-event-hubs-or-blob-storage-as-output"></a>未将事件中心或 Blob 存储用作输出
* 输入：具有 8 个分区的事件中心
* 输出：PowerBI

PowerBI 输出当前不支持分区。 因此，此方案不易并行。

### <a name="multi-step-query-with-different-partition-by-values"></a>使用不同 Partition By 值的多步骤查询
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
* 步骤中的查询必须有 Partition By 关键字。

对查询进行分区后，需在独立的分区组中处理和聚合输入事件，并为每个组生成输出事件。 如果需要对聚合进行组合，则必须创建另一个不分区的步骤来进行聚合。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>计算作业的最大流式处理单位数
所有未分区的步骤总共可将每个流分析作业增加到最多 6 个流式处理单元 (SU)。 若要添加 SU，必须对步骤进行分区。 每个分区可以有 6 个 SU。

<table border="1">
<tr><th>查询</th><th>作业的最大 SU 数</th></td>

<tr><td>
<ul>
<li>该查询包含一个步骤。</li>
<li>该步骤未分区。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>输入数据流被分为 3 个分区。</li>
<li>该查询包含一个步骤。</li>
<li>该步骤已分区。</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>该查询包含两个步骤。</li>
<li>这两个步骤都未分区。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>输入数据流被分为 3 个分区。</li>
<li>该查询包含两个步骤。 输入步骤进行了分区，第二个步骤未分区。</li>
<li>SELECT 语句从已分区输入中读取数据<strong></strong>。</li>
</ul>
</td>
<td>24（18 个用于已分区步骤 + 6 个用于未分区步骤）</td></tr>
</table>

### <a name="examples-of-scaling"></a>缩放示例

以下查询计算 3 分钟时段内通过收费站（共 3 个收费亭）的车辆数。 此查询可增加到 6 个 SU。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

若要对查询使用更多 SU，必须对输入数据流和查询进行分区。 由于数据流分区设置为 3，因此可将以下经修改的查询增加到 18 个 SU：

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

对查询进行分区后，将在独立的分区组中处理和聚合输入事件。 此外，还会为每个组生成输出事件。 在输入数据流中，当“分组方式”字段不是分区键时，执行分区可能会导致某些意外的结果。 例如，在前面的查询中，TollBoothId 字段不是 Input1 的分区键。 因此，可以将 TollBooth #1 中的数据分布到多个分区。

流分析会分开处理每个 Input1 分区。 因此，将在相同的翻转窗口为同一收费亭创建多个关于车辆数的记录。 如果不能更改输入分区键，则可通过添加不分区步骤来解决此问题，如下例所示：

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

## <a name="configure-stream-analytics-streaming-units"></a>配置流分析流式处理单元

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在资源列表中，找到要缩放的流分析作业，然后将其打开。
3. 在作业边栏选项卡的“配置”下，单击“缩放”。

    ![Azure 门户流分析作业配置][img.stream.analytics.preview.portal.settings.scale]

4. 使用滑块设置作业的 SU。 请注意，只能设置特定的 SU。


## <a name="monitor-job-performance"></a>监视作业性能
使用 Azure 门户时，可以跟踪作业的吞吐量：

![Azure 流分析监视作业][img.stream.analytics.monitor.job]

计算工作负荷的预期吞吐量。 如果吞吐量低于预期，则可调整输入分区和查询，并为作业添加 SU。


## <a name="visualize-stream-analytics-throughput-at-scale-the-raspberry-pi-scenario"></a>大规模可视化流分析吞吐量：Raspberry Pi 方案
为了帮助你了解如何缩放流分析作业，我们基于 Raspberry Pi 设备中的输入进行了一项试验。 通过此试验，可以发现输入对多个流式处理单元和分区的吞吐量的影响。

在此方案中，设备将传感器数据（客户端）发送到事件中心。 流分析处理数据，并将警报或统计信息作为输出发送到另一个事件中心。 

客户端以 JSON 格式发送传感器数据。 数据输出也采用 JSON 格式。 数据如下所示：

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

以下查询用于在关灯时发送警报：

    SELECT AVG(lght),
     "LightOff" as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

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
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

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
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


