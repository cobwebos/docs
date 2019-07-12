---
title: Azure 流分析输出的故障排除
description: 本文介绍了对 Azure 流分析作业中的输出连接进行故障排除的技巧。
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a07ac40ad3adda486b5216e83d683e00ec93265d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620791"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure 流分析输出的故障排除

本页介绍了输出连接的常见问题以及如何排查和解决这些问题。

## <a name="output-not-produced-by-job"></a>不是由作业生成的输出 
1.  使用每项输出对应的“测试连接”按钮来验证与输出的连接  。

2.  在“监视器”  选项卡上查看[“监控指标”  ](stream-analytics-monitoring.md)。由于值将进行聚合，因此指标会延迟几分钟。
    - 如果“输入事件数”大于 0，则作业可以读取输入数据。 如果“输入事件”不大于 0，则：
      - 若要查看数据源是否具有有效数据，请使用[服务总线资源管理器](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)。 如果作业使用事件中心作为输入，则会应用此检查。
      - 检查以查看数据序列化格式和数据编码是否符合预期。
      - 如果该作业正在使用事件中心，请检查以查看消息正文是否为 Null  。
      
    - 如果“数据转换错误数”大于 0 且在不断增加，则可能出现以下情况：
      - 输出事件不符合目标接收器的架构。 
      - 事件架构可能与查询中事件的已定义/预期架构不匹配。
      - 事件中某些字段的数据类型可能不符合预期。
      
    - 如果“运行时错误数”大于 0，则表示作业可以接收数据，但在处理查询时将遇到错误。
      - 若要查找错误，请转到[审核日志](../azure-resource-manager/resource-group-audit.md)并筛选“失败”  状态。
      
    - 如果“输入事件数”大于 0 且“输出事件数”等于 0，则会出现以下情况之一：
      - 查询处理导致生成零个输出事件。
      - 事件或其字段可能出现格式错误，导致完成查询处理后生成零个输入。
      - 由于连接或身份验证原因，作业无法将数据推送到输出接收器。
      
    - 对于前文所述的所有这些错误，操作日志消息会解释其他详细信息（包括发生了什么情况），但如果查询逻辑筛选掉了所有事件，则不提供这些详细信息。 如果处理多个事件时出现错误，流分析会将 10 分钟内类型相同的前 3 个错误消息记录在操作日志中。 随后它将阻止其他的相同错误，并且显示一条“错误发生速度过快，已禁止显示此类错误”的消息。
    
## <a name="job-output-is-delayed"></a>作业输出延迟

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

若要查看这些详细信息，请在 Azure 门户中选择流作业，然后选择“作业关系图”。  每个输入都有一个分区积压工作 (backlog) 事件指标。 如果积压工作事件指标不断增大，则表示系统资源受到约束。 原因可能在于输出接收器限制或 CPU 利用率偏高。 有关使用作业关系图的详细信息，请参阅[使用作业关系图进行数据驱动的调试](stream-analytics-job-diagram-with-metrics.md)。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL 数据库输出键冲突警告

在将 Azure SQL 数据库配置为流分析作业的输出时，Azure SQL 数据库会将记录批量插入到目标表。 一般情况下，Azure 流分析要保证[至少一次发送](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)到输出接收器，在 SQL 表仅有一个已定义的约束时，仍然可以[实现仅一次的发送]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)到 SQL 输出操作。 

在 SQL 表中设置唯一键约束后，如果在 SQL 表中插入重复记录，则 Azure 流分析会删除重复的记录。 流分析将数据拆分为几个批，并以递归方式插入这些批，直到找到单个重复记录。 如果流作业包含大量重复行，则此拆分和插入过程必须逐个忽略重复项，从而导致效率下降和耗费大量时间。 如果一小时内在活动日志中显示了多个键冲突警告消息，则很可能是 SQL 输出拖慢了整个作业。 

要解决此问题，应通过启用 IGNORE_DUP_KEY 选项[配置索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)（导致键冲突的索引）。 启用该选项允许 SQL 在批量插入时忽略重复值，SQL Azure 只生成警告消息而不是错误消息。 Azure 流分析不会再生成主键冲突错误。

为多种类型的索引配置 IGNORE_DUP_KEY 时，应注意以下几点：

* 不能在主键或使用 ALTER INDEX 的唯一约束上设置 IGNORE_DUP_KEY，需删除并重新创建该索引。  
* 可使用 ALTER INDEX 为唯一索引设置 IGNORE_DUP_KEY 选项，该索引不同于 PRIMARY KEY/UNIQUE 约束，它是使用 CREATE INDEX 或 INDEX 定义创建的。  
* IGNORE_DUP_KEY 不能应用于列存储索引，因为不能对此类索引强制唯一性。  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>列的名称是小写 Azure Stream analytics
在使用原始兼容级别 (1.0)，Azure Stream Analytics 用于将列名称更改为小写形式。 此行为已在更高版本的兼容性级别中修复。 为了保留这种情况，我们建议客户将移到兼容性级别 1.1 及更高版本。 有关详细信息[Azure Stream Analytics 作业的兼容性级别](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)。


## <a name="get-help"></a>获取帮助

如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
