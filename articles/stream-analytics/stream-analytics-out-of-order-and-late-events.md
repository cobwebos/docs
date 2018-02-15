---
title: "通过 Azure 流分析处理事件顺序和延迟 | Microsoft Docs"
description: "了解流分析如何处理数据流中的无序事件或延迟事件。"
keywords: "无序、延迟、事件"
documentationcenter: 
services: stream-analytics
author: jseb225
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
ms.openlocfilehash: 71929b449f2a0fa55327fd3f9741208506859e85
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Azure 流分析事件顺序注意事项

## <a name="arrival-time-and-application-time"></a>抵达时间和应用程序时间

在事件的时态数据流中，每个事件都分配有一个时间戳。 Azure 流分析使用到达时间或应用程序时间向各事件分配时间戳。 “System.Timestamp”列包含已分配给事件的时间戳。 

当事件抵达源时，会在输入源中分配到达时间。 可以使用事件中心输入的 EventEnqueuedTime 属性以及 Blob 输入的 [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) 属性来访问到达时间。 

生成事件时，会分配应用程序时间。应用程序时间是有效负载的一部分。 若要按应用程序时间处理事件，可在 select 查询中使用“Timestamp by”子句。 如果缺少“Timestamp by”子句，则会按到达时间处理事件。 

Azure 流分析按时间戳顺序生成输出，并提供一些设置来处理无序数据。


## <a name="handling-of-multiple-streams"></a>多个流的处理

在某些情况下，Azure 流分析作业将合并来自多个时间线的事件，如下所示：

* 从多个分区生成输出。 未显式指定“Partition by PartitionId”子句的查询必须合并来自所有分区的事件。
* 两个或更多个不同输入源的联合。
* 联接输入源。

在合并多个时间线的情况下，只有在合并的所有源都至少位于时间点 t1 之后，Azure 流分析才生成时间戳 t1 的输出。 例如，假定查询从具有两个分区的事件中心分区读取。 其中一个分区 (P1) 在时间点 t1 之前具有事件。 另一个分区 (P2) 在时间点 t1 + x 之后具有事件。 在达到时间点 t1 之前，生成输出。 但是，如果显式指定了“Partition by PartitionId”子句，这两个分区会独立地继续运行。

“延迟到达容错”设置用于处理某些分区中缺少数据的情况。

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>配置延迟到达容错和无序容错
无序输入流包括：
* 已排序（因此已延迟）
* 由系统根据用户指定的策略进行调整

按应用程序时间进行处理时，流分析可以容许延迟事件和无序事件。 Azure 门户中的“事件顺序”选项提供以下设置： 

