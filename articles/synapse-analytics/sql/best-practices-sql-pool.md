---
title: 有关 SQL 池的最佳做法
description: 使用 SQL 池时应知道的建议和最佳做法。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8483fd2a1b33330b868fb21d71922377e906e6c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85958415"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中 SQL 池的最佳做法

本文提供了一系列最佳做法，有助于你在 Azure Synapse Analytics 中实现 SQL 池的最佳性能。 下面你将找到构建解决方案时需要关注的基本指南和重要领域。 每部分都介绍了一个概念，并为你指出了更深入介绍该概念的更详细的文章。

## <a name="sql-pools-loading"></a>SQL 池加载

有关 SQL 池加载指南，请参阅[数据加载指南](data-loading-best-practices.md)。

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="maintain-statistics"></a>维护统计信息

尽管 SQL Server 自动检测和创建或更新列的统计信息，但 SQL 池需要手动维护统计信息。 你需要维护统计信息，以确保 SQL 池计划得到优化。  优化工具创建的计划只能使用可用的统计信息。

> [!TIP]
> 创建每个列的模板统计信息是开始使用统计信息的简单方式。  

更新统计信息和对数据做重大更改一样重要。  保守的做法是每天或每次加载之后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。

要缩短统计信息维护时间，你需要选择需要做统计的列，或者需要频繁更新的列。 例如，你可能希望更新每天都要添加新值的日期列。 可专注于对涉及联接的列、WHERE 子句中使用的列、在 GROUP BY 中找到的列做统计。

