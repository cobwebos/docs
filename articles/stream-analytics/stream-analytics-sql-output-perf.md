---
title: 从 Azure 流分析输出到 Azure SQL 数据库
description: 了解如何将数据从 Azure 流分析输出到 SQL Azure，并实现更高的写入吞吐量速率。
services: stream-analytics
author: chetang
ms.author: chetang
manager: katicad
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/21/2018
ms.openlocfilehash: 623d03c96866392ef245fb924cbf6600e7850ffe
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47057647"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>从 Azure 流分析输出到 Azure SQL 数据库

本文提供有关在使用 Azure 流分析将数据载入 SQL Azure 数据库时，如何提高写入吞吐量性能的提示。

Azure 流分析中的 SQL 输出支持使用并行写入作为一个选项。 此选项允许[完全并行](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)作业拓扑，其中，多个输出分区将并行写入到目标表。 但是，在 Azure 流分析中启用此选项可能并不足以提高吞吐量，因为此选项严重依赖于 SQL Azure 数据库配置和表架构。 选择的索引、群集键、索引填充因子和压缩都会对加载表所需的时间产生影响。 有关如何基于内部基准优化 SQL Azure 数据库以提高查询和加载性能的详细信息，请参阅 [SQL 数据库性能指南](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)。

下面是每个服务中一些可以帮助提高解决方案整体吞吐量的配置。

## <a name="azure-stream-analytics"></a>Azure 流分析

- **继承分区** – 使用此 SQL 输出配置选项可以继承先前查询步骤或输入的分区方案。 启用此选项后，写入到基于磁盘的表以及对作业使用[完全并行](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)拓扑时，吞吐量预期会提高。 其他许多[输出](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks)已自动采用此分区。 使用此选项执行批量插入时，还会禁用表锁定 (TABLOCK)。

> [!NOTE] 
> 如果输入分区超过 8 个，则继承输入分区方案可能不是适当的选择。 包含单个标识列和聚集索引的表曾经达到过此上限。 根据架构和选择的索引，观察结果可能有所不同。

- **批大小** - 使用 SQL 输出配置可以根据目标表/工作负荷的性质，在 Azure 流分析 SQL 输出中指定最大批大小。 批大小是随每个批量插入事务一起发送的最大记录数。 在聚集列存储索引中，批大小约为 [100K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)，这可以实现并行化、极简的日志记录和锁定优化。 在基于磁盘的表中，10K（默认值）或更小的值可能最适合解决方案，因为较大的批大小可能在批量插入期间触发锁升级。

- **输入消息优化** – 如果已使用继承分区和批大小进行优化，则增大每个分区的每个消息的输入事件数有助于进一步提高写入吞吐量。 通过输入消息优化，可将 Azure 流分析中的批大小最大提高到指定的批大小，从而提高吞吐量。 可以使用高级事件中心 SKU 中提供的[压缩](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs)或更大消息大小来实现此目的。

## <a name="sql-azure"></a>SQL Azure

- **分区表和索引** – 在包含与分区键（例如 PartitionId）相同的列的表中使用[分区](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) SQL 表和分区索引可以在写入期间明显减少分区之间的争用。 对于分区表，需要在 PRIMARY 文件组中创建[分区函数](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017)和[分区方案](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017)。 这也可以在加载新数据时提高现有数据的可用性。 根据分区的数量，可能会达到日志 IO 限制；升级 SKU 可以提高限制。

- **避免唯一键冲突** – 如果 Azure 流分析活动日志中出现[多个键冲突警告消息](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output)，请确保作业不受唯一约束冲突（在恢复期间可能会发生）的影响。 可以通过在索引中设置 [IGNORE\_DUP\_KEY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-common-troubleshooting-issues#handle-duplicate-records-in-azure-sql-database-output) 选项来避免此问题。

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure 数据工厂和内存中表

- **用作临时表的内存中表** – 使用[内存中表](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)可以大大提高数据加载速度，但内存必须能够装得下这些数据。 基准测试表明，从内存中表批量加载到基于磁盘的表，比使用单个写入器直接批量插入到包含标识列和聚集索引的基于磁盘的表的速度大约要快 10 倍。 若要利用这种批量插入性能，请设置一个[使用 Azure 数据工厂的复制作业](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database)，用于将数据从内存中表复制到基于磁盘的表。

## <a name="summary"></a>摘要

概括而言，使用 Azure 流分析中适用于 SQL 输出的、使作业并行化与 SQL Azure 中分区表相符的分区输出功能，应该可以明显改善吞吐量。 利用 Azure 数据工厂来协调从内存中表到基于磁盘的表的数据移动，可让吞吐量获得指数级的提升。 如果可行，改善消息密度也可能是改善总体吞吐量的主要因素。
