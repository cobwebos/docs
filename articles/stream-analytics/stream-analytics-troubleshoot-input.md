---
title: Azure 流分析输入故障排除
description: 本文介绍对 Azure 流分析作业中的输入连接进行故障排除的技巧。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: dac3037f82c38980c9ac16685aa7fddac68a2e7b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720293"
---
# <a name="troubleshoot-input-connections"></a>排查输入连接问题

此页介绍输入连接的常见问题和如何解决这些问题。

## <a name="input-events-not-received-by-job"></a>作业未收到输入事件 
1.  测试连接。 使用每项输入和输出对应的“测试连接”按钮来验证与输入和输出的连接。

2.  检查输入数据。

    1. 若要验证输入数据是否流入事件中心，请使用[服务总线资源管理器](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)连接到 Azure 事件中心（如果使用了事件中心输入）。
        
    1. 为每个输入使用 "[**示例数据**](stream-analytics-sample-data-input.md)" 按钮。 下载输入示例数据。
        
    1. 检查示例数据，了解数据的形状，即架构和[数据类型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)。

3.  确保在输入预览中选择了时间范围。 选择 "**选择时间范围**"，然后输入测试查询之前的示例持续时间。

## <a name="malformed-input-events-causes-deserialization-errors"></a>格式不正确的输入事件导致反序列化错误 
当流分析作业的输入流包含格式不当的消息时，会导致反序列化问题。 例如，JSON 对象中缺少圆括号或大括号，或者时间字段中的时间戳格式不当，都可能导致消息格式不当。 
 
当流分析作业从某个输入收到格式不当的消息时，它会丢弃该消息并通过警告来通知你。 流分析作业的“输入”磁贴上会显示一个警告符号。 只要作业处于运行状态，此警告符号就存在：

![Azure 流分析输入磁贴](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

启用诊断日志，以便查看警告的详细信息。 对于格式不正确的输入事件，执行日志包含一条类似于以下内容的消息： 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>导致反序列化错误的原因
可以执行以下步骤，详细分析输入事件，清晰了解反序列化错误的原因。 然后，可以修复事件源，生成格式正确的事件，防止再次发生此问题。

1. 导航到输入磁贴，然后单击警告符号，以查看问题列表。

2. 输入详细信息磁贴会显示含各问题详细信息的警告的列表。 下面的示例警告消息包含格式不正确的 JSON 数据所在的分区、偏移量和序列号。 

   ![包含偏移量的流分析警告消息](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. 要找到格式正确的 JSON 数据，可在 [GitHub 示例存储库](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)中运行可用的 CheckMalformedEvents.cs 代码。 此代码读取分区 ID、偏移量并列显位于该偏移位置的数据。 

4. 在阅读数据后，你可以分析并更正序列化格式。

5. 还可以[使用服务总线资源管理器从 IoT 中心读取事件](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b)。

## <a name="job-exceeds-maximum-event-hub-receivers"></a>作业超过事件中心接收器的最大数量
使用事件中心的最佳做法是使用多个使用者组来确保作业的伸缩性。 对于特定的输入，流分析作业中读取器的数量会影响单个使用者组中的读取器数量。 接收器的精确数量取决于横向扩展的拓扑逻辑的内部实现详细信息并且不对外公开。 读取器的数量会在作业启动时或作业升级期间发生更改。

当接收器的数量超出了最大数量时显示的错误为：`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> 当读取器的数量在作业升级期间发生更改时，暂时性警告会被写入到审核日志中。 流分析作业会自动从这些暂时性问题中恢复。

### <a name="add-a-consumer-group-in-event-hubs"></a>在事件中心内添加使用者组
若要在事件中心实例内添加新的使用者组，请执行以下步骤：

1. 登录到 Azure 门户。

2. 找到你的事件中心。

3. 选择“实体”标题下的“事件中心”。

4. 通过名称选择事件中心。

5. 在“事件中心实例”页面上，在“实体”标题下，选择“使用者组”。 此时将列出名为 **$Default** 的使用者组。

6. 选择“+ 使用者组”添加新的使用者组。 

   ![在事件中心内添加使用者组](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. 在流分析作业中创建输入以指向事件中心时，你在那里指定了使用者组。 未指定时将使用 $Default。 在创建新的使用者组后，编辑流分析作业中的事件中心输入并指定新使用者组的名称。


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>每个分区的读取器数超过事件中心限制

如果流式处理查询语法多次引用了同一输入事件中心资源，则作业引擎可以为每个查询使用来自该同一使用者组的多个读取器。 当存在对同一使用者组的太多引用时，作业可能会超出限制数 5 并引发错误。 在这些情况下，可以通过使用以下部分中介绍的解决方案，对多个使用者组使用多个输入，从而进行进一步划分。 

每个分区的读取器数超过数据中心限制（5 个）的情况如下：

* 多个 SELECT 语句：如果使用引用同一个事件中心输入的多个 SELECT 语句，则每个 SELECT 语句都将导致新建一个接收器。
* UNION：使用 UNION 时，可能存在引用同一个事件中心或使用者组的多个输入。
* SELF JOIN：使用 SELF JOIN 操作时，可能会多次引用同一个事件中心。

下列最佳做法可帮助缓解每个分区的读取器数超过数据中心限制（5 个）的情况。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>使用 WITH 子句将查询拆分为多个步骤

WITH 子句将指定可由查询中的 FROM 子句引用的临时命名结果集。 在单个 SELECT 语句的执行范围内定义 WITH 子句。

例如，与使用此查询相比：

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

请使用此查询：

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

对于其中具有三个或以上的输入连接到同一事件中心的查询，请创建单独的使用者组。 这需要创建额外的流分析输入。

## <a name="get-help"></a>获取帮助

如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
