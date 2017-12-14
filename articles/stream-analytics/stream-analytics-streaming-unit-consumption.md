---
title: "Azure 流分析：了解和调整流式处理单元 | Microsoft 文档"
description: "了解哪些因素会影响 Azure 流分析的性能。"
keywords: "流单元, 查询性能"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e8812f10662ee7b571e8e353074c2537d1a3181b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="understand-and-adjust-streaming-units"></a>了解和调整流式处理单元

Azure 流分析将运行作业的性能“权重”聚合到流式处理单位 (SU) 中。 SU 表示执行作业所消耗的计算资源。 在已经对 CPU、内存以及读取和写入速率进行测量的情况下，可以使用 SU 来描述相对的事件处理能力。 借助这种能力，你能够专注于查询逻辑，并且无需再了解存储层性能注意事项、无需手动为作业分配内存，并且无需计算及时运行作业所需的 CPU 核心计数的近似值。

为了实现低延迟流式处理，Azure 流分析作业将执行内存中的所有处理。 内存不足时，流式处理作业会失败。 因此，对于生产作业，请务必监视流式处理作业的资源使用情况，并确保分配有足够的资源来保持作业的全天候运行。

指标是一个范围从 0% 到 100% 的百分数。 对于占用最小内存的流式处理作业，SU 利用率指标通常介于 10% 到 20%。 最好保持低于 80% 的指标，以应对偶发的峰值。  可以在指标上设置一个警报（请参阅[此处设置指标警报](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)）。



## <a name="configure-stream-analytics-streaming-units-sus"></a>配置流分析流式处理单元 (SU)
1. 登录到 [Azure 门户](http://portal.azure.com/)
2. 在资源列表中，找到要缩放的流分析作业，然后将其打开。 
3. 在“作业”页的“配置”下，单击“缩放”。 

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



## <a name="factors-increasing-su-utilization"></a>增加 SU 利用率的因素 
### <a name="stateful-query-logic"></a>有状态的查询逻辑 
Azure 流分析作业的独有功能之一是执行有状态的处理，如开窗聚合、临时联接和临时分析函数。 每个运算符都保留一些状态。 
#### <a name="windowed-aggregates"></a>开窗聚合
开窗聚合的状态大小与依据运算符分组的组数（基数）成正比。 例如，在以下查询中，与 clusterid 关联的数字就是查询的基数。 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


若要解决前面查询中由高基数导致的问题，可以将事件发送到通过依据“clusterid”分区的事件中心，并通过允许系统使用 PARTITION BY 分别处理每个输入分区来横向扩展查询，如以下示例所示：


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

将查询分区后，它会分散到多个节点中。 因此，可以通过减少依据运算符分组的基数来减少传入每个节点的 clusterid 数。 

事件中心分区应根据分组键进行分区，以避免减少步骤的需要。 其他详细信息，请单击[此处](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview)查看。 
#### <a name="temporal-join"></a>临时联接
临时联接的状态大小与联接的临时调整空间中的事件数量成正比，即事件输入速率与调整空间大小的乘积。 

联接中的不匹配事件数会影响查询的内存利用率。 以下查询将查找产生点击量的广告印象：

    SELECT id
    FROM clicks 
    INNER JOIN, impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

在本示例中，有可能显示了很多广告，但很少有人点击它们，并且需要保留该时间范围内的所有事件。 内存消耗量与时间范围大小和事件发生速率成比例。 

若要修正此问题，请将事件发送到依据联接键（在此情况下为 ID）分区的事件中心，并通过允许系统使用 PARTITION BY 分别处理每个输入分区来横向扩展查询，如下所示：

    SELECT id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

将查询分区后，它会分散到多个节点中。 因此，可以通过减小保留在联接窗口中状态的大小来减少传入每个节点的事件数。 
#### <a name="temporal-analytic-function"></a>临时分析函数
临时分析函数的状态大小与事件速率和持续时间的乘积成正比。 修正的方法类似于临时联接。 你可以使用 PARTITION BY 来横向扩展查询。 

### <a name="out-of-order-buffer"></a>无序缓冲区 
在“事件排序配置”窗格中，用户可以配置无序的缓冲区大小。 可以使用缓冲区来保留窗口持续时间内的输入，并对其进行重新排序。 缓冲区的大小与事件输入速率和无序窗口大小的乘积成正比。 默认窗口大小为 0。 

若要修正此问题，请使用 PARTITION BY 来横向扩展查询。 将查询分区后，它会分散到多个节点中。 因此，可以通过减少每个重新排序缓冲区中的事件数来减少传入每个节点的事件数。 

### <a name="input-partition-count"></a>输入分区计数 
作业输入的每个输入分区都有一个缓冲区。 输入分区数量越大，作业所消耗的资源越多。 对于每个 SU，Azure 流分析可以处理大约 1 MB/秒的输入，因此，你需要将 ASA SU 数量与事件中心的分区数进行匹配。 通常情况下，1SU 作业对于具有 2 个分区的事件中心来说已经足够（这是事件中心的最低要求）。 如果事件中心具有更多分区，ASA 作业将耗用更多资源，但不一定使用事件中心提供的额外吞吐量。 对于 6SU 作业，你可能需要事件中心的 4 个或 8 个分区。 在 1SU 作业中使用具有 16 个分区或更多分区的事件中心，往往会导致资源的过度使用，请尽量避免。 

### <a name="reference-data"></a>引用数据 
ASA 中的引用数据会被加载到内存中，以便快速查找。 在当前的实现中，每个带有引用数据的联接操作都在内存中保留有一份引用数据，即使你多次使用相同的引用数据进行联接也是如此。 对于使用 PARTITION BY 的查询，每个分区都有一份引用数据，因此，这些分区是完全分离的。 通过倍增效应，如果多次使用多个分区联接引用数据，内存使用率很快就会变得非常高。  

#### <a name="use-of-udf-functions"></a>使用 UDF 函数
当添加 UDF 函数时，Azure 流分析会将 JavaScript 运行时加载到内存中。 这将影响 SU%。


## <a name="get-help"></a>获取帮助
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [在 Azure 流分析中创建可并行的查询](stream-analytics-parallelization.md)
* [扩展 Azure 流分析作业以增加吞吐量](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
