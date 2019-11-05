---
title: Azure SQL 数据仓库开发最佳做法 | Microsoft Docs
description: 开发 Azure SQL 数据仓库解决方案时应了解的建议和最佳实践。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2debe9000cb60d4dea7395181dfb783fe26586f1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499856"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库开发最佳做法
本文介绍在开发数据仓库解决方案时的指导和最佳做法。 

## <a name="tune-query-performance-with-new-product-enhancements"></a>利用新产品增强功能优化查询性能  
- [利用具体化视图进行性能优化](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [具有有序聚集列存储索引的性能优化](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [性能优化和结果集缓存](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本
若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。 

## <a name="maintain-statistics"></a>维护统计信息
可以将 Azure SQL 数据仓库配置为自动检测和创建有关列的统计信息。  优化器创建的查询计划仅适用于可用的统计信息。  建议为数据库启用 AUTO_CREATE_STATISTICS，并使统计信息每天或每次加载后更新，以确保查询中使用的列的统计信息始终保持最新。 

如果发现更新所有统计信息花费了太长时间，则可能想要更好地选择哪些列需要频繁统计信息更新。 例如，可能想要更新每天都要添加新值的日期列。 **通过对联接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列进行了更新，您将获得最大的好处。**

另请参阅[管理表统计信息][Manage table statistics]、[CREATE STATISTICS][CREATE STATISTICS]、[UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="hash-distribute-large-tables"></a>哈希分布大型表
默认情况下，表是以轮循机制分布的。  这种设计使用户可以轻松地开始创建表，而无需决定如何分布表。  轮循机制表在某些工作负荷中执行良好，但大多数情况下，选择分布列的执行性能将更好。  按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询，也就是数据移动操作会被消除。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。  这种说明仅用于说明这一点。 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  请参阅以下链接，了解有关选择分布列如何提高性能的更多详细信息，以及如何在 CREATE TABLES 语句的 WITH 子句中定义分布式表的更多详细信息。

另请参阅[表概述][Table overview]、[表数据分布][Table distribution]、[Selecting table distribution][Selecting table distribution]（选择表数据分布）、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>不要过度分区
尽管可以通过分区切换或使用分区消除优化扫描来有效地对数据进行分区，但分区过多可能会减慢查询速度。  通常，高粒度分区策略可能会很好地处理 SQL Server 可能无法在 SQL 数据仓库上正常工作。  如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。  请记住，SQL 数据仓库数据在幕后将数据分区成 60 个数据库，因此如果创建包含 100 个分区的表，实际上将导致 6000 个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  请考虑比 SQL Server 上运行良好的数据粒度更低的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区][Table partitioning]

## <a name="minimize-transaction-sizes"></a>最小化事务大小
在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  例如，如果你有一个插入，你预计会需要1小时，如有可能，请将插入分为四个部分，每个部分将在15分钟内运行。  使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 所需的时间很长，则运行起来要安全得多，因为它具有最少的事务日志记录，并且可以在需要时快速取消。

另请参阅[了解事务][Understanding transactions]、[优化事务][Optimizing transactions]、[表分区][Table partitioning]、[TRUNCATE TABLE][TRUNCATE TABLE]、[ALTER TABLE][ALTER TABLE]、[Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小
定义 DDL 时，使用可支持数据的最小数据类型将提高查询性能。  这对 CHAR 和 VARCHAR 列尤其重要。  如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述][Table overview]、[表数据类型][Table data types]、[CREATE TABLE][CREATE TABLE]

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表
聚集列存储索引是将数据存储在 SQL 数据仓库中最有效率的方式之一。  默认情况下，SQL 数据仓库中的表将创建为聚集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表分段质量的分步说明，请参阅[表索引][Causes of poor columnstore index quality]一文中的[列存储索引质量不佳的原因][Table indexes]。  由于高质量列存储段很重要，因此最好使用中型或大型资源类中用于加载数据的用户 Id。 使用较低的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常要等到每个表有超过 1 百万个行之后才会数据推送到压缩的列存储区段，并且每个 SQL 数据仓库表分区成 60 个表，根据经验法则，列存储表对于查询没有好处，除非表有超过 6 千万行。  小于 6 千万列的表使用列存储索引似乎不太合理，  但也无伤大雅。  此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表有 100 个分区，则它至少必须拥有 60 亿个行才将受益于聚集列存储（60 个分布区 * 100 个分区 * 1 百万行）。  如果表在本示例中并没有 60 亿个行，请减少分区数目，或考虑改用堆表。  使用辅助索引配合堆表而不是列存储表，也可能是值得进行的实验，看看是否可以获得较佳的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引][Table indexes]、[列存储索引指南][Columnstore indexes guide]、[重新生成列存储索引][Rebuilding columnstore indexes]

## <a name="next-steps"></a>后续步骤
如果在本文中找不到要查找的内容，请尝试使用本页左侧的 "搜索文档" 来搜索所有 Azure SQL 数据仓库文档。  在[AZURE Sql 数据仓库论坛][Azure SQL Data Warehouse MSDN Forum]中，您可以向其他用户和 SQL 数据仓库产品组提出问题。  我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  若要提问有关堆栈溢出的问题，请访问 [Azure SQL 数据仓库堆栈溢出论坛][Azure SQL Data Warehouse Stack Overflow Forum]。

最后，如需提出功能方面的请求，请使用 [Azure SQL 数据仓库反馈][Azure SQL Data Warehouse Feedback]页。  添加请求或对其他请求投赞成票对我们确定功能的优先级有很大的帮助。

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
