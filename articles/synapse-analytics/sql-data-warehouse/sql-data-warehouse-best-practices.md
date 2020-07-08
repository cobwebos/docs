---
title: Azure Synapse Analytics（以前称为 SQL DW）中 Synapse SQL 池的最佳做法
description: 在 Azure Synapse Analytics（以前称为 SQL DW）中开发 SQL 池解决方案的建议和最佳做法。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6978855e9b32a3842e76d02ef543d86cf0673019
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206642"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics（以前称为 SQL DW）中 Synapse SQL 池的最佳做法

本文包含一系列最佳做法，可帮助你从 [SQL 池](sql-data-warehouse-overview-what-is.md)部署中获得最佳性能。  本文的目的是提供一些基本指导，并侧重介绍重点关注的领域。  

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。

## <a name="maintain-statistics"></a>维护统计信息

可以将 SQL 池配置为自动检测列并为其创建统计信息。  优化器创建的查询计划的好坏取决于可用的统计信息。  

建议为数据库启用 AUTO_CREATE_STATISTICS，并且每天或在每次加载后更新统计信息，以确保查询中使用的列的统计信息始终是最新的。

如果发现更新所有统计信息所需时间太长，可能要更谨慎地选择哪些列需要频繁更新统计信息。 例如，可能想要更新每天都要添加新值的日期列。

> [!TIP]
> 对涉及联接的列、WHERE 子句中使用的列以及在 GROUP BY 中找到的列更新统计信息，可以获得最大效益。

