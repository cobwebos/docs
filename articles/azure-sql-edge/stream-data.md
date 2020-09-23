---
title: Azure SQL Edge 中的数据流
description: 了解 Azure SQL Edge 中的数据流。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ca22b3d2c00bfef128455df4ad6b9bb6411f8a13
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900554"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Azure SQL Edge 中的数据流

Azure SQL Edge 提供名为 T-sql 流式处理的数据流式处理功能的本机实现。 它提供实时数据流式处理、分析和事件处理，可同时分析和处理来自多个源的大量快速流式处理数据。 T-sql 流式处理是通过使用在 Microsoft Azure 中支持 [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) 的同一高性能流式处理引擎生成的。 此功能支持一组类似的功能，这些功能由在边缘上运行的 Azure 流分析提供。

与流分析一样，T-sql 流可识别从许多 IoT 输入源（包括设备、传感器和应用程序）中提取的信息中的模式和关系。 您可以使用这些模式来触发操作并启动工作流。 例如，您可以创建警报、将信息馈送到报表或可视化解决方案，或存储数据以供以后使用。 

T-sql 流式处理可帮助你：

* 分析来自 IoT 设备的实时遥测数据流。
* 使用从自治和无人驾驶车辆生成的实时数据分析。
* 使用高价值工业或制造资产的远程监视和预测性维护。
* 在农业或能源场内使用 IoT 传感器读数的异常检测和模式识别。

## <a name="how-does-t-sql-streaming-work"></a>T-sql 流式处理的工作原理是什么？

T-sql 流式处理的工作方式与 [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)完全相同。 例如，它使用流式处理 *作业* 的概念来处理实时数据流。 

流分析作业包括：

- **流输入**：它定义了与数据源的连接，以从其读取数据流。 Azure SQL Edge 目前支持以下流输入类型：
    - Edge 中心
    - Kafka (对 Kafka 输入的支持目前仅适用于 Azure SQL Edge 的 Intel/AMD64 版本。 ) 

- **流输出**：这定义了与数据源的连接，以写入数据流。 Azure SQL Edge 目前支持以下流输出类型
    - Edge 中心
    - SQL (SQL 输出可以是 Azure SQL Edge 实例中的本地数据库，也可以是远程 SQL Server 或 Azure SQL 数据库。 )  

- **流查询**：定义要应用到输入流的转换、聚合、筛选器、排序和联接，然后将其写入流输出。 流查询与流分析所使用的查询语言相同。 有关详细信息，请参阅 [流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

> [!IMPORTANT]
> 与流分析不同，t-sql 流式处理当前不支持 [使用引用数据来进行查找](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) ，也不支持 [在流作业中使用 UDF 和 UDA](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)。

> [!NOTE]
> T-sql 流式处理仅支持流分析支持的部分语言界面区域。 有关详细信息，请参阅 [流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

## <a name="limitations-and-restrictions"></a>限制和局限

以下限制和限制适用于 T-sql 流式处理。 

- 在任意特定时间，只能有一个流式处理作业处于活动状态。 必须先停止正在运行的作业，然后才能启动另一个作业。
- 每个流式处理作业执行都是单线程的。 如果流式处理作业包含多个查询，则按序列顺序对每个查询进行计算。
- 当你在 Azure SQL Edge 中停止流式处理作业时，可能会有一些延迟，然后才能开始下一个流式处理作业。 引入此延迟的原因是，基础流式处理进程需要停止才能响应停止作业请求，然后在响应启动作业请求时重新启动。 
- 对于 kafka 流，t-sql 流式处理最多32个分区。 尝试配置更大的分区计数将导致错误。 

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL Edge 中创建流分析作业 ](create-stream-analytics-job.md)
- [查看 Azure SQL Edge 中与流作业关联的元数据 ](streaming-catalog-views.md)
- [创建外部流](create-external-stream-transact-sql.md)
