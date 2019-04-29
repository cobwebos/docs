---
title: 配置事件排序策略用于 Azure Stream Analytics
description: 本文介绍如何配置甚至 Stream Analytics 中的排序设置
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789467"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>配置事件排序策略用于 Azure Stream Analytics

本文介绍如何设置和使用 Azure Stream Analytics 中的延迟到达和无序事件策略。 仅当你使用应用这些策略[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)在查询。

## <a name="event-time-and-arrival-time"></a>事件时间与到达时间

Stream Analytics 作业可以处理事件的任*事件时间*或*到达时间*。 **事件/应用程序时间**（时生成事件） 是事件有效负载中存在的时间戳。 **到达时间**是在输入源 （事件中心/IoT 中心/Blob 存储） 收到事件时的时间戳。 

默认情况下，Stream Analytics 处理的事件*到达时间*，但你可以选择要处理的事件*事件时间*通过[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)在查询。 延迟到达和无序策略是按事件时间处理事件时才适用。 配置这些设置时，请考虑为你的方案的延迟和正确性要求。 

## <a name="what-is-late-arrival-policy"></a>延迟到达策略是什么？

有时事件延迟到达由于各种原因。 例如，到达 40 秒晚的事件将具有事件时间 = 00:10:00，到达时间 = 00:10:40。 如果您设置为 15 秒，到达时间晚于 15 秒会是在删除 （Stream Analytics 不处理） 或具有其事件时间调整任何事件晚到策略。 在上面的示例中，如事件到达 40 秒后期 （更多个策略集），其事件时间将调整为最大值后期到达策略 00:10:25 （到达时间-延迟到达策略值）。 晚到策略的默认值为 5 秒。

## <a name="what-is-out-of-order-policy"></a>无序策略是什么？ 

也可能到达无序事件。 根据延迟到达策略调整事件时间后，可以选择以自动删除或调整的无序事件。 如果将此策略设置为 8 秒时，按事件时间到达顺序，但在 8 秒的时间窗口内的任何事件进行重新排序。 将删除或调整为最大无序策略值晚的事件。 默认的无序策略为 0 秒。 

## <a name="adjust-or-drop-events"></a>调整或删除事件

如果事件到达晚了，或者出的顺序基于已配置的策略，可以删除 （不处理通过 Stream Analytics） 此类事件或调整其事件时间。

让我们查看操作中的这些策略的示例。
<br> **晚到策略：** 15 秒
<br> **无序策略：** 8 秒

| 事件不会。 | 事件时间 | 到达时间 | System.Timestamp | 说明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 事件到达后期和外部的容差级别。 因此，事件时间获取调整为最大延迟到达容错。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 事件到达晚，但在容差级别内。 因此不会不获取调整事件时间。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 事件到达时间。 所需做任何调整。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 事件到达顺序颠倒，但 8 秒的公差范围内。 因此，不会不获取调整事件时间。 用于分析此事件将被认为是前面的事件编号 4。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 事件到达顺序颠倒和外部容差为 8 秒。 因此，事件时间调整为最大的无序容错。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>这些设置可能会延迟我作业的输出？ 

可以。 默认情况下，无序策略设置为零 （00 分 00 秒）。 如果更改默认值时，作业的第一个输出是延迟此值 （或更高版本）。 

如果你的输入分区中的一个不会接收事件，则会输出延迟到达策略值会延迟。 若要了解原因，请阅读下面 InputPartition 错误部分。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>我在我的活动日志中看到 LateInputEvents 消息

会显示这些消息来通知你，事件具有晚到达并删除或根据你的配置进行调整。 如果已相应地配置晚到策略，则可以忽略这些消息。 

此消息的示例是： <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>我在我的活动日志中看到 InputPartitionNotProgressing

在输入的源 （事件中心/IoT 中心） 可能有多个分区。 Azure Stream Analytics 在合并的所有分区都都至少在时间 t1 后才生成时间戳 t1 的输出。 例如，假定查询从具有两个分区的事件中心分区读取。 其中一个分区，P1，时间 t1 之前具有事件。 另一个分区，P2，时间 t1 + x 之后具有事件。 时间 t1 之前，生成输出。 但如果 PartitionId 子句显式分区，两个分区独立地继续，请运行。 

当相同的输入流中的多个分区合并时，延迟到达容错是时间的最大每个分区等待新数据量。 如果事件中心，在一个分区或者如果 IoT 中心不会接收输入，对该分区的日程表不会继续直到到达延迟到达容错阈值。 这会将输出延迟由延迟到达容错阈值。 在这种情况下，可能会看到以下消息：
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
此消息，通知你，在输入中的至少一个分区为空，并且将由延迟到达阈值延迟输出。 若要解决此问题，建议你或者：1. 请确保所有分区的事件中心/IoT 中心都接收的输入。 2. 通过在查询中的 PartitionID 子句中使用分区。 

## <a name="next-steps"></a>后续步骤
* [时间处理注意事项](stream-analytics-time-handling.md)
* [可以在流分析中使用的指标](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
