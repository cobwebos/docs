---
title: 为 Azure 流分析配置事件排序策略
description: 本文介绍如何在流分析中进行更多的排序设置配置
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461189"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>为 Azure 流分析配置事件排序策略

本文介绍如何在 Azure 流分析中设置和使用延迟到达和顺序外事件策略。 仅当在查询中使用[TIMESTAMP by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)子句时，才应用这些策略。

## <a name="event-time-and-arrival-time"></a>事件时间和到达时间

你的流分析作业可以根据*事件时间*或*到达时间*处理事件。 **事件/应用程序时间**是事件负载（生成事件时）中存在的时间戳。 **到达时间**是在输入源（事件中心/IoT 中心/Blob 存储）收到事件时的时间戳。 

默认情况下，流分析会按*到达时间*处理事件，但你可以选择在查询中使用[TIMESTAMP by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)子句按*事件时间*处理事件。 仅当按事件时间处理事件时，延迟到达和顺序外策略才适用。 配置这些设置时，应考虑方案的延迟要求和正确性要求。 

## <a name="what-is-late-arrival-policy"></a>什么是延迟到达策略？

由于各种原因，有时事件会延迟。 例如，晚于40秒的事件将有事件时间 = 00:10:00，到达时间 = 00:10:40。 如果将延迟到达策略设置为15秒，则任何到达15秒以上的事件都将被删除（流分析不处理）或调整其事件时间。 在上面的示例中，由于事件晚到40秒（超过策略集），其事件时间将调整为 "延迟到达策略00:10:25 （到达时间-延迟到达策略值）" 的最大值。 默认的延迟到达策略为5秒。

## <a name="what-is-out-of-order-policy"></a>什么是订单外策略？ 

事件可能也未按顺序到达。 根据延迟到达策略调整事件时间后，还可以选择自动删除或调整不按顺序的事件。 如果将此策略设置为8秒，则任何按顺序到达但位于8秒窗口内的事件都将按事件时间重新排序。 稍后到达的事件将被删除或调整到最大的顺序策略值。 默认的顺序外策略为0秒。 

## <a name="adjust-or-drop-events"></a>调整或删除事件

如果事件基于已配置的策略延迟或无序到达，则可以删除此类事件（不被流分析处理），也可以调整其事件时间。

让我们看一看这些策略的示例。
<br> **延迟到达策略：** 15 秒
<br> 不**顺序策略：** 8 秒

| 事件编号 | 活动时间 | 到达时间 | System.Timestamp | 说明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 事件的到达延迟和超出容差级别。 因此，事件时间会调整到最大延迟到达容差。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 事件到达时，但在容差范围内。 因此不会调整事件时间。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 事件到达时。 无需调整。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 事件未按顺序到达，但超出了8秒的容差。 因此，不会调整事件时间。 出于分析目的，此事件将被视为前面的事件号4。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 事件到达顺序超出8秒。 因此，将事件时间调整到最大顺序。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>这些设置是否可以延迟作业的输出？ 

可以。 默认情况下，无序策略设置为零（00分和00秒）。 如果更改默认值，则作业的第一个输出会被此值（或更高版本）延迟。 

如果输入的其中一个分区未收到事件，则应预计输出会被延迟到达策略值延迟。 若要了解原因，请参阅下面的 InputPartition 错误部分。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>我在我的活动日志中看到 LateInputEvents 消息

将显示这些消息，通知你事件已延迟到达，并根据配置删除或调整。 如果你已正确配置了 "延迟到达" 策略，你可以忽略这些消息。 

此消息的示例如下： <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>我在我的活动日志中看到 InputPartitionNotProgressing

输入源（事件中心/IoT 中心）可能包含多个分区。 仅当组合的所有分区至少在时间 t1 时，Azure 流分析才生成时间戳 t1 的输出。 例如，假定查询从具有两个分区的事件中心分区读取。 分区 P1 包含事件，直到时间 t1。 另一个分区 P2 包含事件，直到时间 t1 + x。 然后，将生成输出到时间 t1。 但如果有显式 Partition by PartitionId 子句，则这两个分区会独立运行。 

合并同一输入流中的多个分区时，延迟到达容错是每个分区等待新数据的最长时间。 如果事件中心有一个分区，或 IoT 中心未接收到输入，则在到达延迟到达容错阈值之前，该分区的时间线不会进行。 这会使输出延迟，延迟到达容错阈值。 在这种情况下，你可能会看到以下消息：
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
此消息通知你输入中至少有一个分区为空，并将按延迟到达阈值延迟输出。 若要解决此情况，建议您执行以下操作之一：  
1. 确保事件中心/IoT 中心的所有分区都接收输入。 
2. 在查询中使用 Partition by PartitionID 子句。 

## <a name="next-steps"></a>后续步骤
* [时间处理注意事项](stream-analytics-time-handling.md)
* [可以在流分析中使用的指标](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
