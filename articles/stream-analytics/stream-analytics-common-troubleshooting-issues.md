---
title: 对 Azure 流分析中格式不正确的输入事件进行故障排除
description: 如何知道输入数据中的哪个事件导致了流分析作业中的问题
services: stream-analytics
author: jasonwhowell
manager: Kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: fcbb03b4d9aed797cf99c374661c743d39f81276
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>流分析的常见问题以及故障排除的步骤

## <a name="troubleshoot-for-malformed-input-events"></a>对格式不正确的输入事件进行故障排除

当流分析作业的输入流包含格式不正确的消息时，它会导致序列化问题。 格式不正确的消息包括不正确的序列化，例如，JSON 对象中缺少括号，或者时间戳格式不正确。 当流分析作业收到格式不正确的消息时，它会丢弃消息并通过警告来通知用户。 警告符号显示在流分析作业的“输入”磁贴上（该警告符号在作业运行状态中始终存在）：

![“输入”磁贴](media/stream-analytics-malformed-events/inputs_tile.png)

可通过启用诊断日志查看警告的详细信息。 对于格式不正确的输入事件，执行日志包含一条类似以下内容的消息：“消息: 无法将来自资源 <blob URI> 的输入事件反序列化为 json)”。 

### <a name="troubleshooting-steps"></a>疑难解答步骤

1. 导航到输入磁贴，然后单击以查看警告。
2. 输入详细信息磁贴会显示一组有关问题的详细信息的警告。 下面是一个示例警告消息，警告消息显示了格式不正确的 JSON 数据所在的分区、偏移量和序列号。 

   ![包含偏移量的警告消息](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 要获取正确格式的 JSON 数据，可运行 CheckMalformedEvents.cs 代码，然后从 [GitHub 示例存储库](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH) 中获取 JSON 数据。 此代码读取分区 Id、偏移量并打印位于该偏移的数据。 

4. 在阅读数据后，你可以分析并更正序列化格式。 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>使用 Azure SQL 数据库作为流分析作业的输出时，处理重复记录

在将 Azure SQL 数据库配置为流分析作业的输出时，Azure SQL 数据库会将记录批量插入到目标表。 一般情况下，Azure 流分析要保证[至少一次发送]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics)到输出接收器，在 SQL 表仅有一个已定义的约束时，仍然可以[实现仅一次的发送]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)到 SQL 输出操作。 

在 SQL 表上安装了唯一的键约束，并且 SQL 表中插入了重复记录后，Azure 流分析将会删除这些重复记录，删除方式为：将数据拆分为几个批次，然后以递归方式插入这些批次，直到找到单个重复记录。 如果管道中有大量重复行，拆分和以递归方式插入数据并逐个忽略重复数据的过程将会耗费大量时间。 如果一小时内在活动日志中显示了多个键冲突警告消息，则很可能是 SQL 输出拖慢了整个作业。 要解决此问题，应通过启用 IGNORE_DUP_KEY 选项[配置索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)（导致键冲突的索引）。 启用该选项允许 SQL 在批量插入时忽略重复值，SQL Azure 只生成警告消息而不是错误消息。 Azure 流分析不会再生成主键冲突错误。

为多种类型的索引配置 IGNORE_DUP_KEY 时，应注意以下几点：

* 不能在主键或使用 ALTER INDEX 的唯一约束上设置 IGNORE_DUP_KEY，需删除并重新创建该索引。  
* 可使用 ALTER INDEX 为唯一索引设置 IGNORE_DUP_KEY 选项，该索引不同于 PRIMARY KEY/UNIQUE 约束，它是使用 CREATE INDEX 或 INDEX 定义创建的。  
* IGNORE_DUP_KEY 不能应用于列存储索引，因为不能对此类索引强制唯一性。  

## <a name="next-steps"></a>后续步骤

* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

