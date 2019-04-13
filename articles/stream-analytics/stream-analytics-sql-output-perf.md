---
title: 从 Azure 流分析输出到 Azure SQL 数据库
description: 了解如何将数据从 Azure 流分析输出到 SQL Azure，并实现更高的写入吞吐量速率。
services: stream-analytics
author: chetanmsft
ms.author: chetanmsft
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: 4be73554df0b6bddaafe3910c80c855e127d79f1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527993"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>从 Azure 流分析输出到 Azure SQL 数据库

本文提供有关在使用 Azure 流分析将数据载入 SQL Azure 数据库时，如何提高写入吞吐量性能的提示。

Azure 流分析中的 SQL 输出支持使用并行写入作为一个选项。 此选项允许[完全并行](stream-analytics-parallelization.md#embarrassingly-parallel-jobs)作业拓扑，其中，多个输出分区将并行写入到目标表。 但是，在 Azure 流分析中启用此选项可能并不足以提高吞吐量，因为此选项严重依赖于 SQL Azure 数据库配置和表架构。 选择的索引、群集键、索引填充因子和压缩都会对加载表所需的时间产生影响。 有关如何基于内部基准优化 SQL Azure 数据库以提高查询和加载性能的详细信息，请参阅 [SQL 数据库性能指南](../sql-database/sql-database-performance-guidance.md)。 与 SQL Azure 数据库并行编写时，无法保证写入顺序。

下面是每个服务中一些可以帮助提高解决方案整体吞吐量的配置。

## <a name="azure-stream-analytics"></a>Azure 流分析

- **继承分区** – 使用此 SQL 输出配置选项可以继承先前查询步骤或输入的分区方案。 启用此选项后，写入到基于磁盘的表以及对作业使用[完全并行](stream-analytics-parallelization.md#embarrassingly-parallel-jobs)拓扑时，吞吐量预期会提高。 其他许多[输出](stream-analytics-parallelization.md#partitions-in-sources-and-sinks)已自动采用此分区。 使用此选项执行批量插入时，还会禁用表锁定 (TABLOCK)。

> [!NOTE] 
> 如果输入分区超过 8 个，则继承输入分区方案可能不是适当的选择。 包含单个标识列和聚集索引的表曾经达到过此上限。 在这种情况下，请考虑使用[INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 在查询中，若要显式指定的输出编写器数。 根据架构和选择的索引，观察结果可能有所不同。

- **批大小** - 使用 SQL 输出配置可以根据目标表/工作负荷的性质，在 Azure 流分析 SQL 输出中指定最大批大小。 批大小是随每个批量插入事务一起发送的最大记录数。 在聚集列存储索引中，批大小约为 [100K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)，这可以实现并行化、极简的日志记录和锁定优化。 在基于磁盘的表中，10K（默认值）或更小的值可能最适合解决方案，因为较大的批大小可能在批量插入期间触发锁升级。

- **输入消息优化** – 如果已使用继承分区和批大小进行优化，则增大每个分区的每个消息的输入事件数有助于进一步提高写入吞吐量。 通过输入消息优化，可将 Azure 流分析中的批大小最大提高到指定的批大小，从而提高吞吐量。 这可以通过使用实现[压缩](stream-analytics-define-inputs.md)或增加事件中心或 Blob 中的输入的消息大小。

## <a name="sql-azure"></a>SQL Azure

- **分区表和索引** – 在包含与分区键（例如 PartitionId）相同的列的表中使用[分区](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) SQL 表和分区索引可以在写入期间明显减少分区之间的争用。 对于分区表，需要在 PRIMARY 文件组中创建[分区函数](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017)和[分区方案](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017)。 这也可以在加载新数据时提高现有数据的可用性。 根据分区的数量，可能会达到日志 IO 限制；升级 SKU 可以提高限制。

- **避免唯一键冲突** – 如果 Azure 流分析活动日志中出现[多个键冲突警告消息](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output)，请确保作业不受唯一约束冲突（在恢复期间可能会发生）的影响。 可以通过在索引中设置 [IGNORE\_DUP\_KEY](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output) 选项来避免此问题。

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure 数据工厂和内存中表

- **内存中表作为临时表**–[内存中表](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)允许非常高速数据加载，但需要容纳在内存中数据。 基准测试表明，从内存中表批量加载到基于磁盘的表，比使用单个写入器直接批量插入到包含标识列和聚集索引的基于磁盘的表的速度大约要快 10 倍。 若要利用这种批量插入性能，请设置一个[使用 Azure 数据工厂的复制作业](../data-factory/connector-azure-sql-database.md)，用于将数据从内存中表复制到基于磁盘的表。

## <a name="avoiding-performance-pitfalls"></a>避免性能缺陷
大容量插入数据是比因为加载与单个插入数据快得多的重复避免传输数据、 分析 insert 语句中，运行该语句中，和发出的事务记录的开销。 相反，更高效的途径使用到存储引擎进行流式处理数据。 此路径的安装程序成本是但是远高于基于磁盘的表中的单个 insert 语句。 保本点通常是大约 100 行，超出的大容量加载方法通常更高效。 

如果传入的事件速率较低，但它可以轻松地创建的批大小中也是低于 100 行，它使大容量插入效率低下，并使用过多磁盘空间的。 若要解决此限制，可以执行以下操作之一：
* 创建 INSTEAD OF[触发器](/sql/t-sql/statements/create-trigger-transact-sql)使用简单的插入的每一行。
* 在上一部分中所述，使用内存中临时表。

写入到非聚集列存储索引 (NCCI)，较小的大容量插入可以在其中创建太多段，可能会崩溃索引时需要执行另一个此类方案。 在这种情况下，建议将改为使用聚集列存储索引。

## <a name="summary"></a>摘要

概括而言，使用 Azure 流分析中适用于 SQL 输出的、使作业并行化与 SQL Azure 中分区表相符的分区输出功能，应该可以明显改善吞吐量。 利用 Azure 数据工厂来协调从内存中表到基于磁盘的表的数据移动，可让吞吐量获得指数级的提升。 如果可行，改善消息密度也可能是改善总体吞吐量的主要因素。
