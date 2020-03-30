---
title: 开发最佳做法
description: 在为 Synapse SQL 池开发解决方案时，应了解的建议和最佳实践。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a4902d2817c98b002b9767ad55d7ac60f5cb0c93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350712"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Synapse SQL 池的开发最佳实践
本文介绍在开发数据仓库解决方案时的指导和最佳做法。 

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新的产品增强功能优化查询性能  
- [通过具体化视图进行性能优化](performance-tuning-materialized-views.md)
- [通过有序聚集列存储索引进行性能优化](performance-tuning-ordered-cci.md)
- [通过结果集缓存进行性能优化](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本
若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。 

## <a name="maintain-statistics"></a>维护统计信息
Synapse SQL 池可以配置为自动检测和创建列上的统计信息。  优化工具创建的查询计划质量以可用的统计信息为限。  建议为数据库启用 AUTO_CREATE_STATISTICS，并使统计信息每日更新或者在每次加载后更新，以确保查询中使用的有关列的统计信息始终保持最新。 

如果你发现更新所有统计信息需要太长的时间，可以尝试更精心地选择哪些列的统计信息需要频繁更新。 例如，可能想要更新每天都要添加新值的日期列。 **对涉及联接的列、WHERE 子句中使用的列、在 GROUP BY 中找到的列更新统计信息，可以获得最大效益。**

另请参阅[管理表统计信息](sql-data-warehouse-tables-statistics.md)、[CREATE STATISTICS](sql-data-warehouse-tables-statistics.md)、[UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#updating-statistics)

## <a name="hash-distribute-large-tables"></a>哈希分布大型表
默认情况下，表是以轮循机制分布的。  此设计可让用户更容易开始创建表，而不必确定应该如何分布其表。  轮循机制表在某些工作负荷中执行良好，但大多数情况下，选择分布列的执行性能将更好。  按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询，也就是数据移动操作会被消除。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。  这种解释较为粗略。 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  有关选择分布列如何能提升性能，以及如何在 CREATE TABLES 语句的 WITH 子句中定义分布表的更多详细信息，请参阅以下链接。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表数据分布](sql-data-warehouse-tables-distribute.md)、[Selecting table distribution](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)（选择表数据分布）、[CREATE TABLE](sql-data-warehouse-tables-overview.md)、[CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>不要过度分区
尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  通常，在 SQL Server 上可能很好地工作的高粒度分区策略在 SQL 池中可能不起作用。  如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。  请记住，在后台，SQL 池会将您的数据分区到 60 个数据库中，因此，如果创建具有 100 个分区的表，这实际上会导致 6000 个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  请考虑比 SQL Server 上运行良好的数据粒度更低的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](sql-data-warehouse-tables-partition.md)

## <a name="minimize-transaction-sizes"></a>最小化事务大小
在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  例如，如果预期 INSERT 需要 1 小时，可能的话，将 INSERT 分成 4 个部分，每个部分运行 15 分钟。  使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 需要的时间一样长，则较安全的操作，是在它具有极小事务记录的条件下运行它，且必要时可以快速地取消。

另请参阅[了解事务](sql-data-warehouse-develop-transactions.md)、[优化事务](sql-data-warehouse-develop-best-practices-transactions.md)、[表分区](sql-data-warehouse-tables-partition.md)[、TRUNCATE TABLE、ALTER](https://msdn.microsoft.com/library/ms177570.aspx) [TABLE、](https://msdn.microsoft.com/library/ms190273.aspx)[创建表作为选择表 （CTAS）。](sql-data-warehouse-develop-ctas.md)

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小
在定义 DDL 时，使用可支持数据的最小数据类型，能够改善查询性能。  这对 CHAR 和 VARCHAR 列尤其重要。  如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表数据类型](sql-data-warehouse-tables-data-types.md)、[CREATE TABLE](sql-data-warehouse-tables-overview.md)

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表
群集列存储索引是将数据存储在 SQL 池中的最有效方法之一。  默认情况下，SQL 池中的表创建为群集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表分段质量的分步说明，请参阅[表索引](sql-data-warehouse-tables-index.md)一文中的[列存储索引质量不佳的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)。  由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常不会将数据推送到压缩列存储段，直到每个表有 100 多万行，并且每个 SQL 池表被分区为 60 个表，因此，根据经验，列存储表不会使查询受益，除非表有超过 6000 万行。  小于 6 千万列的表使用列存储索引似乎不太合理，  但也无伤大雅。  此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表有 100 个分区，则它至少必须拥有 60 亿个行才将受益于聚集列存储（60 个分布区 * 100 个分区 * 1 百万行）。  如果表在本示例中并没有 60 亿个行，请减少分区数目，或考虑改用堆表。  使用辅助索引配合堆表而不是列存储表，也可能是值得进行的实验，看看是否可以获得较佳的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](sql-data-warehouse-tables-index.md)、[列存储索引指南](https://msdn.microsoft.com/library/gg492088.aspx)、[重新生成列存储索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="next-steps"></a>后续步骤
如果在本文中找不到要查找的内容，请尝试使用此页面左侧的"搜索文档"来搜索所有 Azure Synapse 分析文档。  在 [Azure SQL 数据仓库论坛](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)中可以向其他用户和 SQL 数据仓库的产品小组提出问题。  我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  若要提问有关堆栈溢出的问题，请访问 [Azure SQL 数据仓库堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。

最后，如需提出功能方面的请求，请使用 [Azure SQL 数据仓库反馈](https://feedback.azure.com/forums/307516-sql-data-warehouse)页。  添加请求或对其他请求投赞成票对我们确定功能的优先级有很大的帮助。


