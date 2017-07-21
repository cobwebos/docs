---
title: "Azure 流分析：优化作业以高效使用流式处理单位 | Microsoft Docs"
description: "对于 Azure 流分析中缩放和性能的查询最佳做法。"
keywords: "流单元, 查询性能"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 341d4f057e0a4611bbff498d494ca54d0b1fd9e7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017


---

# <a name="optimize-your-job-to-use-streaming-units-efficiently"></a>优化作业以高效使用流式处理单位

Azure 流分析将运行作业的性能“权重”聚合到流式处理单位（SU）中。 SU 表示执行作业所消耗的计算资源。 在已经对 CPU、内存以及读取和写入速率进行测量的情况下，可以使用 SU 来描述相对的事件处理能力。 此容量使你能够专注于查询逻辑，并且无需再了解存储层性能注意事项、无需手动为作业分配内存，以及无需计算及时运行作业所需的 CPU 核心计数的近似值。

## <a name="how-many-sus-are-required-for-a-job"></a>一个作业需要多少 SU？

为特定作业选择所需的 SU 数量时，需要根据输入的分区配置以及在作业内定义的查询来决定。 可以使用“缩放”边栏选项卡设置正确的 SU 数量。 分配的 SU 数最好超过所需的数量。 流分析处理引擎会针对延迟和吞吐量进行优化，不过，代价是需要分配额外的内存。

通常情况下，最佳做法是一开始为不使用 PARTITION BY 的查询分配 6 个 SU。 然后，在传递了具有代表性的数据量并检查了 SU %Utilization 指标后，使用修改 SU 数量的试用和错误方法来确定最佳数量。

在开始进行任何处理之前，Azure 流分析将事件保留在一个名为“重新排序缓冲区”的窗口中。 将在重新排序窗口内部按时间对事件进行排序，并且将针对临时排序的事件执行后续操作。 按时间对事件进行重新排序可以确保运算符能浏览规定时间范围内的所有事件。 它还能使运算符执行必要处理并生成输出。 此机制的一个副作用是，处理会出现延迟，延迟的时间长短即是重新排序窗口的持续时间。 作业的内存占用量（这将影响 SU 消耗）是此重新排序窗口的大小及其包含的事件数的函数。

> [!NOTE]
> 当读取器的数量在作业升级期间发生更改时，暂时性警告将被写入到审核日志中。 流分析作业会自动从这些暂时性问题中恢复。

## <a name="common-high-memory-causes-for-high-su-usage-for-running-jobs"></a>常见的高内存导致运行作业时 SU 使用率较高

### <a name="high-cardinality-for-group-by"></a>GROUP BY 的基数较高

传入事件的基数指示作业的内存使用量。

例如，在 `SELECT count(*) from input group by clustered, tumblingwindow (minutes, 5)` 中，与 clustered 关联的数字就是查询的基数。

若要缓解由高基数导致的问题，可以使用 PARTITION BY 增加分区来横向扩展查询。

```
Select count(*) from input
Partition By clusterid
GROUP BY clustered tumblingwindow (minutes, 5)
```

这里，clustered 的数字是 GROUP BY 的基数。

在对查询进行分区后，它将会分散到多个节点中。 如此一来，传入每个节点的事件数将减少，进而降低重新排序缓冲区的大小。 还应该按 partitionid 对事件中心分区进行分区。

### <a name="high-unmatched-event-count-for-join"></a>JOIN 的不匹配事件数较大

JOIN 中的不匹配事件数会影响查询的内存利用率。 例如，以查找产生点击量的广告窗口数的查询为例：

```
SELECT id from clicks INNER JOIN,
impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10
```

在此方案中，很可能会显示大量广告，但几乎不会产生点击量。 此类结果需要作业保留此时间范围内的所有事件。 内存消耗量与窗口大小和事件发生速率成比例。 

若要缓解这种情况，请通过使用 PARTITION BY 增加分区来横向扩展查询。 

在对查询进行分区后，它将会分散到多个处理的节点中。 如此一来，传入每个节点的事件数将减少，进而降低重新排序缓冲区的大小。

### <a name="large-number-of-out-of-order-events"></a>存在大量的失序事件 

如果在一个较大的时间范围内出现大量的失序事件，将导致“重新排序缓冲区”变大。 若要缓解这种情况，请通过使用 PARTITION BY 增加分区来扩展查询。 在对查询进行分区后，它将会分散到多个节点中。 如此一来，传入每个节点的事件数将减少，进而降低重新排序缓冲区的大小。 


## <a name="get-help"></a>获取帮助
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

