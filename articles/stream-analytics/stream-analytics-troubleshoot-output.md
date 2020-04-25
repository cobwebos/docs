---
title: Azure 流分析输出的故障排除
description: 本文介绍了对 Azure 流分析作业中的输出连接进行故障排除的技巧。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: bee40bc30a0ffbdacf8cc7bf88d1512c4fc43fa4
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147951"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure 流分析输出的故障排除

本文介绍 Azure 流分析输出连接的常见问题，以及如何解决输出问题。 许多故障排除步骤需要为流分析作业启用资源和其他诊断日志。 如果未启用资源日志，请参阅[使用资源日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)。

## <a name="the-job-doesnt-produce-output"></a>作业不生成输出

1. 使用每项输出对应的“测试连接”按钮来验证与输出的连接****。
1. 查看 "**监视**" 选项卡上的 "[监视指标](stream-analytics-monitoring.md)"。由于值会进行聚合，因此指标会延迟几分钟。

   * 如果**输入事件**值大于零，则作业可以读取输入数据。 如果**输入事件**值不大于零，则作业的输入有问题。 有关详细信息，请参阅[疑难解答输入连接](stream-analytics-troubleshoot-input.md)。
   * 如果**数据转换错误**值大于零并上升，请参阅[Azure 流分析数据错误](data-errors.md)了解有关数据转换错误的详细信息。
   * 如果**运行时错误**值大于零，则作业将接收数据，但在处理查询时将生成错误。 若要查找错误，请参阅[审核日志](../azure-resource-manager/management/view-activity-logs.md)，并按**失败**状态进行筛选。
   * 如果**输入事件**值大于零，并且**Output 事件**值等于零，则以下语句之一为 true：
      * 查询处理导致生成零个输出事件。
      * 事件或字段可能格式不正确，并在查询处理后生成零个输出。
      * 由于连接或身份验证原因，作业无法将数据推送到输出接收器。

   操作日志消息会解释其他详细信息，包括发生的情况，但查询逻辑筛选掉所有事件的情况除外。 如果处理多个事件时出现错误，则每10分钟收集一次错误。

## <a name="the-first-output-is-delayed"></a>第一个输出延迟

当流分析作业启动时，将读取输入事件。 但在某些情况下，输出可能会有延迟。

在时态查询元素中使用较大的时间值可能会导致输出延迟。 若要在大时间范围内生成正确的输出，流式处理作业会从最新时间读取数据以填充时间范围。 数据可长达七天。 在读取未完成的输入事件之前，不会产生任何输出。 此问题可能会在系统升级流式处理作业时出现。 当发生升级时，作业将重新启动。 此类升级通常每隔几个月发生一次。

设计流分析查询时请谨慎使用。 如果在作业的查询语法中为时态元素使用较大的时间范围，则在作业启动或重新启动时，可能会在第一个输出中导致延迟。 超过几个小时，长达7天，被视为一个较大的时间范围。

此类第一次输出延迟的一项缓解方法是使用查询并行化技术，如对数据进行分区。 或者，你可以添加更多的流式处理单位来提高吞吐量，直到作业捕获。  有关详细信息，请参阅[创建流分析作业时的注意事项](stream-analytics-concepts-checkpoint-replay.md)。

这些因素会影响第一个输出的及时性：

* 使用开窗聚合，如翻转、跳跃和滑动窗口的 GROUP BY 子句：

  * 对于翻转或跳跃窗口聚合，结果在窗口时间范围结束时生成。
  * 对于滑动窗口，结果在事件进入或退出滑动窗口时生成。
  * 如果计划使用较大的窗口大小（如超过一小时），最好选择跳跃或滑动窗口。 这些窗口类型使你可以更频繁地查看输出。

* 使用时态联接，如使用 DATEDIFF 进行联接：
  * 匹配事件的两边到达时立即生成。
  * 对于左侧的每个事件，在 DATEDIFF 窗口的末尾生成了缺少匹配项的数据（如左外部联接）。

* 使用时态分析函数，例如 ISFIRST、LAST 和 LAG，并带有限制持续时间：
  * 对于分析函数，将为每个事件生成输出。 没有延迟。

## <a name="the-output-falls-behind"></a>输出滞后于

在作业的正常操作过程中，输出的延迟时间可能较长且更长。 如果输出落后于这种情况，可以通过检查以下因素来找出根本原因：

* 下游接收器是否受限制
* 上游源是否受限制
* 查询中的处理逻辑是否是计算密集型的

若要查看输出详细信息，请在 Azure 门户中选择流式处理作业，然后选择 "**作业关系图**"。 对于每个输入，每个分区有一个积压工作（backlog）事件指标。 如果指标持续增加，则表明系统资源受到约束。 增加可能是由于输出接收器限制或 CPU 使用率较高引起的。 有关详细信息，请参阅[使用作业关系图进行数据驱动的调试](stream-analytics-job-diagram-with-metrics.md)。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL 数据库输出键冲突警告

将 Azure SQL 数据库配置为流分析作业的输出时，它会将记录大容量插入到目标表中。 通常情况下，Azure 流分析[至少保证一次传递](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)到输出接收器。 当 SQL 表定义了唯一约束时，仍可以[实现一次只传递]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)到 sql 输出。

在 SQL 表上设置唯一键约束时，Azure 流分析会删除重复记录。 它将数据拆分为批处理，并以递归方式插入批处理，直到找到单个重复记录。 Split 和 insert 进程一次忽略一个重复项。 对于包含多个重复行的流式处理作业，此过程效率低下且非常耗时。 如果在前一小时的活动日志中看到了多个键冲突警告消息，则可能是 SQL 输出速度缓慢。

若要解决此问题，请通过启用 IGNORE_DUP_KEY 选项来配置导致键冲突的[索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。 此选项允许 SQL 在大容量插入期间忽略重复值。 Azure SQL 数据库只生成一条警告消息，而不是错误。 因此，Azure 流分析不再产生主键冲突错误。

为多种类型的索引配置 IGNORE_DUP_KEY 时，应注意以下几点：

* 不能对使用 ALTER INDEX 的 primary KEY 或 unique 约束设置 IGNORE_DUP_KEY。 需要删除该索引并重新创建它。  
* 可以通过对唯一索引使用 ALTER INDEX 来设置 IGNORE_DUP_KEY。 此实例与 PRIMARY KEY/UNIQUE 约束不同，并且是使用 CREATE INDEX 或 INDEX 定义创建的。  
* IGNORE_DUP_KEY 选项不适用于列存储索引，因为不能对其强制执行唯一性。  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Azure 流分析中的列名为小写（1.0）

使用原始兼容级别（1.0）时，Azure 流分析会将列名称更改为小写。 此行为已在以后的兼容性级别中修复。 若要保留大小写，请转到兼容性级别1.1 或更高版本。 有关详细信息，请参阅[流分析作业的兼容性级别](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)。

## <a name="get-help"></a>获取帮助

若要获得进一步的帮助，请尝试我们的[Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [开始使用 Azure 流分析](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