另请参阅[管理表统计信息](sql-data-warehouse-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 来监视和优化查询

SQL 池有多个可用于监视查询执行的 DMV。  《使用 DMV 监视工作负荷》一文逐步详细说明了如何查看正在执行的查询的详细信息。  

若要在这些 DMV 中快速找到查询，可在查询中使用 LABEL 选项。

另请参阅[使用 DMV 监视工作负荷](sql-data-warehouse-manage-monitor.md)、[LABEL](sql-data-warehouse-develop-label.md)、[OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="tune-query-performance-with-new-product-enhancements"></a>利用新产品增强功能优化查询性能

- [通过具体化视图进行性能优化](performance-tuning-materialized-views.md)
- [通过有序聚集列存储索引进行性能优化](performance-tuning-ordered-cci.md)
- [通过结果集缓存进行性能优化](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>将 INSERT 语句分组为批

若要将数据一次性加载到小型表中，使用一个 INSERT 语句即可完成；即使是定期重载某个查找，也可使用 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 之类的语句来完成。  

但是，如果一整天都要加载数千或数百万个行，可能发现单个 INSERT 跟不上要求。  请开发自己的可写入文件的进程，并开发另一个进程定期处理并加载此文件。

另请参阅 [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 快速加载和导出数据

SQL 池支持通过多种工具（包括 Azure 数据工厂、PolyBase 和 BCP）来加载和导出数据。  对于少量的数据，性能不是那么重要，任何工具都可以满足需求。  但是，当要加载或导出大量数据，或者需要快速的性能时，PolyBase 是最佳选择。  

PolyBase 设计为使用 MPP（大规模并行处理）体系结构，因此加载和导出巨量数据的速度比其他任何工具更快。  可使用 CTAS 或 INSERT INTO 来运行 PolyBase 加载。  

> [!TIP]
> 使用 CTAS 可以减少事务日志记录，是加载数据最快的方法。

Azure 数据工厂还支持 PolyBase 加载，并且可以实现与 CTAS 类似的性能。  PolyBase 支持各种不同的文件格式，包括 Gzip 文件。  
  
> [!NOTE]
> 若要在使用 gzip 文本文件时获得最大的吞吐量，请将文件拆分成至少 60 个文件，以便最大程度提高加载的并行度。  若要更快的总吞吐量，请考虑并行加载数据。

另请参阅[加载数据](design-elt-data-loading.md)、[PolyBase 使用指南](guidance-for-loading-data.md)、[SQL 池加载模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../)、[使用 Azure 数据工厂加载数据]( ../../data-factory/load-azure-sql-data-warehouse.md)、[使用 Azure 数据工厂移动数据](../../data-factory/transform-data-using-machine-learning.md)、[CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md)。

## <a name="load-then-query-external-tables"></a>加载并查询外部表

虽然 Polybase（也称外部表）可以最快速地加载数据，但并不特别适合查询。 Polybase 表目前只支持 Azure blob 文件和 Azure Data Lake 存储。 这些文件并没有任何计算资源的支持。  

因此，SQL 池无法卸载此工作，因此必须读取整个文件，方法是将其加载到 tempdb 来读取数据。  因此，如果有多个查询需要查询此数据，则最好是先加载一次此数据，然后让查询使用本地表。

另请参阅 [PolyBase 使用指南](guidance-for-loading-data.md)。

## <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。  这可让用户更容易开始创建表，而不必确定应该如何分布其表。  轮循机制表在某些工作负荷中执行良好，但大多数情况下，选择分布列的执行性能将更好。  

按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询，也就是数据移动操作会被消除。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。  

> [!TIP]
> 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  

有关选择分布列如何能提升性能，以及如何在 CREATE TABLE 语句的 WITH 子句中定义分布式表的更多详细信息，请参阅以下链接。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表分布](sql-data-warehouse-tables-distribute.md)、[选择表分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="do-not-over-partition"></a>不要过度分区

尽管数据分区对于维护数据（通过分区切换）或优化扫描（通过分区排除）很有效，但分区过多会减慢查询速度。  通常，在 SQL Server 上运行良好的高粒度分区策略可能无法在 SQL 池中正常工作。  

如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。  请记住，SQL 池在后台将数据分区成 60 个数据库，因此如果创建包含 100 个分区的表，实际上会产生 6000 个分区。  

每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  请考虑比 SQL Server 上运行良好的数据粒度更低的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](sql-data-warehouse-tables-partition.md)。

## <a name="minimize-transaction-sizes"></a>最小化事务大小

在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果预期 INSERT 需要 1 小时，可能的话，将 INSERT 分成四个部分，每个运行 15 分钟。  使用特殊的最低限度日志记录方案（例如 CTAS、TRUNCATE、DROP TABLE 或 INSERT 到空表）来降低回滚的风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，不要执行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据按月分区，然后将有数据的分区换出成另一个表中的空分区（请参阅 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 示例）。  

对于未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 花费的时间相同，则运行起来要安全得多，因为它的事务日志记录最少，并且可以根据需要快速取消。

另请参阅[了解事务](sql-data-warehouse-develop-transactions.md)、[优化事务](sql-data-warehouse-develop-best-practices-transactions.md)、[表分区](sql-data-warehouse-tables-partition.md)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md)。

## <a name="reduce-query-result-sizes"></a>缩减查询结果大小

此步骤可帮助你避免由大型查询结果引起的客户端问题。  你可以编辑查询以减少返回的行数。 一些查询生成工具允许你向每个查询添加“top N”语法。  你还可以将查询结果 CETAS 到临时表，然后使用 PolyBase 导出进行下层处理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

定义 DDL 时，使用支持数据的最小数据类型将提高查询性能。  此方法对于 CHAR 和 VARCHAR 列尤其重要。  

如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表数据类型](sql-data-warehouse-tables-data-types.md)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="use-temporary-heap-tables-for-transient-data"></a>对暂时性数据使用临时堆表

临时加载数据时，可能会发现使用堆表可让整个过程更快速。  如果加载数据只是在做运行更多转换之前的预备，将表载入堆表会远快于将数据载入聚集列存储表。  

此外，将数据载入临时表也比将表载入永久存储更快速。  临时表以“#”开头，并且只能由创建它的会话访问，因此只能在有限的情况下使用。

堆表在 CREATE TABLE 的 WITH 子句中定义。  如果使用临时表，请记得同时在该临时表上创建统计信息。

另请参阅[临时表](sql-data-warehouse-tables-temporary.md)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

聚集列存储索引是将数据存储在 SQL 池中最有效率的方式之一。  默认情况下，SQL 池中的表创建为聚集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  

当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表分段质量的分步说明，请参阅[表索引](sql-data-warehouse-tables-index.md)一文中的[列存储索引质量不佳的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)。  

由于高质量列存储段很重要，因此可以考虑使用中型或大型资源类中的用户 ID 来加载数据。 使用较低的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常要等到每个表中的行数超过 100 万且每个 SQL 池表分区成 60 个表之后，才会数据推送到压缩的列存储段，根据经验法则，除非列存储表中的行数超过 6000 万，否则该表对于查询没有任何好处。  小于 6 千万列的表使用列存储索引似乎不太合理，  但也无伤大雅。  

此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表有 100 个分区，则它至少必须有 60 亿行才能受益于聚集列存储（60 个分布区 100 个分区 100 万行）。  

如果表在本示例中并没有 60 亿个行，请减少分区数目，或考虑改用堆表。  使用辅助索引配合堆表而不是列存储表，也可能是值得进行的实验，看看是否可以获得较佳的性能。

> [!TIP]
> 查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](sql-data-warehouse-tables-index.md)、[列存储索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[重新生成列存储索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用较大的资源类来改善查询性能

SQL 池使用资源组作为将内存分配给查询的一种方式。  默认情况下，所有用户都分配有小型资源类，此类授予每个分布区 100 MB 内存。  因为永远将有 60 个分布区，每个分布区有至少 100 MB，整个系统的总内存分配为 6000 MB 或者刚好接近6 GB。  

有些查询，例如大型联接或载入聚集列存储表，将受益于较大的内存分配。  某些查询，例如纯扫描，则不会获得任何好处。  但是，使用较大的资源类会降低并发性，因此，在将所有用户转移到大型资源类之前，需要考虑这种影响。

另请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用较小的资源类来增加并发性

如果注意到用户查询似乎长时间延迟，可能是因为用户正在较大资源类中运行并占用大量并发槽位，从而导致其他查询排入队列。  若要确认用户的查询是否被排入队列，请运行 `SELECT * FROM sys.dm_pdw_waits` 来看是否返回了任何行。

另请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)、[sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="other-resources"></a>其他资源

另请参阅[故障诊断](sql-data-warehouse-troubleshoot.md)一文，了解常见的问题和解决方案。

如果在本文中没有找到所需内容，可尝试使用本页面左侧的“搜索文档”来搜索所有 Azure Synapse 文档。  可以在[有关 Azure Synapse 的 Microsoft 问答页](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)上向其他用户和 Azure Synapse 产品组发布问题。 我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  

如果更喜欢在 Stack Overflow 上提问，还可以访问 [Azure Synapse Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。

请使用 [Azure Synapse 反馈](https://feedback.azure.com/forums/307516-sql-data-warehouse)页面提出功能请求。  添加请求或对其他请求投赞成票对我们确定功能的优先级有很大的帮助。
