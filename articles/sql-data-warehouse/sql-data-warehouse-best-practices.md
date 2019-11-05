---
title: Azure Synapse Analytics 中 SQL Analytics 的最佳实践（以前称为 SQL DW） |Microsoft Docs
description: 在 Azure Synapse Analytics （以前称为 SQL DW）中开发 SQL Analytics 解决方案的建议和最佳实践。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: bafa4088ee413e28ec6adee3df20f253aeebb861
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499835"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics 中 SQL Analytics 的最佳实践（以前称为 SQL DW）

本文是一系列最佳实践，可帮助你从[SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)部署中获得最佳性能。  本文的目的是为您介绍一些基本指导，并重点介绍重要的重点区域。  每个部分都会向您介绍一个概念，然后指向更深入介绍该概念的更详细的文章。 主题顺序按重要性顺序排列。 

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。 

## <a name="maintain-statistics"></a>维护统计信息
可以将 Azure SQL 数据仓库配置为自动检测和创建有关列的统计信息。  优化器创建的查询计划仅适用于可用的统计信息。  建议为数据库启用 AUTO_CREATE_STATISTICS，并使统计信息每天或每次加载后更新，以确保查询中使用的列的统计信息始终保持最新。 

如果发现更新所有统计信息花费了太长时间，则可能想要更好地选择哪些列需要频繁统计信息更新。 例如，可能想要更新每天都要添加新值的日期列。 **通过对联接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列进行了更新，您将获得最大的好处。**

