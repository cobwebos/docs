---
title: 排查 Azure 流分析中的常见问题
description: 本文介绍 Azure 流分析中的几个常见问题及其排查步骤。
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: e04d1072acee635235b0a5bd8465ca38c861017b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523517"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>流分析的常见问题以及故障排除的步骤

## <a name="troubleshoot-malformed-input-events"></a>排查输入事件格式不当问题

 当流分析作业的输入流包含格式不当的消息时，会导致序列化问题。 例如，JSON 对象中缺少圆括号或大括号，或者时间字段中的时间戳格式不当，都可能导致消息格式不当。 
 
 当流分析作业从某个输入收到格式不当的消息时，它会丢弃该消息并通过警告来通知用户。 警告符号显示在流分析作业的“输入”磁贴上（该警告符号在作业运行状态中始终存在）：

![“输入”磁贴](media/stream-analytics-malformed-events/inputs_tile.png)

若要查看详细信息，请启用诊断日志来查看警告详细信息。 对于格式不当的输入事件，执行日志包含一条类似以下内容的消息：“消息: 无法将来自资源 <blob URI> 的输入事件反序列化为 json”。 

### <a name="troubleshooting-steps"></a>疑难解答步骤

1. 导航到输入磁贴，然后单击以查看警告。

2. 输入详细信息磁贴会显示一组有关问题的详细信息的警告。 下面是一个示例警告消息，警告消息显示了格式不正确的 JSON 数据所在的分区、偏移量和序列号。 

   ![包含偏移量的警告消息](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 若要获取格式不正确的 JSON 数据，请运行 CheckMalformedEvents.cs 代码。 [GitHub 示例存储库](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)中提供了此示例。 此代码读取分区 ID、偏移量并列显位于该偏移位置的数据。 

4. 在阅读数据后，你可以分析并更正序列化格式。 

## <a name="delayed-output"></a>延迟输出

### <a name="first-output-is-delayed"></a>首次输出延迟
启动流分析作业后，将会读取输入事件，但在某些情况下，生成的输出可能出现延迟。

在时态查询元素中使用较大的时间值可能会导致输出延迟。 为了在较长时间范围内生成正确的输出，流作业首先会尽量从最新的时间（最长 7 天前）读取数据，以填充时间范围。 在此期间，只有在完成待处理输入事件的同步读取之后，才生成输出。 当系统升级流作业，从而重启了作业时，可能会出现此问题。 此类升级通常每隔几个月发生一次。 

因此，在设计流分析查询时请保持谨慎。 如果对作业查询语法中的时态元素使用较大的时间窗口（几个小时以上，但不超过 7 天），则在作业启动或重启时，可能会导致首次输出延迟。  

这种首次输出延迟问题的解决方法之一是使用查询并行化技术（数据分区），或者添加更多的流单位，以便在作业同步之前提高吞吐量。  有关详细信息，请参阅[创建流分析作业时的注意事项](stream-analytics-concepts-checkpoint-replay.md)

这些因素会影响生成的首次输出的适时性：

1. 使用开窗聚合（翻转窗口、跳跃窗口和滑动窗口 GROUP BY）
   - 对于翻转窗口或跳跃窗口聚合，结果将在窗口时限结束时生成。 
   - 对于滑动窗口，结果将在事件进入或退出滑动窗口时生成。 
   - 如果打算使用较大的窗口大小（超过 1 小时），最好是选择跳跃窗口或滑动窗口，以便可以更频繁地查看输出。

2. 使用时态联接 (JOIN with DATEDIFF)
   - 在两侧的匹配事件抵达后，立即生成匹配项。
   - 缺少匹配项 (LEFT OUTER JOIN) 的数据在相对于左侧每个事件的 DATEDIFF 窗口结束时生成。

3. 使用时态分析函数（ISFIRST、LAST 和 LAG with LIMIT DURATION）
   - 对于分析函数，输出是针对每个事件生成的，因此不存在延迟。

### <a name="output-falls-behind"></a>输出拖延
在作业正常运行期间，如果你发现该作业的输出不断拖延（延迟越来越长），可以通过检查以下因素来查明根本原因：
- 下游接收器是否受限制
- 上游源是否受限制
- 查询中的处理逻辑是否是计算密集型的

若要查看这些详细信息，请在 Azure 门户中选择流作业，然后选择“作业关系图”。 每个输入都有一个分区积压工作 (backlog) 事件指标。 如果积压工作事件指标不断增大，则表示系统资源受到约束。 原因可能在于输出接收器限制或 CPU 利用率偏高。 有关使用作业关系图的详细信息，请参阅[使用作业关系图进行数据驱动的调试](stream-analytics-job-diagram-with-metrics.md)。

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>处理 Azure SQL 数据库输出中的重复记录

在将 Azure SQL 数据库配置为流分析作业的输出时，Azure SQL 数据库会将记录批量插入到目标表。 一般情况下，Azure 流分析要保证[至少一次发送]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics)到输出接收器，在 SQL 表仅有一个已定义的约束时，仍然可以[实现仅一次的发送]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)到 SQL 输出操作。 

在 SQL 表中设置唯一键约束后，如果在 SQL 表中插入重复记录，则 Azure 流分析会删除重复的记录。 流分析将数据拆分为几个批，并以递归方式插入这些批，直到找到单个重复记录。 如果流作业包含大量重复行，则此拆分和插入过程必须逐个忽略重复项，从而导致效率下降和耗费大量时间。 如果一小时内在活动日志中显示了多个键冲突警告消息，则很可能是 SQL 输出拖慢了整个作业。 

要解决此问题，应通过启用 IGNORE_DUP_KEY 选项[配置索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)（导致键冲突的索引）。 启用该选项允许 SQL 在批量插入时忽略重复值，SQL Azure 只生成警告消息而不是错误消息。 Azure 流分析不会再生成主键冲突错误。

为多种类型的索引配置 IGNORE_DUP_KEY 时，应注意以下几点：

* 不能在主键或使用 ALTER INDEX 的唯一约束上设置 IGNORE_DUP_KEY，需删除并重新创建该索引。  
* 可使用 ALTER INDEX 为唯一索引设置 IGNORE_DUP_KEY 选项，该索引不同于 PRIMARY KEY/UNIQUE 约束，它是使用 CREATE INDEX 或 INDEX 定义创建的。  
* IGNORE_DUP_KEY 不能应用于列存储索引，因为不能对此类索引强制唯一性。  

## <a name="next-steps"></a>后续步骤
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
