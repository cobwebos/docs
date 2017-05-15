---
title: "通过 Azure 流分析处理事件顺序和延迟 | Microsoft Docs"
description: "了解流分析如何处理数据流中的无序事件或延迟事件。"
keywords: "无序、延迟、事件"
documentationcenter: 
services: stream-analytics
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
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 02c854727e5185968dba49233df82ea6ff336ec8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017


---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure 流分析事件顺序处理

在事件的临时数据流中，每个事件记录有接收事件的时间。 某些条件可能会导致事件流偶尔以不同于发送事件的顺序接收某些事件。 简单的 TCP 重新传输或者发送设备和接收事件中心之间的时间偏差都可能造成此问题。 此外，还向接收的事件流添加了“标点”事件，以便在事件未到达时将时间提前。 在“3 分钟内没有登录时通知我”等方案中，需要使用这些操作。

无序输入流包括：
* 已排序（因此“已延迟”）。
* 根据用户指定的策略由系统来调整。


## <a name="lateness-tolerance"></a>延迟容错
流分析允许这些类型的方案。 流分析可对“无序”和“延迟”事件进行处理。 采用以下方式处理这些事件：

* 对于以无序方式到达、但在设置的容错范围内的事件，将按时间戳来记录。
* 对于晚于容错范围的事件，将对其进行删除或调整。
    * 已调整：调整为在最晚的可接受时间到达。
    * 已删除：已放弃。

![流分析事件处理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>减少无序事件的数目

由于在处理传入事件（如窗口化聚合或临时联接）时，流分析应用了临时转换，因此流分析将按时间戳顺序对传入事件进行排序。

若不使用“按时间戳”关键字，则将默认使用 Azure 事件中心事件排队时间。 事件中心可确保每个事件中心分区上时间戳的单一性。 它还可确保来自所有分区的事件都将按时间戳顺序合并。 这两个事件中心保障可确保没有无序事件。

有时，使用发送者的时间戳是很重要的。 在这种情况下，可使用“按时间戳”选择来自事件有效负载的时间戳。 在这些方案中，有可能引入一个或多个无序事件的源：

* 事件创建器具有时钟偏差。 当创建器来自不同计算机因而具有不同时钟时，此情况很常见。
* 在事件源和目标事件中心之间存在网络延迟。
* 事件中心分区之间存在时钟偏差。 流分析首先通过事件排队时间，对来自所有事件中心分区的事件进行排序。 然后，检查无序事件的数据流。

在配置选项卡中，可以看到以下默认值：

![流分析无序处理](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

如果将无序容错时间范围设为 0 秒，则断言全体事件始终有序。 考虑到无序事件的三个源，则这不可能为 true。 

若要允许流分析更正事件无序，可指定一个非零无序容错时间范围。 流分析缓冲事件到该时间范围，然后使用所选的时间戳对其重新排序。 然后，它将应用临时转换。 可以从 3 秒时间范围开始调整值，以减少时间调整的事件的数目。 

缓冲的一个副作用是输出将延迟相同时长。 可以通过调整值来减少无序事件的数目，并将作业延迟控制在较低的水平。

## <a name="get-help"></a>获取帮助
有关更多帮助，请访问我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-get-started.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
