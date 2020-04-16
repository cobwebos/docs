---
title: 什么是三角洲湖
description: 三角洲湖概述及其作为 Azure 突触分析的一部分的工作方式
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429195"
---
# <a name="what-is-delta-lake"></a>什么是 Delta Lake？

Azure 突触分析与 Linux 基础三角洲湖兼容。 Delta Lake 是一个开源存储层，为 Apache Spark 和大数据工作负载带来 ACID（原子性、一致性、隔离性和持久性）事务。

## <a name="key-features"></a>主要功能

| Feature | 说明 |
| --- | --- |
| **ACID 交易** | 数据湖通常通过多个进程和管道填充，其中一些进程和管道与读取同时写入数据。 在达美湖和添加事务之前，数据工程师必须经过手动错误易发流程，以确保数据完整性。 三角洲湖为数据湖带来了熟悉的 ACID 交易。 它提供序列化，最强的隔离级别。 了解更多关于[潜入三角洲湖：解包交易日志](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)。|
| **可扩展的元数据处理** | 在大数据中，即使是元数据本身也可以是"大数据"。 Delta Lake 将元数据与数据一样处理，利用 Spark 的分布式处理能力来处理其所有元数据。 因此，Delta Lake 可以轻松处理具有数十亿个分区和文件的 PB 级表。 |
| **时间差（数据版本控制）** | "撤消"更改或返回早期版本的能力是事务的关键功能之一。 Delta Lake 提供数据快照，使您能够还原到早期版本的数据，以便进行审核、回滚或重现实验。 了解更多关于[介绍大范围数据湖的三角洲湖时间旅行](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)。 |
| **打开格式** | Apache Parquet 是三角洲湖的基准格式，使您能够利用该格式本机的有效压缩和编码方案。 |
| **统一批处理和流式处理源和接收器** | 增量湖中的表既是批处理表，也是流源和接收器。 流数据引入、批量历史回填和交互式查询都开箱即用。 |
| **架构强制** | 架构实施有助于确保数据类型正确且存在所需的列，防止不良数据导致数据不一致。 有关详细信息，请参阅[潜入三角洲湖：架构实施&演进](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **架构演化** | Delta Lake 使您能够对可自动应用的表架构进行更改，而无需编写迁移 DDL。 有关详细信息，请参阅[潜入三角洲湖：架构实施&演进](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **审计历史记录** | Delta Lake 事务日志记录对数据所做的每个更改的详细信息，从而提供更改的完整审核跟踪。 |
| **更新和删除** | 三角洲湖支持Scala / Java / Python和SQL API的各种功能。 支持合并、更新和删除操作可帮助您满足合规性要求。 有关详细信息，请参阅使用 Python API[在三角洲湖表上发布增量湖 0.4.0 版本](https://delta.io/news/delta-lake-0-4-0-released/)[和简单、可靠的 upsert 和删除](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)，其中包括用于合并、更新和删除 DML 命令的代码段。 |
| **100% 兼容阿帕奇火花 API** | 开发人员可以使用 Delta Lake 及其现有数据管道，只需极少的更改，因为它与现有的 Spark 实现完全兼容。 |

有关完整文档，请参阅[三角洲湖文档页面](https://docs.delta.io/latest/delta-intro.html)

有关详细信息，请参阅[三角洲湖项目](https://lfprojects.org)。

## <a name="next-steps"></a>后续步骤

- [阿帕奇火花文档的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
