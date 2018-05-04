---
title: 了解和调整 Azure 流分析中的流单元
description: 本文介绍 Azure 流分析中的流单元设置和影响性能的其他因素。
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: c96e9825cddd0b60e67cd4752fab8f440ceaae76
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="understand-and-adjust-streaming-units"></a>了解和调整流式处理单元

Azure 流分析将运行作业的性能“权重”聚合到流式处理单位 (SU) 中。 SU 表示执行作业所消耗的计算资源。 在已经对 CPU、内存以及读取和写入速率进行测量的情况下，可以使用 SU 来描述相对的事件处理能力。 此容量使你能够专注于查询逻辑，并且无需管理及时运行流分析作业所需的硬件。

为了实现低延迟流式处理，Azure 流分析作业将执行内存中的所有处理。 内存不足时，流式处理作业会失败。 因此，对于生产作业，请务必监视流式处理作业的资源使用情况，并确保分配有足够的资源来保持作业的全天候运行。

指标是一个范围从 0% 到 100% 的百分数。 对于占用最小内存的流式处理作业，SU 利用率指标通常介于 10% 到 20%。 最好保持低于 80% 的指标，以应对偶发的峰值。 Microsoft 建议针对 SU 利用率指标达到 80% 设置警报，以防止资源耗尽。 有关详细信息，请参阅[教程：为 Azure 流分析作业设置警报](stream-analytics-set-up-alerts.md)。

