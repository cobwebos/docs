---
title: Azure SQL Edge（预览版）中的数据流式处理
description: 了解 Azure SQL Edge（预览版）中的数据流式处理
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594506"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Azure SQL Edge（预览版）中的数据流式处理

Azure SQL Edge（预览版）提供了两个不同的选项来实现数据流式处理。 

1. 部署在 Azure 中创建的 Azure 流分析 Edge 作业。 有关如何在 Azure SQL Edge 中部署 Azure 流分析 Edge 作业的详细信息，请参阅[部署 Azure 流分析作业](deploy-dacpac.md)。
2. 使用新的“T-SQL 流式处理”功能在 SQL Edge 中创建流式处理作业，而无需在 Azure 中配置流式处理作业。 

虽然可以使用这两个选项来实现 SQL Edge 中的数据流式处理，但强烈建议只使用一个选项。 同时使用这两个选项时，可能会有争用条件，影响数据流式处理操作。

本文档的其余部分提到了“T-SQL 流式处理”这一新功能，它提供实时数据流式处理、分析和事件处理，可同时分析和处理来自多个源的大量快速流数据。 T-SQL 流式处理是使用 Microsoft Azure 中支持 [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)的同一高性能流式处理引擎构建的，支持在边缘上运行的 Azure 流分析提供的一组类似功能集。

与 Azure 流分析一样，T-SQL 流式处理允许识别从多个 IoT 输入源（包括设备、传感器和应用程序）提取的信息中的模式和关系。 这些模式可用于触发操作和启动工作流，例如创建警报、向报告或可视化效果解决方案馈送信息，或存储数据供以后使用。 

下面是可以使用 T-SQL 流式处理的示例场景：

* 分析来自 IoT 设备的实时遥测数据流。
* 实时分析自动驾驶和无人驾驶车辆生成的数据。
* 远程监视和预测性维护高价值的工业或制造业资产。
* 农业或能源农场中的 IoT 传感器读数的异常情况检测和/或模式识别。

## <a name="how-does-t-sql-streaming-work"></a>T-SQL 流式处理的工作原理是什么？

T-SQL 流式处理的工作方式与 [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)完全相同，例如，它使用流式处理作业的概念来处理实时数据流。 

流分析作业包括：

- 流输入 - 流输入定义了要从中读取数据流的数据源的连接。 Azure SQL Edge 目前支持以下流输入类型：
    - Edge 中心
    - Kafka - 对 Kafka 输入的支持目前仅适用于 Azure SQL Edge 的 Intel/AMD64 版本。

- 流输出 - 流输出定义了要写入数据流的数据源的连接。 Azure SQL Edge 目前支持以下流输出类型
    - Edge 中心
    - SQL - SQL 输出可以是 SQL Edge 实例中的本地数据库，也可以是远程 SQL Server 或 Azure SQL 数据库。 
    - Azure Blob 存储

- 流查询 - 流查询定义转换、聚合、筛选器、排序和联接，在将输入流写入流输出之前，需要将这些内容应用到输入流。 流查询基于 Azure 流分析所使用的相同查询语言。 有关 Azure 流分析查询语言的详细信息，请参阅[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

> [!IMPORTANT]
> 与 Azure 流分析不同，T-SQL 流式处理目前不支持[使用引用数据进行查找](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)，或[在流作业中使用 UDF 和 UDA 的引用数据](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)。

> [!NOTE]
> T-SQL 流式处理仅支持 Azure 流分析支持的部分语言外围应用。 有关 Azure 流分析查询语言的详细信息，请参阅[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

## <a name="limitations-and-restrictions"></a>限制和局限

T-SQL 流式处理存在以下限制和局限。 

- 在任何给定时间，只有一个流式处理作业可以处于活动状态。 必须先停止正在执行的作业，然后才能启动另一个作业。
- 每个流式处理作业执行都是单线程的。 如果流式处理作业包含多个查询，则将按串行顺序计算每个查询。

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL Edge（预览版）中创建流分析作业](create-stream-analytics-job.md)
- [查看 Azure SQL Edge（预览版）中与流作业关联的元数据](streaming-catalog-views.md)
- [创建外部流](create-external-stream-transact-sql.md)