![流分析事件处理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>延迟到达容错
仅当按应用程序时间进行处理时，延迟到达容错设置才适用。 否则，将忽略该设置。

延迟到达容错是到达时间与应用程序时间的最大差。 如果事件到达晚于延迟到达容错（例如，应用程序时间 app_t 小于到达时间 arr_t 减去延迟到达策略容错 late_t），则将事件调整为延迟到达容错 (arr_t - late_t) 的最大值。 延迟到达时段是生成事件后，输入源收到事件经历的最大延迟。 

如果将相同输入流或多个输入流中的多个分区组合在一起，则延迟到达容错就是每个分区等待新数据的最长时间。 

首先，根据延迟到达容错进行调整。 接着，根据无序容错进行调整。 System.Timestamp 列包含已分配给事件的最终时间戳。

### <a name="out-of-order-tolerance"></a>无序容错
对于以无序方式到达、但在“无序容错时段”范围内的事件，会按时间戳重新排序。 晚于容错时段的事件会被：
* 已调整：调整为在最晚的可接受时间到达。 
* 已删除：已放弃。

当流分析对在“无序容错时段”范围内收到的事件重新排序时，查询的输出会根据无序容错时段延迟。

### <a name="example"></a>示例

* 延迟到达容错 = 10 分钟<br/>
* 无序容错 = 3 分钟<br/>
* 按应用程序时间进行处理<br/>
* 事件：
   1. 应用程序时间 = 00:00:00，到达时间 = 00:10:01，System.Timestamp = 00:00:01，经过调整，因为“(到达时间 - 应用程序时间)”大于延迟到达容错。
   2. 应用程序时间 = 00:00:01，到达时间 = 00:10:01，System.Timestamp = 00:00:01，未经过调整，因为应用程序时间在延迟到达时段范围内。
   3. 应用程序时间 = 00:10:00，到达时间 = 00:10:02，System.Timestamp = 00:10:00，未经过调整，因为应用程序时间在延迟到达时段范围内。
   4. 应用程序时间 = 00:09:00，到达时间 = 00:10:03，System.Timestamp = 00:09:00，已根据原始时间戳接受，因为应用程序时间在无序容错范围内。
   5. 应用程序时间 = 00:06:00，到达时间 = 00:10:04，System.Timestamp = 00:07:00，经过调整，因为应用程序时间早于无序容错。

### <a name="practical-considerations"></a>实际考虑因素
如前所述，延迟到达容错是应用程序时间与到达时间之间的最大差。 在按应用程序时间进行处理时，会在应用无序容错设置之前，调整比配置的延迟到达容错更晚的事件。 因此，有效的无序是延迟到达容错与无序容错中的最小值。

流中出现无序事件的原因包括：
* 发送者之间存在时钟偏差。
* 发送者与输入事件源之间存在可变的延迟。

延迟到达的原因包括：
* 发送者在间隔时间过后根据某个间隔批处理和发送事件。
* 发送者发送事件之后、在输入源位置收到事件之前存在延迟。

配置特定作业的延迟到达容错和无序容错时，应考虑到正确性、延迟要求和上述因素。

下面是几个示例。

#### <a name="example-1"></a>示例 1 
查询具有“Partition by PartitionId”子句。 在单个分区中，事件以同步发送的方式发送。 在发送事件之前，同步发送方法一直阻塞。

在这种情况下，无序为零，因为事件是按顺序发送的，只有在显式确认后才发送下一个事件。 延迟到达时间是事件生成时间与事件发送时间之间的最大延迟，与发送者与输入源之间的最大延迟之和。

#### <a name="example-2"></a>示例 2
查询具有“Partition by PartitionId”子句。 在单个分区中，事件以异步发送的方式发送。 异步发送方法可以同时启动多个发送操作，这可能导致事件无序。

在这种情况下，无序时间和延迟到达时间至少是事件生成时间与事件发送时间之间的最大延迟 + 发送者与输入源之间的最大延迟。

#### <a name="example-3"></a>示例 3
查询不包含“Partition by PartitionId”子句，并且至少有两个分区。

配置与示例 2 相同。 但是，如果一个分区中缺少数据，可能会按照额外的“延迟到达容错”时限将输出延迟。

## <a name="handling-event-producers-with-differing-timelines"></a>通过不同的时间线处理事件生成者
单个输入事件流中通常包含来自多个事件生成者的事件，如单个设备。 这些事件可能以无序方式到达，原因如上所述。 在这些情况中，尽管事件生成者之间的无序可能相当严重，但单个生成者中事件间的无序则很轻微（甚至不存在）。

Azure 流分析提供处理无序事件的常规机制。 这些机制会造成处理延迟（等待散乱事件到达系统）、删除或调整事件，或兼而有之。

但在大多数情况中，所需的查询将独立处理来自不同事件生成者的事件。 例如，它可能会分窗口、分设备地聚合事件。 在这些情况下，无需为了等待另一个事件生成者赶上而延迟对应于某个事件生成者的输出。 换言之，无需处理生成者之间的时间偏差。 可将其忽略。

当然，这即意味着，输出事件本身在其时间戳方面就是无序的。 下游使用者必须能够处理这种行为。 但输出中的每个事件都是正确的。

Azure 流分析使用 [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx) 子句来实现此功能。

## <a name="summary"></a>摘要
* 根据正确性和延迟需求配置延迟到达容错和无序窗口。 并且考虑如何发送事件。
* 建议将无序容错设置得比延迟到达容错更小。
* 在合并多个时间线时，如果一个源或分区中缺少数据，可能会按照额外的延迟到达容错时限将输出延迟。

## <a name="get-help"></a>获取帮助
有关更多帮助，请访问 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-real-time-fraud-detection.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
