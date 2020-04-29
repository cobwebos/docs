---
title: SQL 池的最佳实践
description: 使用 SQL 池时应了解的建议和最佳实践。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81427791"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中 SQL 池的最佳实践

本文提供了一系列最佳实践，帮助你在 Azure Synapse Analytics 中实现 SQL 池的最佳性能。 在您生成解决方案时，您会发现要重点关注的基本指导和重要领域。 每部分将介绍一个概念，并提供哪里可以阅读深度介绍的详细文章。

## <a name="sql-pools-loading"></a>正在加载 SQL 池

有关 SQL 池加载指南，请参阅[加载数据的指南](data-loading-best-practices.md)。

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

有关通过暂停和缩放来降低成本的详细信息，请参阅[管理计算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="maintain-statistics"></a>维护统计信息

尽管 SQL Server 自动检测和创建或更新列的统计信息，但 SQL 池需要手动维护统计信息。 你需要维护统计信息，以确保 SQL 池计划经过优化。  优化工具创建的计划只能使用可用的统计信息。

> [!TIP]
> 创建每个列的模板统计信息是开始使用统计信息的简单方式。  

更新统计信息和对数据做重大更改一样重要。  保守的做法是每天或每次加载之后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。

若要缩短统计信息的维护时间，请选择具有统计信息的列，或者需要最频繁更新的列。 例如，你可能想要更新可能每天添加新值的日期列。 专注于联接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列的统计信息。

有关统计信息的详细信息，请参阅[管理表统计信息](develop-tables-statistics.md)、[创建统计](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)信息和[更新统计](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)信息。

## <a name="group-insert-statements-into-batches"></a>将 INSERT 语句分组为批

对于包含 INSERT 语句（如） `INSERT INTO MyLookup VALUES (1, 'Type 1')`的小型表，一次性负载可能是最佳方法，具体取决于你的需求。 但是，如果您需要在一天中加载数千或数百万行，则很可能是单独插入不是最佳的。

解决此问题的一种方法是开发一个写入文件的进程，然后开发另一个进程来定期加载此文件。 有关详细信息，请参阅[INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 快速加载和导出数据

SQL 池支持通过多种工具（包括 Azure 数据工厂、PolyBase 和 BCP）来加载和导出数据。  对于少量的数据，性能不是那么重要，任何工具都可以满足需求。  

> [!NOTE]
> 在加载或导出大量数据时，Polybase 是最佳选择，或者需要更快的性能。

可使用 CTAS 或 INSERT INTO 来运行 PolyBase 加载。 CTAS 将最大程度地减少事务日志记录，是加载数据的最快方法。 Azure 数据工厂还支持 PolyBase 负载，并可实现类似于 CTAS 的性能。 PolyBase 支持各种不同的文件格式，包括 Gzip 文件。

若要在使用 Gzip 文本文件时最大限度地提高吞吐量，请将文件分解为60或更多文件，以最大程度地提高负载并行度。 若要更快的总吞吐量，请考虑并行加载数据。 以下文章中包含了与本部分相关的主题的其他信息：

- [加载数据](data-loading-overview.md)
- [PolyBase 使用指南](data-loading-best-practices.md)
- [Azure SQL 池加载模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [用 Azure 数据工厂加载数据](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [使用 Azure 数据工厂移动数据](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Create table as select （CTAS）](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>加载并查询外部表

Polybase 不适用于查询。 SQL 池的 Polybase 表目前只支持 Azure blob 文件和 Azure Data Lake 存储。 这些文件没有支持这些文件的计算资源。 因此，SQL 池不能卸载此工作，并且必须通过将其加载到 tempdb 来读取整个文件，以便它能够读取数据。

如果有多个查询用于查询此数据，则最好加载一次此数据，然后让查询使用本地表。 其他 Polybase 指南包含在[有关使用 polybase 的指南](data-loading-best-practices.md)一文。

## <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。   利用此默认设置，用户可以轻松地开始创建表，而无需决定如何分散表。 轮循机制表在某些工作负荷上可能会完全执行。 但在大多数情况下，分布列提供更好的性能。  

如果联接两个大型事实数据表，则由遥遥领先远远轮循机制表的列分布的最常见的示例。  

例如，如果您有一个按 order_id 分布的 orders 表，并且通过 order_id 分发了一个事务表，则将 orders 表联接到 order_id 上的事务表时，此查询将成为传递查询。 然后，将消除数据移动操作。 减少步骤意味着加快查询速度。 更少的数据移动也将让查询更快。

> [!NOTE]
> 在加载分布式表时，不应将传入数据按分发键进行排序。 这样做会减缓负载。

下面提供的文章链接将通过选择分布列提供有关提高性能的其他详细信息。 此外，你还将了解有关如何在 CREATE TABLE 语句的 WITH 子句中定义分布式表的信息：

- [表概述](develop-tables-overview.md)
- [表分发](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [选择表数据分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>不要过度分区

尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  通常可以很好地处理 SQL Server 的高粒度分区策略可能无法在 SQL 池上正常工作。  

如果每个分区的行数少于1000000，则分区过多会降低聚集列存储索引的效率。 SQL 池会自动将数据分区到60数据库。 因此，如果您创建一个包含100个分区的表，则结果将是6000个分区。 每个工作负荷都不同，因此最佳建议是尝试进行分区，以了解哪种功能最适合你的工作负荷。  

要考虑的一个选项是使用的粒度低于你使用 SQL Server 实现的粒度。 例如，考虑使用每周或每月分区，而不是每日分区。

有关分区的详细信息，请阅读[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="minimize-transaction-sizes"></a>最小化事务大小

INSERT、UPDATE 和 DELETE 语句在事务中运行。 它们失败时，必须回滚。 为了降低长时间回滚的可能性，请尽可能将事务大小降到最低。  可以通过将 INSERT、UPDATE 和 DELETE 语句分成几个部分来最小化事务大小。 例如，如果您的插入需要1小时，则可以将插入分为四个部分。 然后，每次运行将缩短为15分钟。  

> [!TIP]
> 利用特殊的最小日志记录事例，如 CTAS、截断、删除表或插入空表以降低回滚风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，你可以每月对数据进行分区，而不是执行 DELETE 语句来删除表中的所有行（其中，order_date 在2001年10月）。 然后，可以从另一个表中的空分区的数据切出分区（请参阅 ALTER TABLE 示例）。  

针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 所需的时间很长，则运行起来要安全得多，因为它具有最小的事务日志记录，并且可以在需要时快速取消。

下面的文章中包含有关本部分内容的详细信息：

- [Create table as select （CTAS）](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [了解事务](develop-transactions.md)
- [优化事务](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>减小查询结果的大小

缩小查询结果大小有助于避免由大型查询结果引起的客户端问题。  你可以编辑自己的查询以减少返回的行数。 某些查询生成工具允许在每个查询中添加“top N”语法。  还可以通过 CETAS 将查询结果处理成临时表，然后使用 PolyBase 导出进行下层处理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

定义 DDL 时，使用将支持数据的最小数据类型，因为这样做会提高查询性能。  此建议对于 CHAR 和 VARCHAR 列尤其重要。  如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR （当这只是所需的全部），而不是使用 NVARCHAR。

请参阅[表概述](develop-tables-overview.md)、[表数据类型](develop-tables-data-types.md)和[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章，详细了解与上述信息相关的基本概念。

## <a name="use-temporary-heap-tables-for-transient-data"></a>对暂时性数据使用临时堆表

临时登录 SQL 池上的数据时，堆表通常会使整体进程更快。  如果要加载的数据只是在运行更多转换之前暂存数据，则将表加载到堆表将比将数据加载到聚集列存储表更快。  

将数据加载到临时表还比将表加载到永久存储更快。  临时表以 "#" 开头，且只有创建它的会话才能访问它。 因此，它们只能在有限的情况下工作。 堆表在 CREATE TABLE 的 WITH 子句中定义。  如果使用临时表，请记得同时在该临时表上创建统计信息。

有关其他指南，请参阅[临时表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[CREATE TABLE 作为选择](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)项目。

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

聚集列存储索引是将数据存储在 SQL 池中的最有效方法之一。  默认情况下，SQL 池中的表创建为聚集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  

可以按压缩行组中的行数来度量段质量。 有关检测和改善聚集列存储表的段质量的分步说明，请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的[列存储索引质量不佳的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)。  

由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](resource-consumption-models.md)值意味着需要向加载用户分配较大的资源类。

列存储表通常不会将数据推送到压缩的列存储段，直到每个表有超过1000000行。 每个 SQL 池表都分区为60表。 这样，列存储表将不会对查询产生益处，除非该表的行数超过60000000。  

> [!TIP]
> 对于少于60000000行的表，具有列存储索引可能不是最佳解决方案。  

如果对数据进行分区，则每个分区将需要1000000行才能受益于聚集列存储索引。  对于包含100个分区的表，它至少需要有6000000000行才能受益于聚集列存储（60分布*100 分区*1000000 行）。  

如果表中没有6000000000行，则有两个主要选项。 减少分区数目，或考虑改用堆表。  还值得一提的是，通过使用带有辅助索引的堆表而不是列存储表来了解能否获得更好的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  有关表和列存储索引的详细信息，可以在[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列存储索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和重新[生成列存储索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)一文中找到。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用较大的资源类来改善查询性能

SQL 池使用资源组作为将内存分配给查询的一种方法。 最初，会将所有用户分配给小型资源类，每个分配的内存为 100 MB。  始终存在60个分发版。 每个分布的最小值为 100 MB。 整个系统范围的内存分配为 6000 MB，或刚好小于 6 GB。  

有些查询，例如大型联接或载入聚集列存储表，将受益于较大的内存分配。  某些查询，例如纯扫描，将看不到任何权益。 利用更大的资源类会影响并发性。 因此，在将所有用户移动到大型资源类之前，你需要牢记这些事实。

有关资源类的其他信息，请参阅[用于工作负荷管理的资源类](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用较小的资源类来增加并发性

如果在用户查询中注意到延迟时间较长，则用户可能会在更大的资源类中运行。 此方案升级并发槽的消耗，这可能会导致其他查询排队。  若要确定是否已将用户查询排`SELECT * FROM sys.dm_pdw_waits`入队列，请运行以查看是否返回了任何行。

[用于工作负荷管理](../sql-data-warehouse/resource-classes-for-workload-management.md)和[sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)的资源类将为你提供详细信息。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 来监视和优化查询

SQL 池有多个 Dmv，可用于监视查询执行。  以下监视文章逐步说明了如何查看正在执行的查询的详细信息。  若要在这些 DMV 中快速找到查询，可在查询中使用 LABEL 选项。 有关更多详细信息，请参阅以下列表中包含的文章：

- [使用 DMV 监视工作负荷](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [标识](develop-label.md)
- [选](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>后续步骤

另请参阅[故障排除](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文，了解常见问题和解决方案。

如果你需要本文中未提供的信息，请使用此页面左侧的 "搜索文档" 来搜索所有 SQL 池文档。  [Sql Pool 论坛](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他用户和 SQL Pool 产品组提出问题的地方。  

我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  如果你希望在 Stack Overflow 上提出问题，还会有一个[AZURE SQL 池 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。

对于功能请求，使用[AZURE SQL 池反馈](https://feedback.azure.com/forums/307516-sql-data-warehouse)页。  添加请求或对其他请求投票，有助于我们将重点放在最需要的功能上。