有关统计信息的其他信息，请参阅[管理表统计信息](develop-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章。

## <a name="group-insert-statements-into-batches"></a>将 INSERT 语句分组为批

一次性加载到具有 INSERT 语句（如 `INSERT INTO MyLookup VALUES (1, 'Type 1')`）的小型表可能是最佳方法，具体取决于你的需求。 但是，如果一整天都要加载数千或数百万个行，可能发现单一实例 INSERT 不是最佳选项。

解决此问题的一种方法是开发两个进程，一个用于写入文件，另一个用于定期加载此文件。 有关详细信息，请参阅 [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 一文。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 快速加载和导出数据

SQL 池支持通过多种工具（包括 Azure 数据工厂、PolyBase 和 BCP）来加载和导出数据。  对于少量的数据，性能不是那么重要，任何工具都可以满足需求。  

> [!NOTE]
> 在加载或导出大量数据，或需要更快的性能时，Polybase 是最佳选择。

可使用 CTAS 或 INSERT INTO 来运行 PolyBase 加载。 CTAS 可以减少事务日志记录，是加载数据最快的方法。 Azure 数据工厂还支持 PolyBase 加载，并且可以实现与 CTAS 类似的性能。 PolyBase 支持各种不同的文件格式，包括 Gzip 文件。

若要在使用 Gzip 文本文件时获得最大的吞吐量，请将文件拆分成至少 60 个文件，以便最大程度提高加载的并行度。 若要更快的总吞吐量，请考虑并行加载数据。 有关本部分相关主题的其他信息，请参阅以下文章：

- [加载数据](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [PolyBase 使用指南](data-loading-best-practices.md)
- [Azure SQL 池加载模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [使用 Azure 数据工厂加载数据](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [使用 Azure 数据工厂移动数据](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [创建表为选择 (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>加载并查询外部表

Polybase 不是查询的最佳选项。 SQL 池的 Polybase 表目前只支持 Azure blob 文件和 Azure Data Lake Storage。 这些文件并没有任何计算资源的支持。 因此，SQL 池无法卸载此工作，必须读取整个文件，方法是将其加载到 tempdb 来读取数据。

如果有多个查询需要查询此数据，则最好是先加载一次此数据，然后让查询使用本地表。 更多 Polybase 指南，请参阅 [PolyBase 使用指南](data-loading-best-practices.md)一文。

## <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。   此默认设置让用户可以轻松开始创建表，而不必确定表的分布方式。 轮循机制表的性能应可满足某些工作负载的要求。 但在大多数情况下，分布列提供的性能更好。  

按列分布的表的性能高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询。 然后，将消除数据移动操作。 减少步骤意味着加快查询速度。 更少的数据移动也将让查询更快。

> [!NOTE]
> 加载分布式表时，不应对传入数据按分布键进行排序。 这样做会减慢负载。

下面提供的文章链接将为你提供有关通过选择分布列提高性能的其他详细信息。 此外，你还可以在 CREATE TABLE 语句的 WITH 子句中找到有关如何定义分布式表的信息：

- [表概述](develop-tables-overview.md)
- [表分布](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [选择表数据分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>不要过度分区

尽管数据分区对于维护数据（通过分区切换）或优化扫描（通过分区排除）很有效，但分区过多会减慢查询速度。  通常，在 SQL Server 上运行良好的高粒度分区策略可能无法在 SQL 池中正常工作。  

如果每个分区的行数少于 1 百万，分区过多会降低聚集列存储索引的效率。 SQL 池会自动将数据分区为 60 个数据库。 因此，如果创建具有 100 个分区的表，将得到 6000 个分区。 每个工作负载都不同，因此最佳建议是尝试不同的分区，找出最适合工作负载的分区。  

可以考虑的一种选择是，使用比你使用 SQL Server 实现的粒度更粗的粒度。 例如，考虑使用每周或每月分区，而不是每日分区。

有关分区的详细信息，请参阅[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="minimize-transaction-sizes"></a>最小化事务大小

INSERT、UPDATE 和 DELETE 语句在事务中运行。 它们失败时，必须回退。 为了降低长时间回退的可能性，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来最小化事务大小。 例如，如果预期 INSERT 需要 1 小时，可将 INSERT 分成四个部分。 然后，每次运行将缩短为 15 分钟。  

> [!TIP]
> 使用特殊的最低限度日志记录方案（例如 CTAS、TRUNCATE、DROP TABLE 或 INSERT 到空表）来降低回退的风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，对于 order_date 为 2001 年 10 月的表，你可以按月对数据进行分区，而不是执行 DELETE 语句来删除该表中的所有行。 然后，可以从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  

对于未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 花费的时间相同，则运行起来要安全得多，因为它的事务日志记录最少，并且可以根据需要快速取消。

如需进一步了解本部分相关内容，请参阅以下文章：

- [创建表为选择 (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [了解事务](develop-transactions.md)
- [优化事务](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>缩减查询结果大小

缩减查询结果大小可帮助你避免由大型查询结果引起的客户端问题。  你可以编辑查询以减少返回的行数。 一些查询生成工具允许你向每个查询添加“top N”语法。  你还可以将查询结果 CETAS 到临时表，然后使用 PolyBase 导出进行下层处理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

定义 DDL 时，请使用支持数据的最小数据类型以便提高查询性能。  此建议对于 CHAR 和 VARCHAR 列尤其重要。  如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，请将列定义为 VARCHAR（当它只需要这样的大小时）而非使用 NVARCHAR。

有关与上述信息相关的基本概念的详细信息，请参阅[表概述](develop-tables-overview.md)、[表数据类型](develop-tables-data-types.md)和 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章。

## <a name="use-temporary-heap-tables-for-transient-data"></a>对暂时性数据使用临时堆表

暂时将数据登录在 SQL 池上时，使用堆表通常会提高整个进程的速度。  如果加载数据只是为了在运行更多转换之前进行预备，将表载入堆表会远快于将数据载入聚集列存储表。  

将数据载入临时表也比将表载入永久存储更快速。  临时表以“#”开头，并且只能由创建它的会话访问。 因此，它们可能仅在有限的情况才起作用。 堆表在 CREATE TABLE 的 WITH 子句中定义。  如果使用临时表，请记得同时在该临时表上创建统计信息。

有关其他指南，请参阅[临时表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章。

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

聚集列存储索引是将数据存储在 SQL 池中最有效率的方式之一。  默认情况下，SQL 池中的表创建为聚集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  

压缩行组中的行数可以测量分段质量。 有关检测和改善聚集列存储表分段质量的分步说明，请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的[列存储索引质量不佳的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)。  

由于高质量列存储段很重要，因此可以考虑使用中型或大型资源类中的用户 ID 来加载数据。 使用较低的[数据仓库单位](resource-consumption-models.md)值意味着需要向加载用户分配较大的资源类。

列存储表通常不会将数据推送到压缩列存储段，除非每个表的行数超过 100 万。 每个 SQL 池表都分区为 60 个表。 因此，除非表具有 6,000 多万行，否则列存储表对查询没有益处。  

> [!TIP]
> 对于少于 6,000 万行的表，列存储索引可能不是最佳解决方案。  

如果对数据进行分区，则每个分区需要具有 100 万行，这样使用聚集列存储索引才有益。  对于有 100 个分区的表，至少必须有 60 亿行才能受益于聚集列存储（60 个分布区 *100 个分区* 100 万行）。  

如果表没有 60 亿行，则有两个主要选项。 减少分区数目，或考虑改用堆表。  使用具有辅助索引的堆表而不是列存储表也许能提升性能，值得试验。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  有关表和列存储索引的详细信息，请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列存储索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[重新生成列存储索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)文章。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用较大的资源类来改善查询性能

SQL 池使用资源组作为将内存分配给查询的一种方式。 最初，所有用户都分配有小型资源类，此类授予每个分布区 100 MB 内存。  总是有 60 个分布区。 每个分布至少为 100 MB。 系统范围内存分配总量为 6,000 MB，或略低于 6 GB。  

有些查询，例如大型联接或载入聚集列存储表，将受益于较大的内存分配。  某些查询，例如纯扫描，则不会获得任何好处。 使用较大的资源类会影响并发性。 在将所有用户移动到大型资源类之前，你需要牢记这些事实。

有关资源类的其他信息，请参阅[用于工作负载管理的资源类](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用较小的资源类可增加并发性

如果你注意到用户查询延迟时间较长，则用户可能在较大的资源类中运行。 这种情况会加速并发槽的消耗，这可能导致其他查询排队。  若要确认用户的查询是否被排入队列，请运行 `SELECT * FROM sys.dm_pdw_waits` 查看是否返回了任何行。

有关详细信息，请参阅[用于工作负载管理的资源类](../sql-data-warehouse/resource-classes-for-workload-management.md)和 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 来监视和优化查询

SQL 池有多个可用于监视查询执行的 DMV。  以下监视相关文章逐步说明了如何查看正在执行的查询的详细信息。  若要在这些 DMV 中快速找到查询，可在查询中使用 LABEL 选项。 有关其他详细信息，请参阅以下列表中包含的文章：

- [使用 DMV 监视工作负荷](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>后续步骤

有关常见问题和解决方案，另请参阅[疑难解答](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

如果本文未提供你需要的问题，请搜索[有关 Azure Synapse 的 Microsoft Q&A 问题页面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)以向其他用户和 SQL 池产品组提出问题。  

我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  如果更喜欢在 Stack Overflow 上提问，还可以访问 [Azure SQL 池 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。

对于功能请求，请使用 [Azure SQL 池反馈](https://feedback.azure.com/forums/307516-sql-data-warehouse)页面。  添加请求或对其他请求投赞成票有助于我们将重点放在最需要的功能上。
