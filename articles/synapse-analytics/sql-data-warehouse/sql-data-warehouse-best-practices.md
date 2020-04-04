---
title: Azure 突触分析（以前的 SQL DW）中突触 SQL 池的最佳做法
description: 在 Azure 突触分析（以前的 SQL DW）中为 SQL 池开发解决方案的建议和最佳实践。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0a2a49546a31f6d767b5e89348dc6b703278d877
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633627"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure 突触分析（以前的 SQL DW）中突触 SQL 池的最佳做法

本文是一系列最佳实践，可帮助您从[SQL 池](sql-data-warehouse-overview-what-is.md)部署中获得最佳性能。  本文旨在提供一些基本的指导，但也强调了几个重点。  

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。

## <a name="maintain-statistics"></a>维护统计信息

SQL 池可以配置为自动检测和创建列上的统计信息。  优化工具创建的查询计划质量以可用的统计信息为限。  

我们建议您为数据库启用AUTO_CREATE_STATISTICS，并每天或每次加载后更新统计信息，以确保查询中使用的列统计信息始终保持最新。

如果你发现更新所有统计信息需要太长的时间，可以尝试更精心地选择哪些列的统计信息需要频繁更新。 例如，可能想要更新每天都要添加新值的日期列。

> [!TIP]
> 对涉及联接的列、WHERE 子句中使用的列、在 GROUP BY 中找到的列更新统计信息，可以获得最大效益。