## <a name="configure-stream-analytics-streaming-units-sus"></a>配置流分析流式处理单元 (SU)
1. 登录到 [Azure 门户](http://portal.azure.com/)

2. 在资源列表中，找到要缩放的流分析作业，然后将其打开。 

3. 在作业页中的“配置”标题下，选择“缩放”。 

    ![Azure 门户流分析作业配置][img.stream.analytics.preview.portal.settings.scale]
    
4. 使用滑块设置作业的 SU。 请注意，只能设置特定的 SU。 

## <a name="monitor-job-performance"></a>监视作业性能
使用 Azure 门户时，可以跟踪作业的吞吐量：

![Azure 流分析监视作业][img.stream.analytics.monitor.job]

计算工作负荷的预期吞吐量。 如果吞吐量低于预期，则可调整输入分区和查询，并为作业添加 SU。

## <a name="how-many-sus-are-required-for-a-job"></a>一个作业需要多少 SU？

为特定作业选择所需的 SU 数量时，需要根据输入的分区配置以及在作业内定义的查询来决定。 可以使用“缩放”页设置正确的 SU 数量。 分配的 SU 数最好超过所需的数量。 流分析处理引擎会针对延迟和吞吐量进行优化，不过，代价是需要分配额外的内存。

通常情况下，最佳做法是一开始为不使用 PARTITION BY 的查询分配 6 个 SU。 然后，在传递了具有代表性的数据量并检查了 SU 利用率指标后，使用修改 SU 数量的试用和错误方法来确定最佳数量。

有关选择适当数量 SU 的详细信息，请参阅此页：[缩放 Azure 流分析作业以增加吞吐量](stream-analytics-scale-jobs.md)

> [!Note]
> 选择特定作业所需的 SU 数目时，需根据输入的分区配置以及为作业定义的查询来决定。 可为作业选择的最大数目为 SU 配额。 默认情况下，每个 Azure 订阅的配额为最多 200 个 SU，这适用于特定区域的所有分析作业。 若要增加订阅的 SU 数，使其超过此配额，请联系 [Microsoft 支持部门](http://support.microsoft.com)。 每个作业的 SU 有效值以 1、3、6 开始，往上再按 6 递增。

## <a name="factors-that-increase-su-utilization"></a>SU 利用率提高的因素 

时态（时间导向）的查询元素是流分析提供的有状态运算符的核心集。 流分析通过管理内存消耗量、为复原创建检查点，并在服务升级期间恢复状态，代表用户在内部管理这些操作的状态。 尽管流分析能够全面管理状态，但用户还是应该考虑一些最佳做法建议。

## <a name="stateful-query-logic-in-temporal-elements"></a>时态元素中的有状态查询逻辑
Azure 流分析作业的独有功能之一是执行有状态的处理，如开窗聚合、临时联接和临时分析函数。 其中的每个运算符都会保存状态信息。 这些查询元素的最大窗口大小为 7 天。 

多个流分析查询元素中都出现了时态窗口的概念：
1. 开窗聚合：翻转窗口、跳跃窗口和滑动窗口 GROUP BY

2. 时态联接：JOIN with DATEDIFF 函数

3. 时态分析函数：ISFIRST、LAST 和 LAG with LIMIT DURATION

以下因素影响流分析作业使用的内存（流单元指标部分）：

## <a name="windowed-aggregates"></a>开窗聚合
开窗聚合的消耗内存（状态大小）并不始终与窗口大小成正比。 消耗内存与数据基数或者每个时间窗口中的组数成正比。


例如，在以下查询中，与 `clusterid` 关联的数字就是查询的基数。 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

若要解决前面查询中由高基数导致的问题，可以将事件发送到通过依据 `clusterid` 分区的事件中心，并通过允许系统使用 **PARTITION BY** 分别处理每个输入分区来横向扩展查询，如以下示例所示：

   ```sql
   SELECT count(*) 
   FROM PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

将查询分区后，它会分散到多个节点中。 因此，可以通过减少依据运算符分组的基数来减少传入每个节点的 `clusterid` 值数。 

事件中心分区应根据分组键进行分区，以避免减少步骤的需要。 有关详细信息，请参阅[事件中心概述](../event-hubs/event-hubs-what-is-event-hubs.md)。 

## <a name="temporal-joins"></a>时态联接
时态联接的消耗内存（状态大小）与联接的时态调整空间中的事件数量成正比，即事件输入速率与调整空间大小的乘积。 换而言之，联接消耗的内存与 DateDiff 时间范围乘以平均事件速率的结果成正比。

联接中的不匹配事件数会影响查询的内存利用率。 以下查询将查找产生点击量的广告印象：

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

在本示例中，有可能显示了很多广告，但很少有人点击它们，并且需要保留该时间范围内的所有事件。 内存消耗量与时间范围大小和事件发生速率成比例。 

若要修正此问题，请将事件发送到依据联接键（在此情况下为 ID）分区的事件中心，并通过允许系统使用 PARTITION BY 分别处理每个输入分区来横向扩展查询，如下所示：

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

将查询分区后，它会分散到多个节点中。 因此，可以通过减小保留在联接窗口中状态的大小来减少传入每个节点的事件数。 

## <a name="temporal-analytic-functions"></a>时态分析函数
时态分析函数的消耗内存（状态大小）与事件速率和持续时间的乘积成正比。 分析函数消耗的内存与窗口大小不成正比，而是与每个时间窗口中的分区计数成正比。

修正的方法类似于临时联接。 你可以使用 PARTITION BY 来横向扩展查询。 

## <a name="out-of-order-buffer"></a>无序缓冲区 
在“事件排序配置”窗格中，用户可以配置无序的缓冲区大小。 可以使用缓冲区来保留窗口持续时间内的输入，并对其进行重新排序。 缓冲区的大小与事件输入速率和无序窗口大小的乘积成正比。 默认窗口大小为 0。 

若要修正失序缓冲区溢出，请使用 **PARTITION BY** 横向扩展查询。 将查询分区后，它会分散到多个节点中。 因此，可以通过减少每个重新排序缓冲区中的事件数来减少传入每个节点的事件数。 

## <a name="input-partition-count"></a>输入分区计数 
作业输入的每个输入分区都有一个缓冲区。 输入分区数量越大，作业所消耗的资源越多。 对于每个流单元，Azure 流分析大致可以处理 1 MB/秒的输入。 因此，可以通过将流分析流单元数与事件中心内的分区数进行匹配来进行优化。 

通常，使用一个流单元配置的作业足以满足包含两个分区（事件中心至少包含两个分区）的事件中心。 如果事件中心具有更多分区，流分析作业将耗用更多资源，但不一定使用事件中心提供的额外吞吐量。 

对于包含 6 个流单元的作业，可能需要事件中心的 4 个或 8 个分区。 但是，请避免过多的不必要分区，否则可能会超出资源用量。 例如，在包含 1 个流单元的流分析作业中，使用包含 16 个分区的事件中心或更大的事件中心。 

## <a name="reference-data"></a>引用数据 
ASA 中的引用数据会被加载到内存中，以便快速查找。 在当前的实现中，每个带有引用数据的联接操作都在内存中保留有一份引用数据，即使你多次使用相同的引用数据进行联接也是如此。 对于使用 PARTITION BY 的查询，每个分区都有一份引用数据，因此，这些分区是完全分离的。 通过倍增效应，如果多次使用多个分区联接引用数据，内存使用率很快就会变得非常高。  

### <a name="use-of-udf-functions"></a>使用 UDF 函数
当添加 UDF 函数时，Azure 流分析会将 JavaScript 运行时加载到内存中。 这将影响 SU%。

## <a name="next-steps"></a>后续步骤
* [在 Azure 流分析中创建可并行的查询](stream-analytics-parallelization.md)
* [扩展 Azure 流分析作业以增加吞吐量](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
