---
title: "通过 Azure 流分析处理事件顺序和延迟 | Microsoft Docs"
description: "了解流分析如何处理数据流中的无序事件或延迟事件。"
keywords: "无序、延迟、事件"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: cf57bd12a62b3de8ac49b26ce7cdc40aec0b6738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure 流分析事件顺序处理

在事件的时态数据流中，每个事件都分配有一个时间戳。 Azure 流分析使用到达时间或应用程序时间向各事件分配时间戳。 “System.Timestamp”列包含已分配给事件的时间戳。 当事件抵达源时，会在输入源中分配到达时间。 到达时间是事件中心输入的 EventEnqueuedTime 和 Blob 输入的 [Blob 上次修改时间](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3)。 生成事件时，会分配应用程序时间。应用程序时间是有效负载的一部分。 若要按应用程序时间处理事件，可在 select 查询中使用“Timestamp by”子句。 如果缺少“Timestamp by”子句，则会按到达时间处理事件。 可使用事件中心的 EventEnqueuedTime 属性以及 Blob 输入的 BlobLastModified 属性来访问到达时间。 Azure 流分析按时间戳顺序生成输出，并提供一些设置来处理无序数据。

![流分析事件处理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

无序输入流包括：
* 已排序（因此“已延迟”）。
* 由系统根据用户指定的策略进行调整。

按应用程序时间进行处理时，流分析可以容许延迟事件和无序事件。

**延迟到达容错**

* 仅当按应用程序时间进行处理时，此设置才适用，否则请忽略。
* 这是到达时间和应用程序时间之间的最大差。 如果应用程序时间先于 (到达时间 - 延迟到达时段)，则其设置为 (到达时间 - 延迟到达时段)
* 如果将相同输入流或多个输入流中的多个分区组合在一起，则延迟到达容错就是每个分区等待新数据的最长时间。 

简而言之，延迟到达时段是生成事件后，输入源收到事件经历的最大延迟。
首先根据延迟到达容错执行调整，然后根据无序容错执行调整。 “System.Timestamp”列会向事件分配最终时间戳。

**无序容错**

* 对于以无序方式到达、但在“无序容错时段”范围内的事件，会按时间戳重新排序。 
* 晚于容错时段的事件会被删除或调整。
    * 已调整：调整为在最晚的可接受时间到达。 
    * 已删除：已放弃。

为了重新排列在“无序容错时段”范围内收到的事件的顺序，查询的输出会根据无序容错时段延迟。

**示例**

延迟到达容错 = 10 分钟<br/>
无序容错 = 3 分钟<br/>
按应用程序时间进行处理<br/>

事件：

事件 1 应用程序时间 = 00:00:00，到达时间 = 00:10:01，System.Timestamp = 00:00:01，经过调整，因为“(到达时间  -  应用程序时间)”大于延迟到达容错。

事件 2 应用程序时间 = 00:00:01，到达时间 = 00:10:01，System.Timestamp = 00:00:01，未经过调整，因为应用程序时间在延迟到达时段范围内。

事件 3 应用程序时间 = 00:10:00，到达时间 = 00:10:02，System.Timestamp = 00:10:00，未经过调整，因为应用程序时间在延迟到达时段范围内。

事件 4 应用程序时间 = 00:09:00，到达时间 = 00:10:03，System.Timestamp = 00:09:00，已根据原始时间戳接受，因为应用程序时间在无序容错范围内。

事件 5 应用程序时间 = 00:06:00，到达时间 = 00:10:04，System.Timestamp = 00:07:00，经过调整，因为应用程序时间早于无序容错。



## <a name="get-help"></a>获取帮助
有关更多帮助，请访问我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-real-time-fraud-detection.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
