---
title: 什么是 Delta Lake
description: 增量 Lake 概述，以及它如何作为 Azure Synapse Analytics 的一部分工作
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6a38b61ee03aa4853526586ca60542bd3641b66f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91249393"
---
# <a name="what-is-delta-lake"></a>什么是 Delta Lake

Azure Synapse Analytics 与 Linux Foundation Delta Lake 兼容。 Delta Lake 是一种开源存储层，它将 ACID (原子性、一致性、隔离和持续性) 事务引入 Apache Spark 和大数据工作负荷。

Azure Synapse 中包含的增量 Lake 的当前版本支持 Scala、PySpark 和 .NET 的语言支持。 页面底部有一些链接，可获得更详细的示例和文档。

## <a name="key-features"></a>主要功能

| Feature | 说明 |
| --- | --- |
| **ACID 事务** | 通常，数据 lake 是通过多个进程和管道填充的，其中一些是与读取并发编写的数据。 在增量 Lake 和事务添加之前，数据工程师必须经历手动错误的过程来确保数据完整性。 Delta Lake 将常见的 ACID 事务引入到数据 lake。 它提供 serializability，这是最强大的隔离级别。 [深入了解增量 Lake：解包事务日志](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)。|
| **可缩放的元数据处理** | 在大数据中，即使元数据本身也可以是 "大数据"。 Delta Lake 像数据一样处理元数据，利用 Spark 的分布式处理能力来处理其所有元数据。 因此，Delta Lake 可以轻松处理包含数十亿分区和文件的 pb 级表。 |
| **行程 (数据版本控制) ** | "撤消" 更改或返回到以前的版本是事务的主要功能之一。 Delta Lake 提供数据的快照，使你能够恢复到早期版本的数据以进行审核、回滚或重现试验。 若要深入了解 [大规模数据 lake 的增量 Lake Time 旅行](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)，请参阅。 |
| **开放式格式** | Apache Parquet 是增量 Lake 的基线格式，使你能够利用格式的本机格式的有效压缩和编码方案。 |
| **统一批处理和流式处理源和接收器** | Delta Lake 中的表既是批处理表，也是流式处理源和接收器。 流式传输数据引入、批处理历史回填和交互式查询只需使用即可。 |
| **架构强制执行** | 架构强制有助于确保数据类型正确，并显示所需的列，以防错误数据导致数据不一致。 有关详细信息，请参阅 [深入了解增量 Lake：架构强制 & 进化](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **架构进化** | 通过增量 Lake，你可以更改可自动应用的表架构，而无需编写迁移 DDL。 有关详细信息，请参阅 [深入了解增量 Lake：架构强制 & 进化](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **审核历史记录** | Delta Lake transaction 日志记录对数据所做的每个更改的详细信息，提供对更改的完整审核记录。 |
| **更新和删除** | Delta Lake 支持 Scala/Java/Python 和 SQL Api，适用于多种功能。 支持合并、更新和删除操作有助于满足符合性要求。 有关详细信息，请参阅 [发布增量 lake 0.6.1 release （发布](https://delta.io/news/delta-lake-0-6-1-released/)增量 Lake 0.7 发行版），并使用 Python api 宣布增量 lake 表上  [的增量 lake 版本](https://delta.io/news/delta-lake-0-7-0-released/) 和 [简单、可靠的 upsert 和删除](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)，其中包含用于合并、更新和删除 DML 命令的代码段。 |
| **与 Apache Spark API 兼容的100%** | 开发人员可以在其现有数据管道中使用增量 Lake 来实现最小更改，因为它与现有的 Spark 实现完全兼容。 |

有关完整文档，请参阅 [Delta Lake 文档页](https://docs.delta.io/latest/delta-intro.html)

有关详细信息，请参阅 [Delta Lake 项目](https://github.com/delta-io/delta)。

## <a name="next-steps"></a>后续步骤

- [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
