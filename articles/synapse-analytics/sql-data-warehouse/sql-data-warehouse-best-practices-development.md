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
ms.openlocfilehash: 5857a10d0aaf0d0c37ab55a2d0d29e5315340c9f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633656"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Synapse SQL 池的开发最佳实践
本文介绍了开发 SQL 池解决方案时的指导和最佳实践。 

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新的产品增强功能优化查询性能  
- [通过具体化视图进行性能优化](performance-tuning-materialized-views.md)
- [通过有序聚集列存储索引进行性能优化](performance-tuning-ordered-cci.md)
- [通过结果集缓存进行性能优化](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本
有关通过暂停和扩展降低成本的详细信息，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)一文。 

## <a name="maintain-statistics"></a>维护统计信息
SQL 池可以配置为自动检测和创建列上的统计信息。  优化工具创建的查询计划质量以可用的统计信息为限。  

我们建议您为数据库启用AUTO_CREATE_STATISTICS，并每天或每次加载后更新统计信息，以确保查询中使用的列统计信息始终保持最新。 

如果你发现更新所有统计信息需要太长的时间，可以尝试更精心地选择哪些列的统计信息需要频繁更新。 例如，可能想要更新每天都要添加新值的日期列。 

> [!TIP]
> 通过更新联接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列，您将获得最大的好处。

另请参阅[管理表统计信息](sql-data-warehouse-tables-statistics.md)、[创建统计信息](sql-data-warehouse-tables-statistics.md)和[更新统计信息](sql-data-warehouse-tables-statistics.md#update-statistics)。

## <a name="hash-distribute-large-tables"></a>哈希分布大型表
默认情况下，表是以轮循机制分布的。  此设计可让用户更容易开始创建表，而不必确定应该如何分布其表。  

轮循机制表在某些工作负荷中执行良好，但大多数情况下，选择分布列的执行性能将更好。  按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询，也就是数据移动操作会被消除。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。  

加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  以下文章提供了有关通过选择分发列以及如何在 CREATE TABLE 语句的 WITH 子句中定义分布式表来提高性能的进一步详细信息。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表分布](sql-data-warehouse-tables-distribute.md)、[选择表分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[创建表](sql-data-warehouse-tables-overview.md)和[创建表作为 SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>不要过度分区
尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  

通常，在 SQL Server 上可能很好地工作的高粒度分区策略在 SQL 池中可能不起作用。  如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。  

请记住，在后台，SQL 池会将您的数据分区到 60 个数据库中，因此，如果创建具有 100 个分区的表，这实际上会导致 6000 个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  

> [!TIP]
> 请考虑使用比 SQL Server 中可能适用于您粒度更低的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](sql-data-warehouse-tables-partition.md)。

## <a name="minimize-transaction-sizes"></a>最小化事务大小
在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果预期 INSERT 需要 1 小时，可能的话，将 INSERT 分成 4 个部分，每个部分运行 15 分钟。  利用特殊的最小日志记录案例（如 CTAS、TRUNCATE、DROP TABLE 或插入空表）来降低回滚风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  

针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 需要的时间一样长，则较安全的操作，是在它具有极小事务记录的条件下运行它，且必要时可以快速地取消。

另请参阅[了解事务](sql-data-warehouse-develop-transactions.md)、[优化事务](sql-data-warehouse-develop-best-practices-transactions.md)、[表分区](sql-data-warehouse-tables-partition.md)[、TRUNCATE 表](https://msdn.microsoft.com/library/ms177570.aspx)[、ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)和[创建表作为选择 （CTAS）。](sql-data-warehouse-develop-ctas.md)

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小
在定义 DDL 时，使用可支持数据的最小数据类型，能够改善查询性能。  此方法对于 CHAR 和 VARCHAR 列尤其重要。  

如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述](sql-data-warehouse-tables-overview.md)、[表数据类型](sql-data-warehouse-tables-data-types.md)和[创建表](sql-data-warehouse-tables-overview.md)。

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表
群集列存储索引是将数据存储在 SQL 池中的最有效方法之一。  默认情况下，SQL 池中的表创建为群集列存储。  

> [!NOTE]
> 要实现对列存储表上的查询的最佳性能，具有良好的段质量非常重要。  

当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  压缩行组中的行数可以测量分段质量。  

有关检测和改进群集列存储表的段质量的分步说明，请参阅[表索引](sql-data-warehouse-tables-index.md)文章中[的"列存储索引质量差的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)"  

由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常不会将数据推送到压缩列存储段，直到每个表超过 100 万行，并且每个 SQL 池表被分区为 60 个表，因此，通常，除非表具有 6000 多行，否则列存储表不会使查询受益。  

对于行数少于 6000 万行的表，具有列存储索引可能没有任何意义。  但也无伤大雅。  

此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表有 100 个分区，则它至少必须拥有 60 亿个行才将受益于聚集列存储（60 个分布区 * 100 个分区 * 1 百万行）。  

如果表在本示例中并没有 60 亿个行，请减少分区数目，或考虑改用堆表。  使用辅助索引配合堆表而不是列存储表，也可能是值得进行的实验，看看是否可以获得较佳的性能。

> [!TIP]
> 查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](sql-data-warehouse-tables-index.md)、[列存储索引指南](https://msdn.microsoft.com/library/gg492088.aspx)和[重建列存储索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)。

## <a name="next-steps"></a>后续步骤
如果在本文中找不到要查找的内容，请尝试使用此页面左侧的"搜索文档"来搜索所有 Azure Synapse 文档。  

[Azure 突触论坛](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他用户和 Azure Synapse 产品组发布问题的地方。  我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  

若要提问有关堆栈溢出的问题，请访问 [Azure SQL 数据仓库堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。

使用[Azure 同步反馈](https://feedback.azure.com/forums/307516-sql-data-warehouse)页发出功能请求。  添加请求或对其他请求投赞成票对我们确定功能的优先级有很大的帮助。