另请参阅[管理表统计信息](sql-data-warehouse-tables-statistics.md)、[创建统计信息](https://msdn.microsoft.com/library/ms188038.aspx)和[更新统计信息](https://msdn.microsoft.com/library/ms187348.aspx)。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 来监视和优化查询

SQL 池有多个可用于监视查询执行的 DMV。  使用 DMV 监视工作负荷的文章详细介绍了有关如何查看执行查询详细信息的分步说明。  

若要在这些 DMV 中快速找到查询，可在查询中使用 LABEL 选项。

另请参阅使用 DMV、LABEL、OPTION、sys.dm_exec_sessions、sys.dm_pdw_exec_requests、sys.dm_pdw_request_steps、sys.dm_pdw_sql_requests、sys.dm_pdw_dms_workers、DBCC [PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)和[sys.dm_pdw_waits](https://msdn.microsoft.com/library/mt203893.aspx)[来监视您的工作负载](sql-data-warehouse-manage-monitor.md)。 [LABEL](sql-data-warehouse-develop-label.md) [OPTION](https://msdn.microsoft.com/library/ms190322.aspx) [sys.dm_exec_sessions]( https://msdn.microsoft.com/library/ms176013.aspx) [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx) [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx) [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx) [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx)

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新的产品增强功能优化查询性能

- [通过具体化视图进行性能优化](performance-tuning-materialized-views.md)
- [通过有序聚集列存储索引进行性能优化](performance-tuning-ordered-cci.md)
- [通过结果集缓存进行性能优化](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>将 INSERT 语句分组为批

一次性加载到具有 INSERT 语句的小表，甚至定期重新加载查找，对于您的需求（如`INSERT INTO MyLookup VALUES (1, 'Type 1')`）可能运行良好。  

但是，如果一整天都要加载数千或数百万个行，可能发现单个 INSERT 跟不上要求。  请开发自己的可写入文件的进程，并开发另一个进程定期处理并加载此文件。

另请参阅[INSERT](https://msdn.microsoft.com/library/ms174335.aspx)。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 快速加载和导出数据

SQL 池支持通过多个工具（包括 Azure 数据工厂、PolyBase 和 BCP）加载和导出数据。  对于少量的数据，性能不是那么重要，任何工具都可以满足需求。  但是，当要加载或导出大量数据，或者需要快速的性能时，PolyBase 是最佳选择。  

PolyBase 使用 MPP（大规模并行处理）体系结构，因此加载和导出巨量数据的速度比其他任何工具更快。  可使用 CTAS 或 INSERT INTO 来运行 PolyBase 加载。  

> [!TIP]
> 使用 CTAS 可以减少事务日志记录，是加载数据最快的方法。

Azure 数据工厂还支持 PolyBase 加载，并且可以实现与 CTAS 类似的性能。  PolyBase 支持各种不同的文件格式，包括 Gzip 文件。  
  
> [!NOTE]
> 要在使用 gzip 文本文件时获得最大的吞吐量，请将文件分成 60 个以上的文件让加载有最大化的并行度。  若要更快的总吞吐量，请考虑并行加载数据。

另请参阅[加载数据](design-elt-data-loading.md)、[使用 PolyBase 的指南](guidance-for-loading-data.md)[、SQL 池加载模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../)、[使用 Azure 数据工厂加载数据]( ../../data-factory/load-azure-sql-data-warehouse.md)、使用 Azurehttps://msdn.microsoft.com/library/dn935026.aspx)[数据工厂移动数据](../../data-factory/transform-data-using-machine-learning.md)（和[创建表作为选择 （CTAS）。](sql-data-warehouse-develop-ctas.md)

## <a name="load-then-query-external-tables"></a>加载并查询外部表

虽然 Polybase（也称外部表）可以最快速地加载数据，但并不特别适合查询。 Polybase 表目前仅支持 Azure blob 文件和 Azure 数据湖存储。 这些文件并没有任何计算资源的支持。  

因此，SQL 池无法卸载此工作，因此必须通过将其加载到 tempdb 来读取整个文件才能读取数据。  因此，如果有多个查询需要查询此数据，则最好是先加载一次此数据，然后让查询使用本地表。

另请参阅[使用 PolyBase 的指南](guidance-for-loading-data.md)。

## <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。  这可让用户更容易开始创建表，而不必确定应该如何分布其表。  轮循机制表在某些工作负荷中执行良好，但大多数情况下，选择分布列的执行性能将更好。  

按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询，也就是数据移动操作会被消除。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。  

> [!TIP]
> 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  

有关选择分发列如何提高性能以及如何在 CREATE TABLE 语句的 WITH 子句中定义分布式表的详细信息，请参阅以下链接。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表分布](sql-data-warehouse-tables-distribute.md)、[选择表分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[创建表](https://msdn.microsoft.com/library/mt203953.aspx)、[创建表作为 SELECT](https://msdn.microsoft.com/library/mt204041.aspx)。

## <a name="do-not-over-partition"></a>不要过度分区

尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  通常，粒度分区策略（在 SQL Server 中可能运行良好）在 SQL 池中可能不太工作。  

如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。  请记住，在后台，SQL 池会将您的数据分区到 60 个数据库中，因此，如果创建具有 100 个分区的表，这实际上会导致 6000 个分区。  

每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  请考虑比 SQL Server 上运行良好的数据粒度更低的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](sql-data-warehouse-tables-partition.md)。

## <a name="minimize-transaction-sizes"></a>最小化事务大小

在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果预期 INSERT 需要 1 小时，可能的话，将 INSERT 分成 4 个部分，每个部分运行 15 分钟。  利用特殊的最小日志记录案例（如 CTAS、TRUNCATE、DROP TABLE 或插入空表）来降低回滚风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，而不是执行 DELETE 语句来删除表中order_date在 2001 年 10 月的所有行，您可以每月对数据进行分区，然后用另一个表的空分区的数据切换出分区（请参阅[ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)示例）。  

针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 需要的时间一样长，则较安全的操作，是在它具有极小事务记录的条件下运行它，且必要时可以快速地取消。

另请参阅[了解事务](sql-data-warehouse-develop-transactions.md)、[优化事务](sql-data-warehouse-develop-best-practices-transactions.md)、[表分区](sql-data-warehouse-tables-partition.md)[、TRUNCATE 表](https://msdn.microsoft.com/library/ms177570.aspx)[、ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)和[创建表作为选择 （CTAS）。](sql-data-warehouse-develop-ctas.md)

## <a name="reduce-query-result-sizes"></a>减小查询结果的大小

此步骤帮助你避免大型查询结果造成的客户端问题。  你可以编辑自己的查询以减少返回的行数。 某些查询生成工具允许在每个查询中添加“top N”语法。  还可以通过 CETAS 将查询结果处理成临时表，然后使用 PolyBase 导出进行下层处理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

在定义 DDL 时，使用可支持数据的最小数据类型，能够改善查询性能。  此方法对于 CHAR 和 VARCHAR 列尤其重要。  

如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表数据类型](sql-data-warehouse-tables-data-types.md)、[创建表](https://msdn.microsoft.com/library/mt203953.aspx)。

## <a name="use-temporary-heap-tables-for-transient-data"></a>对暂时性数据使用临时堆表

临时加载数据时，你可能发现使用堆表可让整个过程更快速。  如果加载数据只是在做运行更多转换之前的预备，将表载入堆表会远快于将数据载入聚集列存储表。  

此外，将数据载入临时表也比将表载入永久存储更快速。  临时表以“#”开头，且只有创建它的会话才能访问它，因此只能在受限情况下使用。

堆表在 CREATE TABLE 的 WITH 子句中定义。  如果使用临时表，请记得同时在该临时表上创建统计信息。

另请参阅[临时表](sql-data-warehouse-tables-temporary.md)、[创建表](https://msdn.microsoft.com/library/mt203953.aspx)、[创建表作为选择](https://msdn.microsoft.com/library/mt204041.aspx)。

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

群集列存储索引是将数据存储在 SQL 池中的最有效方法之一。  默认情况下，SQL 池中的表创建为群集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  

当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  压缩行组中的行数可以测量分段质量。  有关检测和改进群集列存储表的段质量的分步说明，请参阅[表索引](sql-data-warehouse-tables-index.md)文章中[的"列存储索引质量差的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)"  

由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常不会将数据推送到压缩列存储段，直到每个表超过 100 万行，并且每个 SQL 池表被划分为 60 个表，因此，根据经验，除非表具有 6000 多行，否则列存储表不会使查询受益。  小于 6 千万列的表使用列存储索引似乎不太合理，  但也无伤大雅。  

此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表具有 100 个分区，则它需要至少 60 亿行才能从群集列存储中获益（60 个分布*100 个分区 100*个分区 100 万行）。  

如果表在本示例中并没有 60 亿个行，请减少分区数目，或考虑改用堆表。  使用辅助索引配合堆表而不是列存储表，也可能是值得进行的实验，看看是否可以获得较佳的性能。

> [!TIP]
> 查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](sql-data-warehouse-tables-index.md)、[列存储索引指南](https://msdn.microsoft.com/library/gg492088.aspx)、[重新生成列存储索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用较大的资源类来改善查询性能

SQL 池使用资源组作为将内存分配给查询的一种方式。  默认情况下，所有用户都分配有小型资源类，此类授予每个分布区 100 MB 内存。  因为永远将有 60 个分布区，每个分布区有至少 100 MB，整个系统的总内存分配为 6000 MB 或者刚好接近6 GB。  

有些查询，例如大型联接或载入聚集列存储表，将受益于较大的内存分配。  某些查询（如纯扫描）不会带来任何好处。  但是，使用较大的资源类会减少并发性，因此在将所有用户移动到大型资源类之前，您需要考虑此影响。

另请参阅[资源类，了解工作负载管理](resource-classes-for-workload-management.md)。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用较小的资源类来增加并发性

如果您注意到用户查询似乎具有长时间的延迟，则可能是您的用户正在较大的资源类中运行，并且正在占用许多并发槽，从而导致其他查询排队。  若要确认用户的查询是否被排入队列，请运行 `SELECT * FROM sys.dm_pdw_waits` 来看是否返回了任何行。

另请参阅[资源类，了解工作负载管理](resource-classes-for-workload-management.md)[，sys.dm_pdw_waits](https://msdn.microsoft.com/library/mt203893.aspx)。

## <a name="other-resources"></a>其他资源

另请参阅[故障诊断](sql-data-warehouse-troubleshoot.md)一文，了解常见的问题和解决方案。

如果在本文中没有找到所需内容，可尝试使用本页面左侧的“搜索文档”来搜索所有 Azure Synapse 文档。  [Azure 突触论坛](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他用户和 Azure Synapse 产品组发布问题的地方。 我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  

如果您喜欢在堆栈溢出上提问，我们也有一个 Azure[突触堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。

请使用[Azure 同步反馈](https://feedback.azure.com/forums/307516-sql-data-warehouse)页发出功能请求。  添加请求或对其他请求投赞成票对我们确定功能的优先级有很大的帮助。
