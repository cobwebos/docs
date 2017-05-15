---
title: "使用事件中心接收器调试 Azure 流分析 | Microsoft Docs"
description: "考虑到流分析作业中的事件中心使用者组时的查询最佳做法。"
keywords: "事件中心限制, 使用者组"
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
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 3724b9077b04c767d0b1679caffed2bc5d077113
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017


---

# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>使用事件中心接收器调试 Azure 流分析

可以使用 Azure 流分析中的 Azure 事件中心来从作业中引入或输出作业。 使用事件中心的最佳做法是使用多个使用者组来确保作业的可缩放性。 其中一个原因是，对于特定的输入，流分析作业中读取器的数量会影响单个使用者组中的读取器数量。 接收器的精确数量取决于横向扩展的拓扑逻辑的内部实现详细信息。 接收器的数量不向外部公开。 读取器的数量会在作业启动时或作业升级期间发生更改。

> [!NOTE]
> 当读取器的数量在作业升级期间发生更改时，暂时性警告将被写入到审核日志中。 流分析作业会自动从这些暂时性问题中恢复。

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>每个分区的读取器数超过事件中心限制（5 个）

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
WITH input (
   SELECT * FROM inputEventHub
) as data

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
有关更多帮助，请访问我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-get-started.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

