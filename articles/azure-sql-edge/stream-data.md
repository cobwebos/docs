---
title: Azure SQL Edge 中的数据流式处理
description: 了解 Azure SQL Edge 中的数据流式处理
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ca22b3d2c00bfef128455df4ad6b9bb6411f8a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900554"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Azure SQL Edge 中的数据流式处理

Azure SQL Edge 提供了称为 T-SQL 流式处理的数据流式处理功能的原生实现。 它提供实时数据流式处理、分析和事件处理，可同时分析和处理来自多个源的大量快速流数据。 T-SQL 流式处理是使用高性能流式处理引擎构建的，该引擎与为 Microsoft Azure 中的 [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)提供支持的引擎相同。 此功能支持的一组功能与在边缘上运行的 Azure 流分析提供的功能类似。

与流分析一样，T-SQL 流式处理可以识别从多个 IoT 输入源（包括设备、传感器和应用程序）提取的信息中的模式和关系。 你可以使用这些模式来触发操作并启动工作流。 例如，你可以创建警报、将信息馈送到报表或可视化效果解决方案，或者存储数据供以后使用。 

T-SQL 流式处理可以帮助你：

* 分析来自 IoT 设备的实时遥测数据流。
* 对自动驾驶和无人驾驶车辆生成的数据使用实时分析。
* 对高价值的工业或制造业资产使用远程监视和预测性维护。
* 在农业或能源农场中对 IoT 传感器读数使用异常情况检测和模式识别。

## <a name="how-does-t-sql-streaming-work"></a>T-SQL 流式处理的工作原理是什么？

T-SQL 流式处理的工作方式与 [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)完全相同。 例如，它使用流式处理作业的概念来处理实时数据流。 

流分析作业包括：

- **流输入**：此项定义了要从中读取数据流的数据源的连接。 Azure SQL Edge 目前支持以下流输入类型：
    - Edge 中心
    - Kafka（对 Kafka 输入的支持目前仅适用于 Azure SQL Edge 的 Intel/AMD64 版本。）

- **流输出**：此项定义了要将数据流写入到其中的数据源的连接。 Azure SQL Edge 目前支持以下流输出类型
    - Edge 中心
    - SQL（SQL 输出可以是 Azure SQL Edge 实例中的本地数据库，也可以是远程 SQL Server 或 Azure SQL 数据库。） 

- **流查询**：此项定义了转换、聚合、筛选器、排序和联接，在将输入流写入到流输出之前，需要将这些内容应用到输入流。 流查询基于流分析使用的同一查询语言。 有关详细信息，请参阅[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

> [!IMPORTANT]
> 与流分析不同，T-SQL 流式处理目前不支持[使用引用数据进行查找](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)或[在流作业中使用 UDF 和 UDA 的引用数据](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)。

> [!NOTE]
> T-SQL 流式处理仅支持流分析支持的部分语言外围应用。 有关详细信息，请参阅[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

## <a name="limitations-and-restrictions"></a>限制和局限

T-SQL 流式处理存在以下限制和局限。 

- 在任何特定时间，只有一个流式处理作业可以处于活动状态。 必须先停止已在运行的作业，然后才能启动另一个作业。
- 每个流式处理作业执行都是单线程的。 如果流式处理作业包含多个查询，则按串行顺序计算每个查询。
- 当你在 Azure SQL Edge 中停止流式处理作业时，可能会有一些延迟，然后才能开始下一个流式处理作业。 存在此延迟的原因是，基础流式处理进程需要停止以响应停止作业请求，然后再重启以响应启动作业请求。 
- 对于 kafka 流，T-SQL 流式处理最多处理 32 个分区。 尝试配置更大的分区计数将导致错误。 

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL Edge 中创建流分析作业](create-stream-analytics-job.md)
- [查看 Azure SQL Edge 中与流作业关联的元数据](streaming-catalog-views.md)
- [创建外部流](create-external-stream-transact-sql.md)
