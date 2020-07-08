---
title: 为 Azure 流分析配置事件排序策略
description: 本文介绍如何在流分析中配置事件排序设置
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/12/2019
ms.openlocfilehash: e9617018b06d4f62b49946ae5593bd51805355e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044560"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>为 Azure 流分析配置事件排序策略

本文介绍如何在 Azure 流分析中设置和使用延期抵达和失序事件策略。 仅当你在查询中使用[TIMESTAMP by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)子句并且它们仅适用于云输入源时，才应用这些策略。

## <a name="event-time-and-arrival-time"></a>事件时间和抵达时间

流分析作业可以根据事件时间或抵达时间处理事件。   **事件/应用程序时间**是事件有效负载中的时间戳（生成事件的时间）。 **抵达时间**是输入源（事件中心/IoT 中心/Blob 存储）收到事件时的时间戳。 

默认情况下，流分析按抵达时间处理事件，但你可以通过在查询中使用 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 子句，来按事件时间处理事件。   仅当按事件时间处理事件时，延期抵达和失序策略才适用。 配置这些设置时，应考虑方案的延迟要求和正确性要求。 

## <a name="what-is-late-arrival-policy"></a>什么是延期抵达策略？

有时，事件会出于各种原因而延期抵达。 例如，延期 40 秒抵达的事件的事件时间为 00:10:00，抵达时间为 00:10:40。 如果将延期抵达策略设置为 15 秒，则 15 秒后抵达的任何事件将被丢弃（流分析不会处理它），或事件时间将被调整。 在以上示例中，由于事件延期 40 秒抵达（超过了设置的策略），其事件时间将调整为延期抵达策略最大值，即 00:10:25（到达时间 - 延期抵达策略值）。 默认的延期抵达策略为 5 秒。

## <a name="what-is-out-of-order-policy"></a>什么是失序策略？ 

事件也可能会失序抵达。 根据延期抵达策略调整事件时间后，还可以选择自动丢弃或调整失序的事件。 如果将此策略设置为 8 秒，则失序抵达，但处于 8 秒时限内的任何事件将按事件时间重新排序。 延期抵达的事件将被丢弃或调整为失序策略的最大值。 默认的失序策略为 0 秒。 

## <a name="adjust-or-drop-events"></a>调整或丢弃事件

如果事件根据配置的策略延期抵达或失序抵达，你可以丢弃此类事件（不会由流分析处理），或者调整其事件时间。

让我们看看这些策略的运作方式示例。
<br> **延期抵达策略：** 15 秒
<br> **失序策略：** 8 秒

| 事件编号 | 事件时间 | 到达时间 | System.Timestamp | 说明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 事件延期抵达并超过容限级别。 因此，事件时间已调整为最大延期抵达容限。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 事件延期抵达，但在容限级别范围内。 因此未调整事件时间。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 事件准时抵达。 无需调整。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 事件失序抵达，但在 8 秒容限范围内。 因此未调整事件时间。 在分析中，此事件被视为等同于前面的事件编号 4。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 事件失序抵达并超过 8 秒容限范围。 因此，事件时间已调整为最大失序容限。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>这些设置是否会延迟作业的输出？ 

是的。 默认情况下，失序策略设置为 0（00 分 00 秒）。 如果更改默认值，则作业的第一个输出将按此值延迟（或有更大的延迟）。 

如果输入的分区之一未收到事件，则输出预期会按延期抵达策略值延迟。 若要了解原因，请参阅下面的“InputPartition 错误”部分。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>我在活动日志中看到 LateInputEvents 消息

这些消息旨在告知事件已延期抵达，并已根据配置丢弃或调整。 如果已正确配置了延期抵达策略，则可以忽略这些消息。 

此消息的示例如下： <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>我在活动日志中看到 InputPartitionNotProgressing

你的输入源（事件中心/IoT 中心）可能包含多个分区。 只有在合并的所有分区都至少位于时间点 t1 之后，Azure 流分析才生成时间戳 t1 的输出。 例如，假设查询从包含两个分区的事件中心分区读取数据。 其中一个分区 P1 包含时间点 t1 之前的事件。 另一个分区 P2 包含时间点 t1 + x 之前的事件。 那么，只有到达时间点 t1 时，才生成输出。 但是，如果显式指定了 Partition by PartitionId 子句，这两个分区会独立地继续运行。 

如果将相同输入流中的多个分区组合在一起，则延期抵达容限就是每个分区等待新数据的最长时间。 如果事件中心只包含一个分区，或者 IoT 中心未收到输入，则在该分区达到延期抵达容限阈值之前，其时间线不会递进。 这会根据延期抵达容限阈值延迟输出。 在这种情况下，可能会出现以下消息：
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
此消息告知输入中至少有一个分区是空的，会按延期抵达阈值延迟输出。 若要解决此问题，建议采取以下措施之一：  
1. 确保事件中心/IoT 中心的所有分区都收到输入。 
2. 在查询中使用 Partition by PartitionID 子句。 

## <a name="next-steps"></a>后续步骤
* [时间处理注意事项](stream-analytics-time-handling.md)
* [可以在流分析中使用的指标](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
