---
title: SQL 池的最佳实践
description: 在使用 SQL 池时，应了解的建议和最佳实践。
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427791"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure 突触分析中 SQL 池的最佳做法

本文提供了一系列最佳实践，以帮助您在 Azure 突触分析中实现 SQL 池的最佳性能。 下面您将找到构建解决方案时需要关注的基本指南和重要领域。 每部分将介绍一个概念，并提供哪里可以阅读深度介绍的详细文章。

## <a name="sql-pools-loading"></a>SQL 池加载

有关 SQL 池加载指南，请参阅[加载数据的指南](data-loading-best-practices.md)。

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

有关通过暂停和扩展降低成本的详细信息，请参阅[管理计算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="maintain-statistics"></a>维护统计信息

虽然 SQL Server 自动检测和创建或更新列上的统计信息，但 SQL 池需要手动维护统计信息。 您需要维护统计信息，以确保 SQL 池计划得到优化。  优化工具创建的计划只能使用可用的统计信息。

> [!TIP]
> 创建每个列的模板统计信息是开始使用统计信息的简单方式。  

更新统计信息和对数据做重大更改一样重要。  保守的做法是每天或每次加载之后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。

要缩短统计信息维护时间，请选择哪些列具有统计信息，或者需要最频繁的更新。 例如，您可能希望更新日期列，其中可能每天添加新值。 重点介绍联接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列的统计信息。

有关统计信息的其他信息，请参阅[管理表统计信息](develop-tables-statistics.md)、[创建统计信息](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[更新统计信息](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)的文章。

## <a name="group-insert-statements-into-batches"></a>将 INSERT 语句分组为批

一次性加载到带有 INSERT 语句的小表，例如`INSERT INTO MyLookup VALUES (1, 'Type 1')`，根据您的需要，可能是最佳方法。 但是，如果您需要全天加载数千行或数百万行，则单例 INSERTS 可能不是最佳选择。

解决此问题的一种方法是开发一个写入文件的进程，然后开发另一个进程定期加载此文件。 有关详细信息，请参阅[INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 快速加载和导出数据

SQL 池支持通过多个工具（包括 Azure 数据工厂、PolyBase 和 BCP）加载和导出数据。  对于少量的数据，性能不是那么重要，任何工具都可以满足需求。  

> [!NOTE]
> 当您加载或导出大量数据或需要更快的性能时，Polybase 是最佳选择。

可使用 CTAS 或 INSERT INTO 来运行 PolyBase 加载。 CTAS 将最大限度地减少事务日志记录，是加载数据的最快方式。 Azure 数据工厂还支持 PolyBase 负载，并可以实现类似于 CTAS 的性能。 PolyBase 支持各种文件格式，包括 Gzip 文件。

要最大化使用 Gzip 文本文件时的吞吐量，请将文件分解为 60 个或更多文件，以最大化负载的并行性。 若要更快的总吞吐量，请考虑并行加载数据。 有关本节相关主题的其他信息包含在以下文章中：

- [加载数据](data-loading-overview.md)
- [PolyBase 使用指南](data-loading-best-practices.md)
- [Azure SQL 池加载模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [使用 Azure 数据工厂加载数据](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [使用 Azure 数据工厂移动数据](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [创建表作为选择 （CTAS）](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>加载并查询外部表

聚基不是查询的最佳值。 SQL 池的 Polybase 表目前仅支持 Azure blob 文件和 Azure 数据湖存储。 这些文件没有任何支持它们的计算资源。 因此，SQL 池无法卸载此工作，必须通过将其加载到 tempdb 来读取整个文件，以便它可以读取数据。

如果有多个查询用于查询此数据，则最好加载此数据一次，并且让查询使用本地表。 进一步参考库指南包含在[使用 PolyBase 文章的指南](data-loading-best-practices.md)中。

## <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。   此默认值使用户可以轻松地开始创建表，而无需决定如何分配其表。 对于某些工作负载，循环表可能运行得足够多。 但是，在大多数情况下，分发列提供更好的性能。  

由执行"循环"表的列分布的表的最常见示例是联接两个大型事实表。  

例如，如果订单表由 order_id 分发，并且事务表也由 order_id分发，则当您在order_id将订单表加入到交易记录表时，此查询将成为传递查询。 然后消除数据移动操作。 减少步骤意味着加快查询速度。 更少的数据移动也将让查询更快。

> [!NOTE]
> 加载分布式表时，不应对传入数据进行排序。 这样做会减慢您的负载。

下面提供的文章链接将为您提供有关通过选择分发列提高性能的其他详细信息。 此外，您还可以在 CREATE TABLE 语句的 WITH 子句中找到有关如何定义分布式表的信息：

- [表概述](develop-tables-overview.md)
- [表分发](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [选择表数据分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>不要过度分区

尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  通常，在 SQL Server 上可能很好地工作的高粒度分区策略在 SQL 池中可能不起作用。  

如果每个分区的行少于 100 万行，则分区过多可能会降低群集列存储索引的有效性。 SQL 池会自动将数据分区为 60 个数据库。 因此，如果创建具有 100 个分区的表，则结果为 6000 个分区。 每个工作负载都不同，因此最好尝试分区，看看哪些工作负荷最适合您的工作负载。  

需要考虑的一个选项是使用小于使用 SQL Server 实现的粒度。 例如，请考虑使用每周或每月分区，而不是每日分区。

有关分区的详细信息，有关分区的介绍在["表分区"](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中进行了详细介绍。

## <a name="minimize-transaction-sizes"></a>最小化事务大小

在事务中运行的插入、更新和删除语句。 当他们失败时，必须回滚它们。 要降低长时间回滚的可能性，请尽可能最小化事务大小。  通过将 INSERT、更新和删除语句划分为多个部分，可以最大限度地减少事务大小。 例如，如果您有一个"插入"，预计需要 1 小时，则可以将 INSERT 分解为四个部分。 然后，每次运行将缩短为 15 分钟。  

> [!TIP]
> 利用特殊的最小日志记录案例（如 CTAS、TRUNCATE、DROP TABLE 或 INSERT）到空表，以降低回滚风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，您可以每月对数据进行分区，而不是执行 DELETE 语句来删除表中order_date 2001 年 10 月的所有行。 然后，您可以使用另一个表的空分区的数据切换出分区（请参阅 ALTER TABLE 示例）。  

针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 需要相同的时间，则运行会更安全，因为它具有最少的事务日志记录，如果需要，可以快速取消。

有关本节相关内容的更多信息，请参阅以下文章：

- [创建表作为选择 （CTAS）](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [了解事务](develop-transactions.md)
- [优化事务](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [截图表](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [更改表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>减小查询结果的大小

减小查询结果大小有助于避免由大型查询结果引起的客户端问题。  你可以编辑自己的查询以减少返回的行数。 某些查询生成工具允许在每个查询中添加“top N”语法。  还可以通过 CETAS 将查询结果处理成临时表，然后使用 PolyBase 导出进行下层处理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

定义 DDL 时，请使用支持数据的最小数据类型，这样做将提高查询性能。  这项建议对CHAR和VARCHAR列尤其重要。  如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，当需要时将列定义为 VARCHAR，而不是使用 NVARCHAR。

请参阅[表概述](develop-tables-overview.md)、[表数据类型](develop-tables-data-types.md)和[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章，以便更详细地回顾与上述信息相关的基本概念。

## <a name="use-temporary-heap-tables-for-transient-data"></a>对暂时性数据使用临时堆表

当您暂时将数据降落在 SQL 池上时，堆表通常会使整个进程更快。  如果仅在运行更多转换之前加载数据来暂存数据，则将表加载到堆表比将数据加载到群集列存储表要快。  

将数据加载到临时表也会比将表加载到永久存储快得多。  临时表以"+"开头，并且只能由创建它的会话访问。 因此，它们只能在有限的情况下工作。 堆表在 CREATE TABLE 的 WITH 子句中定义。  如果使用临时表，请记得同时在该临时表上创建统计信息。

有关其他指导，请参阅[临时表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[创建表](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[创建 TABLE 作为 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章。

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

群集列存储索引是将数据存储在 SQL 池中的最有效方法之一。  默认情况下，SQL 池中的表创建为群集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  

段质量可以按压缩行组中的行数来衡量。 有关检测和改进群集列存储表的段质量的分步说明，请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)文章中[的"列存储索引质量差的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)"  

由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](resource-consumption-models.md)值意味着需要向加载用户分配较大的资源类。

柱存储表通常不会将数据推送到压缩列存储段，直到每个表超过 100 万行。 每个 SQL 池表被分区为 60 个表。 因此，除非表具有 6000 多万行，否则列存储表不会使查询受益。  

> [!TIP]
> 对于行数少于 6000 万行的表，具有列存储索引可能不是最佳解决方案。  

如果对数据进行分区分区，则每个分区将需要有 100 万行才能从群集列存储索引中获益。  对于具有 100 个分区的表，它需要至少 60 亿行才能从群集列存储中获益（60 个分布*100 个分区 100*个分区 100 万行）。  

如果表没有 60 亿行，则有两个主要选项。 要么减少分区数，要么考虑改用堆表。  可能还值得尝试，看看通过使用具有辅助索引的堆表而不是列存储表是否可以获得更好的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  有关表和列存储索引的详细信息，可在[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列库索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[重建列存储索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)文章中找到。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用较大的资源类来改善查询性能

SQL 池使用资源组作为将内存分配给查询的一种方式。 最初，所有用户都分配给小资源类，该类每个分发分配 100 MB 的内存。  总是有 60 个分布。 每个分布至少为 100 MB。 系统范围内存分配总量为 6，000 MB，或略低于 6 GB。  

有些查询，例如大型联接或载入聚集列存储表，将受益于较大的内存分配。  某些查询（如纯扫描）将看不到任何好处。 利用较大的资源类会影响并发性。 因此，在将所有用户移动到大型资源类之前，您需要牢记这些事实。

有关资源类的其他信息，请参阅[工作负荷管理的资源类](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用较小的资源类来增加并发性

如果您注意到用户查询延迟时间长，则用户可能在更大的资源类中运行。 此方案可促进并发槽的消耗，这可能导致其他查询排队。  要确定用户查询是否排队，请运行`SELECT * FROM sys.dm_pdw_waits`以查看是否返回任何行。

[用于工作负载管理和](../sql-data-warehouse/resource-classes-for-workload-management.md) [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章的资源类将为您提供更多信息。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 来监视和优化查询

SQL 池有多个可用于监视查询执行的 DMV。  下面的监视文章将引导您完成有关如何查看执行查询详细信息的分步说明。  若要在这些 DMV 中快速找到查询，可在查询中使用 LABEL 选项。 有关其他详细信息，请参阅以下列表中包含的文章：

- [使用 DMV 监视工作负荷](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [标签](develop-label.md)
- [选项](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>后续步骤

另请参阅[疑难解答](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)文章，了解常见问题和解决方案。

如果您需要本文中未提供的信息，请使用此页面左侧的"搜索文档"来搜索所有 SQL 池文档。  [SQL 池论坛](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他用户和 SQL 池产品组提出问题的地方。  

我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  如果您喜欢在堆栈溢出上提问，我们也有一个 Azure [SQL 池堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。

对于功能请求，请使用[Azure SQL 池反馈](https://feedback.azure.com/forums/307516-sql-data-warehouse)页。  添加您的请求或上投票的其他请求有助于我们专注于最需要的功能。
