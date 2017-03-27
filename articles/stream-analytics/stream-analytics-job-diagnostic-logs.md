---
title: "Azure 流分析诊断日志 | Microsoft 文档"
description: "了解如何在 Microsoft Azure 中通过流分析作业分析诊断日志。"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: f0292efd50721ef58028df778052eb0ed6fcda84
ms.openlocfilehash: 724eba50b7428b0012e8f062e264ce057e2a5287


---
# <a name="job-diagnostic-logs"></a>作业诊断日志

## <a name="introduction"></a>介绍
流分析公开两种类型的日志： 
* [活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)：始终启用，提供针对作业执行的操作的深入信息；
* [诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)：用户可配置的日志，提供作业发生的所有情况的更多深入信息，包括从创建作业开始，到更新、运行作业，直到删除作业为止的所有信息；

## <a name="how-to-enable-diagnostic-logs"></a>如何启用诊断日志
诊断日志默认已**禁用**。 若要启用诊断日志，请执行下列步骤：

登录到 Azure 门户并导航到流式处理作业边栏选项卡，然后使用“监视”下面的“诊断日志”边栏选项卡。

![在边栏选项卡中导航到诊断日志](./media/stream-analytics-job-diagnostic-logs/image1.png)  

然后单击“启用诊断”链接

![启用诊断日志](./media/stream-analytics-job-diagnostic-logs/image2.png)

在打开的诊断中，将状态更改为“打开”。

![更改诊断日志的状态](./media/stream-analytics-job-diagnostic-logs/image3.png)

配置所需的存档目标（存储帐户、事件中心、Log Analytics）并选择想要收集的日志类别（“执行”、“创作”）。 然后保存新的诊断配置。

保存后，配置将在大约 10 分钟后生效，在此之后，日志将开始出现在配置的存档目标中，可在“诊断日志”边栏选项卡中看到：

![在边栏选项卡中导航到诊断日志](./media/stream-analytics-job-diagnostic-logs/image4.png)

[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)页中提供了有关配置诊断的详细信息。

## <a name="diagnostic-logs-categories"></a>诊断日志类别
目前会捕获两种类别的诊断日志：

* **创作：**捕获与作业创作操作相关的日志：创建、添加和删除输入与输出、添加和更新查询、启动和停止作业。
* **执行：**捕获执行作业期间发生的情况。
    * 连接错误；
    * 数据处理错误；
        * 不符合查询定义的事件（字段类型和值不匹配、缺少字段，等等）；
        * 表达式计算错误；
    * 等等

## <a name="diagnostic-logs-schema"></a>诊断日志架构
所有日志以 JSON 格式存储，每个项包含以下通用字符串字段：

Name | 说明
------- | -------
time | 日志时间戳（采用 UTC）
resourceId | 发生操作的资源的 ID，采用大写格式。 包括订阅 ID、资源组和作业名称。 例如： `/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB`
category | 日志类别：`Execution` 或 `Authoring`。
operationName | 被记录的操作的名称。 例如： `Send Events: SQL Output write failure to mysqloutput`
status | 操作状态。 例如，`Failed, Succeeded`。
级别 | 日志级别。 例如： `Error, Warning, Informational`
properties | 日志项的具体详细信息；序列化为 JSON 字符串；请参阅下面的详细信息

### <a name="execution-logs-properties-schema"></a>执行日志属性架构
执行日志包含有关执行流分析作业期间发生的事件的信息。
属性根据事件类型采用不同的架构。 目前包括以下类型：

### <a name="data-errors"></a>数据错误
处理数据时发生的任何错误都属于这种类别的日志。 主要是在执行数据读取、序列化和写入操作期间生成的。 不包括连接错误（被视为泛型事件）。

Name | 说明
------- | -------
源 | 发生错误的作业输入或输出的名称。
消息 | 与错误关联的消息。
类型 | 错误的类型。 例如 `DataConversionError, CsvParserError, ServiceBusPropertyColumnMissingError`，等等。
数据 | 包含用于准确找到错误起源的数据。 将会根据数据大小截断数据。

数据错误根据 **operationName** 值采用以下架构：
* **序列化事件** - 在执行事件读取期间，如果输入中的数据不符合查询架构，将发生这些事件：
    * 事件序列化/反序列化期间类型不匹配：导致出错的字段。
    * 无法读取事件、序列化无效：有关输入数据中发生错误的位置的信息：Blob 输入的 Blob 名称、偏移量和数据样本。
* **发送事件** - 写入操作：导致出错的流事件。

### <a name="generic-events"></a>泛型事件
其他

Name | 说明
-------- | --------
错误 | （可选）错误的信息，通常是异常信息（如果适用）。
消息| 日志消息。
类型 | 消息的类型，映射到内部错误分类：例如 JobValidationError、BlobOutputAdapterInitializationFailure 等。
相关性 ID | 用于唯一标识作业执行的 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 从启动到停止作业期间生成的所有执行日志项都具有相同的“相关 ID”。



## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


