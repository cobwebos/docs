---
title: Azure 流分析输入故障排除
description: 本文介绍对 Azure 流分析作业中的输入连接进行故障排除的技巧。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 920755e128f10a79a056d47813b1b65d8633c937
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628736"
---
# <a name="troubleshoot-input-connections"></a>排查输入连接问题

本文介绍了 Azure 流分析输入连接的常见问题、如何排查输入问题，以及如何解决这些问题。 许多故障排除步骤都需要为流分析作业启用资源日志。 如果未启用资源日志，请参阅[使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)。

## <a name="input-events-not-received-by-job"></a>作业未收到输入事件 

1.  测试输入和输出连接。 使用每项输入和输出对应的“测试连接”  按钮来验证与输入和输出的连接。

2.  检查输入数据。

    1. 对每个输入使用[**示例数据**](stream-analytics-sample-data-input.md)按钮。 下载输入示例数据。
        
    1. 检查示例数据，了解架构和[数据类型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)。
    
    1. 检查[事件中心指标](../event-hubs/event-hubs-metrics-azure-monitor.md)，确保正在发送事件。 如果事件中心正在接收消息，则消息指标应大于零。

3.  确保在输入预览中选择了时间范围。 选择“选择时间范围”  ，输入示例持续时间，然后测试查询。

## <a name="malformed-input-events-causes-deserialization-errors"></a>格式不正确的输入事件导致反序列化错误 

当流分析作业的输入流包含格式不当的消息时，会导致反序列化问题。 例如，JSON 对象中缺少圆括号或大括号，或者时间字段中的时间戳格式不当，都可能导致消息格式不当。 
 
当流分析作业从某个输入收到格式不当的消息时，它会丢弃该消息并通过警告来通知你。 流分析作业的“输入”磁贴上会显示一个警告符号  。 只要作业处于运行状态，以下警告符号就会存在：

![Azure 流分析输入磁贴](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

启用资源日志以查看错误的详细信息以及导致错误的消息（负载）。 有多种原因会导致反序列化错误。 若要详细了解特定的反序列化错误，请参阅[输入数据错误](data-errors.md#input-data-errors)。 如果未启用资源日志，Azure 门户中将提供一个简短通知。

![输入详细信息警告通知](media/stream-analytics-malformed-events/warning-message-with-offset.png)

如果消息有效负载大于 32 KB 或采用二进制格式，请运行 [GitHub 示例存储库](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)中提供的 CheckMalformedEvents.cs 代码。 此代码读取分区 ID、偏移量并列显位于该偏移位置的数据。 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>作业超出了事件中心接收器的最大数量

使用事件中心时，最佳做法是使用多个使用者组来确保作业的可伸缩性。 对于特定的输入，流分析作业中读取器的数量会影响单个使用者组中的读取器数量。 接收器的精确数量取决于横向扩展的拓扑逻辑的内部实现详细信息并且不对外公开。 读取器的数量会在作业启动时或作业升级期间发生更改。

当接收方数量超过最大值时，将显示以下错误消息。 错误消息包含在使用者组下与事件中心建立的现有连接的列表。 标记`AzureStreamAnalytics`指示连接来自 Azure 流式处理服务。

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> 当读取器的数量在作业升级期间发生更改时，暂时性警告会被写入到审核日志中。 在发生这些暂时性问题后，流分析作业会自动恢复。

### <a name="add-a-consumer-group-in-event-hubs"></a>在事件中心内添加使用者组

若要在事件中心实例内添加新的使用者组，请执行以下步骤：

1. 登录到 Azure 门户。

2. 找到你的事件中心。

3. 选择“实体”标题下的“事件中心”。  

4. 通过名称选择事件中心。

5. 在“事件中心实例”页面上，在“实体”标题下，选择“使用者组”。    此时将列出名为 **$Default** 的使用者组。

6. 选择“+ 使用者组”添加新的使用者组  。 

   ![在事件中心内添加使用者组](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. 在流分析作业中创建输入以指向事件中心时，你在那里指定了使用者组。 在未指定的情况下，会使用 $Default  。 在创建新的使用者组后，编辑流分析作业中的事件中心输入并指定新使用者组的名称。

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>每个分区的读取器数超过事件中心限制

如果流式处理查询语法多次引用了同一输入事件中心资源，则作业引擎可以为每个查询使用来自该同一使用者组的多个读取器。 当存在对同一使用者组的太多引用时，作业可能会超出限制数 5 并引发错误。 在这些情况下，可以通过使用以下部分中介绍的解决方案，对多个使用者组使用多个输入，从而进行进一步划分。 

每个分区的读取器数超过数据中心限制（5 个）的情况如下：

* 多个 SELECT 语句：如果使用引用“同一个”事件中心输入的多个 SELECT 语句，则每个 SELECT 语句都将导致新建一个接收器  。

* UNION：使用 UNION 时，可能存在引用“同一个”事件中心或使用者组的多个输入  。

* SELF JOIN：使用 SELF JOIN 操作时，可能会多次引用“同一个”事件中心  。

下列最佳做法可帮助缓解每个分区的读取器数超过数据中心限制（5 个）的情况。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>使用 WITH 子句将查询拆分为多个步骤

WITH 子句将指定可由查询中的 FROM 子句引用的临时命名结果集。 在单个 SELECT 语句的执行范围内定义 WITH 子句。

例如，与其使用此查询：

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

不如使用此查询：

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>确保输入绑定到不同的使用者组

对于有三个或三个以上输入连接到同一事件中心的查询，请创建单独的使用者组。 这需要创建额外的流分析输入。

## <a name="get-help"></a>获取帮助

如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
