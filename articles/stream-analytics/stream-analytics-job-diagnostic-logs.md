---
title: 使用诊断日志对 Azure 流分析进行故障排除
description: 本文介绍如何在 Azure 流分析中分析诊断日志。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2019
ms.custom: seodec18
ms.openlocfilehash: 34f994bfca8bdeaffde6732572f47aeaa86b2ac5
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818925"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>使用诊断日志对 Azure 流分析进行故障排除

有时，Azure 流分析作业会意外地停止处理。 因此，能够解决此类事件是很重要的。 故障可能由意外的查询结果、与设备的连接问题或意外的服务中断导致。 流分析中的诊断日志可以帮助用户在问题发生时确定原因并缩短恢复时间。

## <a name="log-types"></a>日志类型

流分析提供两种类型的日志：

* [活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)（始终在线），可深入了解对作业执行的操作。

* [诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)（可配置），可详细了解作业发生的所有情况。 诊断日志在创建作业时开始，并在删除作业时结束。 日志中包含了作业更新和运行期间的事件。

> [!NOTE]
> 可以使用 Azure 存储、Azure 事件中心和 Azure Log Analytics 等服务分析不一致的数据。 将根据这些服务的定价模式进行收费。

## <a name="debugging-using-activity-logs"></a>使用活动日志调试

活动日志默认打开，提供对流分析作业执行的操作的深入见解。 活动日志中存在的信息可帮助找到影响作业的问题的根本原因。 执行以下步骤，在流分析中使用活动日志：

1. 登录 Azure 门户并选择“概述”下的“活动日志”。

   ![流分析活动日志](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 可看到已执行的操作的列表。 导致作业失败的所有操作都有一个红色信息气泡。

3. 单击操作以查看其摘要视图。 此处的信息通常是有限的。 若要了解有关操作的更多详细信息，请单击“JSON”。

   ![流分析活动日志操作摘要](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. 向下滚动到 JSON 的“属性”部分，其中提供导致失败操作的错误的详细信息。 在本示例中，失败的原因在于超出范围的纬度值的运行时错误。

   ![JSON 错误详细信息](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. 可以根据 JSON 中的错误消息采取纠正措施。 在本示例中，检查以确保纬度值介于 -90 度到 90 度之间，并需要将其添加到查询中。

6. 如果活动日志中的错误消息对于确定根本原因没有帮助，请启用诊断日志并使用 Log Analytics。

## <a name="send-diagnostics-to-log-analytics"></a>将诊断数据发送到 Log Analytics

强烈建议打开诊断日志并将它们发送到 Log Analytics。 诊断日志默认已**禁用**。 若要启用诊断日志，请完成以下步骤：

1.  登录 Azure 门户，导航到流分析作业。 在“监视”下，选择“诊断日志”。 然后选择“启用诊断”。

    ![在边栏选项卡中导航到诊断日志](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  在“诊断设置”中创建“名称”，并选中“发送到 Log Analytics”旁边的复选框。 然后添加现有的或创建新的“Log Analytics 工作区”。 选中“日志”下“执行”和“创作”的复选框，以及“指标”下“AllMetrics”的复选框。 单击“ **保存**”。

    ![诊断日志设置](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. 流分析作业开始时，诊断日志会被路由到 Log Analytics 工作区。 导航到 Log Analytics 工作区，并选择“常规”部分下的“日志”。

   ![“常规”部分下的 Log Analytics 日志](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. 可以[编写自己的查询](../azure-monitor/log-query/get-started-portal.md)，以搜索字词、识别趋势、分析模式，以及基于数据提供见解。 例如，可以编写查询，仅筛选出具有“The streaming job failed”（流式传输作业已失败）消息的诊断日志。 来自 Azure 流分析的诊断日志存储在“AzureDiagnostics”表中。

   ![诊断查询和结果](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. 如果具有搜索正确日志的查询，可通过选择“保存”并提供“名称”和“类别”来保存它。 随后可通过选择“新建预警规则”来创建警报。 接下来，指定警报条件。 选择“条件”并输入阈值的值和评估此自定义日志搜索的频率。  

   ![诊断日志搜索查询](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. 选择操作组并指定警报详细信息（如名称和描述），然后才能创建预警规则。 可以将各种作业的诊断日志路由到同一 Log Analytics 工作区。 这让你能够一次性设置适合所有作业的警报。  

## <a name="diagnostics-log-categories"></a>诊断日志类别

目前，我们捕获了两个类别的诊断日志：

* **创作**：捕获与作业创作操作相关的日志事件，例如作业创建、添加和删除输入与输出、添加和更新查询，以及开始或停止作业。

* **执行**：捕获作业执行期间发生的事件。
    * 连接错误
    * 数据处理错误，包括：
        * 不符合查询定义的事件（字段类型和值不匹配、缺少字段等）
        * 表达式计算错误
    * 其他事件和错误

## <a name="diagnostics-logs-schema"></a>诊断日志架构

所有日志均以 JSON 格式存储。 每个项目均具有以下常见字符串字段：

名称 | 说明
------- | -------
time | 日志时间戳（采用 UTC）。
resourceId | 发生操作的资源的 ID，采用大写格式。 其中包括订阅 ID、资源组和作业名称。 例如，**/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**。
category | 日志类别，“执行”或“创作”。
operationName | 被记录的操作的名称。 例如，**发送事件：SQL 输出写入到 mysqloutput 失败**。
status | 操作的状态。 例如，“失败”或“成功”。
级别 | 日志级别。 例如，“错误”、“警告”或“信息性消息”。
属性 | 日志项目的具体详细信息；序列化为 JSON 字符串。 有关详细信息，请参阅本文的以下部分。

### <a name="execution-log-properties-schema"></a>执行日志属性架构

执行日志包含有关执行流分析作业期间发生的事件的信息。 属性的架构因时间类型而异。 目前，我们具有以下类型的执行日志：

### <a name="data-errors"></a>数据错误

作业处理数据期间出现的任何错误都在此日志类别中。 这些日志通常创建于读取数据、序列化和写入操作期间。 这些日志不包括连接错误。 连接错误被视为泛型事件。

名称 | 说明
------- | -------
源 | 发生错误的作业输入或输出的名称。
消息 | 与错误关联的消息。
类型 | 错误类型。 例如，DataConversionError、CsvParserError 和 ServiceBusPropertyColumnMissingError 。
数据 | 包含用于准确找到错误起源的数据。 会根据数据大小截断数据。

数据错误根据 operationName 值采用以下架构：
* 序列化事件。 事件读取操作期间发生序列化事件。 当输入的数据由以下任一原因而不满足查询架构时会发生此类事件：
    * *事件序列化/反序列化期间类型不匹配*：标识导致出错的字段。
    * *无法读取事件，序列化无效*：列出输入数据中发生错误的相关位置信息。 包括用于 blob 输入的 blob 名称、偏移量和数据示例。
* 发送事件。 写入操作期间发生发送事件。 它们标识导致错误的流式处理事件。

### <a name="generic-events"></a>泛型事件

泛型事件包含其他所有情况。

名称 | 说明
-------- | --------
错误 | （可选）错误信息。 通常情况下，这是异常信息（如果存在）。
消息| 日志消息。
类型 | 消息类型。 映射到错误的内部分类。 例如，JobValidationError 或 BlobOutputAdapterInitializationFailure。
相关性 ID | 用于唯一标识作业执行的 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 从作业开始到作业停止期间所有的执行日志条目具有相同的“相关 ID”值。

## <a name="next-steps"></a>后续步骤

* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-real-time-fraud-detection.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
