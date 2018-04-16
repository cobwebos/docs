---
title: 使用诊断日志对 Azure 流分析进行故障排除
description: 本文介绍如何在 Azure 流分析中分析诊断日志。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 9001a2962806ee3e691fa448dde162d12c6ecdd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>使用诊断日志对 Azure 流分析进行故障排除

有时，Azure 流分析作业会意外地停止处理。 因此，能够解决此类事件是很重要的。 该事件可能由意外的查询结果、与设备的连接问题或意外的服务中断所导致而成。 流分析中的诊断日志可以帮助用户在问题发生时确定根本原因并缩短恢复时间。

## <a name="log-types"></a>日志类型

流分析提供两种类型的日志： 
* [活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)（始终可用）。 通过活动日志可深入了解对作业执行的操作。
* [诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)（可配置）。 通过诊断日志可详细了解作业发生的所有情况。 诊断日志在创建作业时开始，并在删除作业时结束。 日志中包含了作业更新和运行期间的事件。

> [!NOTE]
> 可以使用 Azure 存储、Azure 事件中心和 Azure Log Analytics 等服务分析不一致的数据。 将根据这些服务的定价模式进行收费。
>

## <a name="turn-on-diagnostics-logs"></a>启用诊断日志

诊断日志默认已**禁用**。 若要启用诊断日志，请完成以下步骤：

1.  登录 Azure 门户，转到“流式处理作业”边栏选项卡。 在“监视”下，选择“诊断日志”。

    ![在边栏选项卡中导航到诊断日志](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  选择“启用诊断”。

    ![启用诊断日志](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  在“诊断设置”页上，针对“状态”选择“打开”。

    ![更改诊断日志的状态](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  设置所需的存档目标（存储帐户、事件中心、Log Analytics）。 然后选择要收集的日志类别（“执行”、“创作”）。 

5.  保存新的诊断配置。

该诊断配置需耗时 10 分钟才会生效。 在此之后，日志将开始在已配置的存档目标（可在“诊断日志”页面中查看）中显示：

![在边栏选项卡中导航到诊断日志 - 存档目标](./media/stream-analytics-job-diagnostic-logs/image4.png)

有关配置诊断的详细信息，请参阅[从 Azure 资源收集和使用诊断数据](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)。

## <a name="diagnostics-log-categories"></a>诊断日志类别

目前，我们捕获了两个类别的诊断日志：

* 创作。 捕获与作业创作操作相关的日志事件：作业创建、添加和删除输入与输出、添加和更新查询、启动和停止作业。
* 执行。 捕获作业执行期间发生的事件：
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
operationName | 被记录的操作的名称。 例如，“发送事件: SQL 输出写入到 mysqloutput 失败”。
status | 操作的状态。 例如，“失败”或“成功”。
级别 | 日志级别。 例如，“错误”、“警告”或“信息性消息”。
属性 | 日志项目的具体详细信息；序列化为 JSON 字符串。 有关详细信息，请参阅以下部分。

### <a name="execution-log-properties-schema"></a>执行日志属性架构

执行日志包含有关执行流分析作业期间发生的事件的信息。 属性的架构因时间类型而异。 目前，我们具有以下类型的执行日志：

### <a name="data-errors"></a>数据错误

作业处理数据期间出现的任何错误都在此日志类别中。 这些日志通常创建于读取数据、序列化和写入操作期间。 这些日志不包括连接错误。 连接错误被视为泛型事件。

名称 | 说明
------- | -------
Source | 发生错误的作业输入或输出的名称。
消息 | 与错误关联的消息。
Type | 错误类型。 例如，DataConversionError、CsvParserError 和 ServiceBusPropertyColumnMissingError 。
数据 | 包含用于准确找到错误起源的数据。 会根据数据大小截断数据。

数据错误根据 operationName 值采用以下架构：
* 序列化事件。 事件读取操作期间发生序列化事件。 当输入的数据由以下任一原因而不满足查询架构时会发生此类事件：
    * 事件序列化（反序列化）期间类型不匹配：标识导致出错的字段。
    * 无法读取事件，序列化无效：列出输入数据中发生错误的相关位置信息。 包括用于 blob 输入的 blob 名称、偏移量和数据示例。
* 发送事件。 写入操作期间发生发送事件。 它们标识导致错误的流式处理事件。

### <a name="generic-events"></a>泛型事件

泛型事件包含其他所有情况。

名称 | 说明
-------- | --------
错误 | （可选）错误信息。 通常，这是异常信息（如果存在）。
消息| 日志消息。
Type | 消息类型。 映射到错误的内部分类。 例如，JobValidationError 或 BlobOutputAdapterInitializationFailure。
相关性 ID | 用于唯一标识作业执行的 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 从作业开始到作业停止期间所有的执行日志条目具有相同的“相关 ID”值。

## <a name="next-steps"></a>后续步骤

* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-real-time-fraud-detection.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