另请参阅[管理表统计信息][Manage table statistics]、[CREATE STATISTICS][CREATE STATISTICS]、[UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 来监视和优化查询
SQL Analytics 有多个 Dmv，可用于监视查询执行。  以下监视相关文章逐步说明了如何查看正在执行的查询的详细信息。  若要在这些 DMV 中快速找到查询，可在查询中使用 LABEL 选项。

另请参阅[使用 DMV 监视工作负荷][Monitor your workload using DMVs]、[LABEL][LABEL]、[OPTION][OPTION]、[sys.dm_exec_sessions][sys.dm_exec_sessions]、[sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests]、[sys.dm_pdw_request_steps][sys.dm_pdw_request_steps]、[sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests]、[sys.dm_pdw_dms_workers]、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN]、[sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>利用新产品增强功能优化查询性能
- [利用具体化视图进行性能优化](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [具有有序聚集列存储索引的性能优化](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [性能优化和结果集缓存](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>将 INSERT 语句分组为批
使用 INSERT 语句一次性载入小型表、甚至定期重新加载查找，可能与使用类似于 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 的语句一样正常执行。  但是，如果一整天都要加载数千或数百万个行，可能发现单个 INSERT 跟不上要求。  请开发自己的可写入文件的进程，并开发另一个进程定期处理并加载此文件。

另请参阅 [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 快速加载和导出数据

 SQL Analytics 支持通过多种工具（包括 Azure 数据工厂、PolyBase 和 BCP）来加载和导出数据。  对于少量的数据，性能不是那么重要，任何工具都可以满足需求。  但是，当要加载或导出大量数据，或者需要快速的性能时，PolyBase 是最佳选择。  
 
 PolyBase 旨在利用 MPP （大规模并行处理）体系结构，并将数据加载和导出度的速度快于任何其他工具。  可使用 CTAS 或 INSERT INTO 来运行 PolyBase 加载。  **使用 CTAS 可以减少事务日志记录，是加载数据最快的方法。** 
 
  Azure 数据工厂还支持 PolyBase 加载，并且可以实现与 CTAS 类似的性能。  PolyBase 支持各种不同的文件格式，包括 Gzip 文件。  **若要在使用 gzip 文本文件时最大限度地提高吞吐量，请将文件分解为60或更多文件，以最大程度地提高负载并行度。**  若要更快的总吞吐量，请考虑并行加载数据。

另请参阅[加载数据][Load data]、[使用 PolyBase 的指南][Guide for using PolyBase]、 [SQL 池加载模式和策略][Azure SQL Data Warehouse loading patterns and strategies]、使用[Azure][Load Data with Azure Data Factory]数据工厂加载数据、[使用 AZURE 数据工厂移动数据][Move data with Azure Data Factory]、[创建外部文件格式][CREATE EXTERNAL FILE FORMAT]、[创建table as select （CTAS）][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>加载并查询外部表
虽然 Polybase（也称外部表）可以最快速地加载数据，但并不特别适合查询。 Polybase 表目前只支持 Azure blob 文件和 Azure Data Lake 存储。 这些文件并没有任何计算资源的支持。  

因此，SQL 分析无法卸载此工作，因此必须读取整个文件，方法是将其加载到 tempdb，以便读取数据。  因此，如果有多个查询需要查询此数据，则最好是先加载一次此数据，然后让查询使用本地表。

另请参阅 [Guide for using PolyBase][Guide for using PolyBase]（PolyBase 使用指南）

## <a name="hash-distribute-large-tables"></a>哈希分布大型表
默认情况下，表是以轮循机制分布的。  这可让用户更容易开始创建表，而不必确定应该如何分布其表。  轮循机制表在某些工作负荷中执行良好，但大多数情况下，选择分布列的执行性能将更好。  按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询，也就是数据移动操作会被消除。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。  

加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  请参阅以下链接，详细了解如何选择分布列如何提高性能，以及如何在 CREATE TABLE 语句的 WITH 子句中定义分布式表。

另请参阅[表概述][Table overview]、[表数据分布][Table distribution]、[Selecting table distribution][Selecting table distribution]（选择表数据分布）、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>不要过度分区
尽管可以通过分区切换或使用分区消除优化扫描来有效地对数据进行分区，但分区过多可能会减慢查询速度。  通常，高粒度分区策略可能会很好地处理 SQL Server 在 SQL Analytics 中可能无法正常工作。  

如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。  请记住，在幕后，SQL Analytics 会将你的数据分区到60数据库中，因此，如果你创建了包含100个分区的表，这实际上会导致6000个分区。  

每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  请考虑比 SQL Server 上运行良好的数据粒度更低的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区][Table partitioning]

## <a name="minimize-transaction-sizes"></a>最小化事务大小
在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果您的插入需要1小时，如有可能，请将插入分为四个部分，每个部分运行15分钟。  使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  

对于未分区的表，请考虑使用 CTAS 将您要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 所需的时间很长，则运行起来要安全得多，因为它具有最少的事务日志记录，并且可以在需要时快速取消。

另请参阅[了解事务][Understanding transactions]、[优化事务][Optimizing transactions]、[表分区][Table partitioning]、[TRUNCATE TABLE][TRUNCATE TABLE]、[ALTER TABLE][ALTER TABLE]、[Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>缩小查询结果大小  
此步骤可帮助你避免由大型查询结果引起的客户端问题。  您可以编辑您的查询以减少返回的行数。 某些查询生成工具允许您向每个查询添加 "top N" 语法。  您还可以将查询结果 CETAS 到临时表，然后使用 PolyBase 导出来进行下层处理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小
定义 DDL 时，使用可支持数据的最小数据类型将提高查询性能。  这对 CHAR 和 VARCHAR 列尤其重要。  如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述][Table overview]、[表数据类型][Table data types]、[CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>对暂时性数据使用临时堆表
临时登录数据时，可能会发现使用堆表可使整体进程更快。  如果加载数据只是在做运行更多转换之前的预备，将表载入堆表会远快于将数据载入聚集列存储表。  

此外，将数据载入临时表也比将表载入永久存储更快速。  临时表以 "#" 开头，且只有创建它的会话才能访问它，因此它们只能在有限的情况下工作。   堆表在 CREATE TABLE 的 WITH 子句中定义。  如果使用临时表，请记得同时在该临时表上创建统计信息。

另请参阅[临时表][Temporary tables]、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表
聚集列存储索引是一种最有效的方法，可将数据存储在 SQL Analytics 中。  默认情况下，SQL Analytics 中的表创建为聚集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  

当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表分段质量的分步说明，请参阅[表索引][Causes of poor columnstore index quality]一文中的[列存储索引质量不佳的原因][Table indexes]。  

由于高质量列存储段很重要，因此最好使用中型或大型资源类中用于加载数据的用户 Id。 使用较低的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常不会将数据推送到压缩的列存储段，因此，每个表中的行数超过1000000，并且每个 SQL 分析表都分区为60表，因此，列存储表不会对查询产生益处，除非该表包含60000000行以上的行。  小于 6 千万列的表使用列存储索引似乎不太合理，  但也无伤大雅。  

此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表有 100 个分区，则它至少必须拥有 60 亿个行才将受益于聚集列存储（60 个分布区 * 100 个分区 * 1 百万行）。  

如果表在本示例中并没有 60 亿个行，请减少分区数目，或考虑改用堆表。  使用辅助索引配合堆表而不是列存储表，也可能是值得进行的实验，看看是否可以获得较佳的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引][Table indexes]、[列存储索引指南][Columnstore indexes guide]、[重新生成列存储索引][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用较大的资源类来改善查询性能
SQL Analytics 使用资源组作为将内存分配给查询的一种方法。  在该对话框中，所有用户都分配给小型资源类，每个分配的内存为 100 MB。  因为永远将有 60 个分布区，每个分布区有至少 100 MB，整个系统的总内存分配为 6000 MB 或者刚好接近6 GB。  

有些查询，例如大型联接或载入聚集列存储表，将受益于较大的内存分配。  某些查询，例如纯扫描，则不会获得任何好处。  另一方面，使用较大的资源类可减少并发性，因此，在将所有用户移动到大型资源类之前，你需要考虑这种影响。

另请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用较小的资源类来增加并发性
如果你注意到用户查询似乎有较长的延迟，则可能是用户在较大的资源类中运行，并且消耗许多并发槽，导致其他查询排队。  若要确认用户的查询是否被排入队列，请运行 `SELECT * FROM sys.dm_pdw_waits` 来看是否返回了任何行。

另请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)、[sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>其他资源
另请参阅[故障诊断][Troubleshooting]一文，了解常见的问题和解决方案。

如果在本文中找不到要查找的内容，请尝试使用本页面左侧的 "搜索文档" 来搜索所有 Azure Synapse 文档。  [Azure Synapse 论坛][Azure SQL Data Warehouse MSDN Forum]是向其他用户和 Azure Synapse 产品组提出问题的地方。 

我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  如果你希望在 Stack Overflow 上提出问题，我们还会获得一个[Azure Synapse Stack Overflow 论坛][Azure SQL Data Warehouse Stack Overflow Forum]。

最后，请使用[Azure Synapse 反馈][Azure SQL Data Warehouse Feedback]页发出功能请求。  添加请求或对其他请求投赞成票对我们确定功能的优先级有很大的帮助。

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
