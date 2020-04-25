---
title: 使用资源日志对 Azure 流分析进行故障排除
description: 本文介绍如何在 Azure 流分析中分析资源日志。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 40b57af95f9ea4d4212756634c721ddd55f85d7b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127756"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>使用资源日志对 Azure 流分析进行故障排除

有时，Azure 流分析作业会意外地停止处理。 因此，能够解决此类事件是很重要的。 故障可能由意外的查询结果、与设备的连接问题或意外的服务中断导致。 流分析中的资源日志可帮助你确定出现问题的原因并缩短恢复时间。

强烈建议为所有作业启用资源日志，因为这将极大地帮助调试和监视。

## <a name="log-types"></a>日志类型

流分析提供两种类型的日志：

* [活动日志](../azure-monitor/platform/platform-logs-overview.md)（始终在线），可深入了解对作业执行的操作。

* [资源日志](../azure-monitor/platform/platform-logs-overview.md)（可配置），可让你更深入地了解作业发生的所有情况。 资源日志在作业创建时启动，并在删除作业时结束。 日志中包含了作业更新和运行期间的事件。

> [!NOTE]
> 可以使用 Azure 存储、Azure 事件中心和 Azure Monitor 日志等服务分析不一致的数据。 将根据这些服务的定价模式进行收费。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>使用活动日志调试

活动日志在默认情况下处于启用状态，提供对流分析作业执行的操作的深入见解。 活动日志中存在的信息可帮助找到影响作业的问题的根本原因。 执行以下步骤，在流分析中使用活动日志：

1. 登录 Azure 门户并选择“概述”下的“活动日志”********。

   ![流分析活动日志](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 可看到已执行的操作的列表。 导致作业失败的所有操作都有一个红色信息气泡。

3. 单击操作以查看其摘要视图。 此处的信息通常是有限的。 若要了解有关操作的更多详细信息，请单击“JSON”****。

   ![流分析活动日志操作摘要](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. 向下滚动到 JSON 的“属性”部分，其中提供导致失败操作的错误的详细信息****。 在本示例中，失败的原因在于超出范围的纬度值的运行时错误。 流分析作业处理的数据中的差异会导致数据错误。 您可以了解不同的[输入和输出数据错误及其出现的原因](https://docs.microsoft.com/azure/stream-analytics/data-errors)。

   ![JSON 错误详细信息](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. 可以根据 JSON 中的错误消息采取纠正措施。 在本示例中，检查以确保纬度值介于 -90 度到 90 度之间，并需要将其添加到查询中。

6. 如果活动日志中的错误消息对于识别根本原因没有帮助，请启用资源日志并使用 Azure Monitor 日志。

## <a name="send-diagnostics-to-azure-monitor-logs"></a>将诊断发送到 Azure Monitor 日志

强烈建议打开资源日志并将它们发送到 Azure Monitor 日志。 默认情况下，它们处于**关闭状态**。 若要将其打开，请完成以下步骤：

1.  登录 Azure 门户，导航到流分析作业。 在“监视”  下，选择“诊断日志”  。 然后选择“启用诊断”  。

    ![到资源日志的边栏导航栏](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  在“诊断设置”中创建“名称”，并选中“发送到 Log Analytics”旁边的复选框    。 然后添加现有的或创建新的“Log Analytics 工作区”  。 选中“日志”下“执行”和“创作”的复选框，以及“指标”下“AllMetrics”的复选框      。 单击“保存”  。 建议使用与流分析作业位于同一 Azure 区域中的 Log Analytics 工作区，以防产生额外的成本。

    ![资源日志设置](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. 当流分析作业启动时，资源日志会路由到 Log Analytics 工作区。 若要查看作业的资源日志，请在 "**监视**" 部分下选择 "**日志**"。

   ![正在监视的资源日志](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. 流分析提供预定义的查询，使你可以轻松搜索感兴趣的日志。 这 3 个类别是**常规**、**输入数据错误**和**输出数据错误**。 例如，若要查看过去 7 天内所有作业错误的摘要，可以选择适当预定义查询的“运行”  。 

   ![正在监视的资源日志](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![日志结果](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="resource-log-categories"></a>资源日志类别

Azure 流分析捕获两种类别的资源日志：

* **创作**：捕获与作业创作操作相关的日志事件，例如作业创建、添加和删除输入与输出、添加和更新查询，以及开始或停止作业。

* **执行**：捕获作业执行期间发生的事件。
    * 连接错误
    * 数据处理错误，包括：
        * 不符合查询定义的事件（字段类型和值不匹配、缺少字段等）
        * 表达式计算错误
    * 其他事件和错误

## <a name="resource-logs-schema"></a>资源日志架构

所有日志均以 JSON 格式存储。 每个项目均具有以下常见字符串字段：

名称 | 说明
------- | -------
time | 日志时间戳（采用 UTC）。
ResourceId | 发生操作的资源的 ID，采用大写格式。 其中包括订阅 ID、资源组和作业名称。 例如， **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**。
category | 日志类别，“执行”  或“创作”  。
operationName | 被记录的操作的名称。 例如，**发送事件：SQL 输出写入到 mysqloutput 失败**。
状态 | 操作的状态。 例如，“失败”  或“成功”  。
level | 日志级别。 例如，“错误”  、“警告”  或“信息性消息”  。
properties | 日志项目的具体详细信息；序列化为 JSON 字符串。 有关详细信息，请参阅本文的以下部分。

### <a name="execution-log-properties-schema"></a>执行日志属性架构

执行日志包含有关执行流分析作业期间发生的事件的信息。 属性的架构根据事件是数据错误还是一般事件而有所不同。

### <a name="data-errors"></a>数据错误

作业处理数据期间出现的任何错误都在此日志类别中。 这些日志通常创建于读取数据、序列化和写入操作期间。 这些日志不包括连接错误。 连接错误被视为泛型事件。 您可以详细了解各种不同的[输入和输出数据错误](https://docs.microsoft.com/azure/stream-analytics/data-errors)的原因。

名称 | 说明
------- | -------
源 | 发生错误的作业输入或输出的名称。
消息 | 与错误关联的消息。
类型 | 错误类型。 例如，DataConversionError****、CsvParserError**** 和 ServiceBusPropertyColumnMissingError ****。
数据 | 包含用于准确找到错误起源的数据。 会根据数据大小截断数据。

数据错误根据 operationName**** 值采用以下架构：

* 事件读取操作期间发生**序列化事件**。 当输入的数据由以下任一原因而不满足查询架构时会发生此类事件：

   * 事件序列化（反序列化）期间类型不匹配**：标识导致出错的字段。

   * 无法读取事件，序列化无效**：列出输入数据中发生错误的相关位置信息。 包括用于 blob 输入的 blob 名称、偏移量和数据示例。

* 在写操作期间发生**发送事件**。 它们标识导致错误的流式处理事件。

### <a name="generic-events"></a>泛型事件

泛型事件包含其他所有情况。

名称 | 说明
-------- | --------
错误 | （可选）错误信息。 通常情况下，这是异常信息（如果存在）。
消息| 日志消息。
类型 | 消息类型。 映射到错误的内部分类。 例如，JobValidationError**** 或 BlobOutputAdapterInitializationFailure****。
相关性 ID | 用于唯一标识作业执行的 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 从作业开始到作业停止期间所有的执行日志条目具有相同的“相关 ID”**** 值。

## <a name="next-steps"></a>后续步骤

* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-real-time-fraud-detection.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [流分析数据错误](https://docs.microsoft.com/azure/stream-analytics/data-errors)
