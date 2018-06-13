---
title: 在 Azure 流分析中对事件中心接收器进行故障排除
description: 本文介绍了如何为流分析作业中的事件中心输入使用多个使用者组。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312950"
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>在 Azure 流分析中对事件中心接收器进行故障排除

可以使用 Azure 流分析中的 Azure 事件中心来从作业中引入或输出作业。 使用事件中心的最佳做法是使用多个使用者组来确保作业的伸缩性。 其中一个原因是，对于特定的输入，流分析作业中读取器的数量会影响单个使用者组中的读取器数量。 接收器的精确数量取决于横向扩展的拓扑逻辑的内部实现详细信息。 接收器的数量不向外部公开。 读取器的数量会在作业启动时或作业升级期间发生更改。

当接收器的数量超出了最大数量时显示的错误为：`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> 当读取器的数量在作业升级期间发生更改时，暂时性警告会被写入到审核日志中。 流分析作业会自动从这些暂时性问题中恢复。

## <a name="add-a-consumer-group-in-event-hubs"></a>在事件中心内添加使用者组
若要在事件中心实例内添加新的使用者组，请执行以下步骤：

1. 登录到 Azure 门户。

2. 找到你的事件中心。

3. 选择“实体”标题下的“事件中心”。

4. 通过名称选择事件中心。

5. 在“事件中心实例”页面上，在“实体”标题下，选择“使用者组”。 此时将列出名为 **$Default** 的使用者组。

6. 选择“+ 使用者组”添加新的使用者组。 

   ![在事件中心内添加使用者组](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. 在流分析作业中创建输入以指向事件中心时，你在那里指定了使用者组。 未指定时将使用 $Default。 在创建新的使用者组后，编辑流分析作业中的事件中心输入并指定新使用者组的名称。


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>每个分区的读取器数超过事件中心限制（5 个）

如果流式处理查询语法多次引用了同一输入事件中心资源，则作业引擎可以为每个查询使用来自该同一使用者组的多个读取器。 当存在对同一使用者组的太多引用时，作业可能会超出限制数 5 并引发错误。 在这些情况下，可以通过使用以下部分中介绍的解决方案，对多个使用者组使用多个输入，从而进行进一步划分。 

每个分区的读取器数超过数据中心限制（5 个）的情况如下：

* 多个 SELECT 语句：如果使用引用同一个事件中心输入的多个 SELECT 语句，则每个 SELECT 语句都将导致新建一个接收器。
* UNION：使用 UNION 时，可能存在引用同一个事件中心或使用者组的多个输入。
* SELF JOIN：使用 SELF JOIN 操作时，可能会多次引用同一个事件中心。

## <a name="solution"></a>解决方案

下列最佳做法可帮助缓解每个分区的读取器数超过数据中心限制（5 个）的情况。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>使用 WITH 子句将查询拆分为多个步骤

WITH 子句将指定可由查询中的 FROM 子句引用的临时命名结果集。 在单个 SELECT 语句的执行范围内定义 WITH 子句。

例如，与使用此查询相比：

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

请使用此查询：

```
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


## <a name="next-steps"></a>后续步骤
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
