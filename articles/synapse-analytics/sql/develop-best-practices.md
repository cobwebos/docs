---
title: Synapse SQL 的开发最佳实践
description: 在为 Synapse SQL 开发时，应了解的建议和最佳实践。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086345"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 的开发最佳实践
本文介绍在开发数据仓库解决方案时的指导和最佳做法。 

## <a name="sql-pool-development-best-practices"></a>SQL 池开发最佳实践

### <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="maintain-statistics"></a>维护统计信息

请确保每日或每次加载后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。 如果您发现维护所有统计信息需要很长时间，请更有选择地确定哪些列具有统计信息或哪些列需要频繁更新。  

例如，您可能希望更新日期列，其中可以每天添加新值。 

> [!NOTE]
> 通过对联接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列进行统计，您将获得最大的好处。

另请参阅[管理表统计信息](develop-tables-statistics.md)、[创建统计信息](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[更新统计信息](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。  这样，用户可以轻松地开始创建表，而无需决定如何分配表。  对于某些工作负载，循环表可能运行得足够多。 但是，在大多数情况下，选择分发列会执行更好的效果。  

按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果您有一个订单表（由order_id分发）和一个事务表（也由order_id分发）在order_id上将订单表加入到交易记录表时，此查询将成为传递查询。 

这意味着我们消除了数据移动操作。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。

> [!TIP]
> 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  

有关选择分发列如何提高性能以及如何在 CREATE TABLE 语句的"WITH"子句中定义分布式表的其他详细信息，请参阅以下链接。

另请参阅[表概述](develop-tables-overview.md)、[表分布](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[选择表分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[创建表](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[创建表作为 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="do-not-over-partition"></a>不要过度分区
尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  通常，在 SQL Server 上可能很好地工作的高粒度分区策略在 SQL 池中可能不起作用。  

> [!NOTE]
> 通常，在 SQL Server 上可能很好地工作的高粒度分区策略在 SQL 池中可能不起作用。  

如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。 SQL 池将您的数据分区到 60 个数据库中。 

因此，如果创建具有 100 个分区的表，则结果为 6000 个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  

需要考虑的一个选项是使用小于 SQL Server 中可能适用于您粒度的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="minimize-transaction-sizes"></a>最小化事务大小

在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果您有一个"插入"，预计需要 1 小时，则可以将 INSERT 分成四个部分，从而将每次运行缩短为 15 分钟。

> [!TIP]
> 使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  

例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  

针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 需要的时间一样长，则较安全的操作，是在它具有极小事务记录的条件下运行它，且必要时可以快速地取消。

另请参阅[了解事务](develop-transactions.md)、[优化事务](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)[、TRUNCATE 表](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)[、ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[创建表作为选择 （CTAS）。](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

在定义 DDL 时，使用可支持数据的最小数据类型，能够改善查询性能。  这对 CHAR 和 VARCHAR 列尤其重要。  

如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述](develop-tables-overview.md)、[表数据类型](develop-tables-data-types.md)和[创建表](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

群集列存储索引是将数据存储在 SQL 池中的最有效方法之一。  默认情况下，SQL 池中的表创建为群集列存储。  

为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  

压缩行组中的行数可以测量分段质量。  有关检测和改进群集列存储表的段质量的分步说明，请参阅[列存储索引质量差的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)和[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。  

由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](resource-consumption-models.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常不会将数据推送到压缩列存储段，直到每个表超过 100 万行，并且每个 SQL 池表被分区为 60 个表，因此，除非表具有 6000 多列行，否则列存储表不会使查询受益。  

> [!TIP]
> 对于行数少于 6000 万行的表，具有 columstore 索引可能不是最佳解决方案。  

此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表具有 100 个分区，则它需要至少 60 亿行才能从群集列存储中获益（60 个分布*100 个分区 100*个分区 100 万行）。  

如果表没有 60 亿行，请减少分区数或考虑改用堆表。  可能还值得尝试，看看通过使用具有辅助索引的堆表而不是列存储表是否可以获得更好的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列存储索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[重建列存储索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)。

## <a name="sql-on-demand-development-best-practices"></a>SQL 按需开发最佳实践

### <a name="general-considerations"></a>一般注意事项

SQL 按需允许您查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能，这意味着查询目标的所有文件都位于 SQL 按需外部。 因此，与从存储读取文件相关的一切都可能会影响查询性能。

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>按需为 Azure 存储帐户和 SQL 提供共同定位

为了最大程度地减少延迟，请共同定位 Azure 存储帐户和 SQL 按需终结点。 工作区创建期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果使用 SQL 按需访问其他存储帐户，请确保它们位于同一区域。 否则，数据从远程区域传输到终结点区域的网络传输的延迟将增加。

### <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用程序和服务可以访问您的存储帐户。 当应用程序、服务和 SQL 按需工作负载生成的合并 IOPS 或吞吐量超过存储帐户的限制时，就会发生存储限制。 当发生存储限制时，会对查询性能产生重大的负面影响。

检测到限制后，SQL 按需已内置处理此方案。 SQL 按需将以较慢的速度发出存储请求，直到解决限制问题。 

但是，为了获得最佳的查询执行，建议您在查询执行期间不要使用其他工作负载来强调存储帐户。

### <a name="prepare-files-for-querying"></a>准备用于查询的文件

如果可能，您可以准备文件以获得最佳性能：

- 将 CSV 转换为镶木地板 – 镶木地板是柱形格式。 由于它是压缩的，它比具有相同数据的 CSV 文件的文件大小小，并且 SQL 按需读取它所需的时间和存储请求更少。
- 如果查询以单个大型文件为目标，则将其拆分为多个较小的文件将受益匪浅。
- 请尝试将 CSV 文件大小保持在 10GB 以下。
- 最好为单个 OPENROWSET 路径或外部表位置具有大小相等的文件。
- 通过将分区存储到不同的文件夹或文件名来划分数据 - 检查[使用文件名和文件路径函数来定位特定分区](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用文件信息和文件路径函数定位特定分区

数据通常以分区方式组织。 您可以指示 SQL 按需查询特定文件夹和文件。 这将减少查询需要读取和处理的文件数量和数据量。 

因此，您将获得更好的性能。 有关详细信息，请查看[文件名](develop-storage-files-overview.md#filename-function)和[文件路径](develop-storage-files-overview.md#filepath-function)函数以及如何[查询特定文件](query-specific-files.md)的示例。

如果存储中的数据未分区，请考虑对其进行分区，以便可以使用这些函数优化针对这些文件的查询。

当从 SQL 按需[查询分区 Spark 表](develop-storage-files-spark-tables.md)时，查询将自动定位为仅需要的文件。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 提高查询性能和联接

[CETAS](develop-tables-cetas.md)是 SQL 按需提供的最重要功能之一。 CETAS 是一个并行操作，用于创建外部表元数据并将 SELECT 查询的结果导出到存储帐户中的一组文件。

您可以使用 CETAS 将常用的查询（如联接的引用表）存储到一组新的文件。 稍后，您可以加入此外部表，而不是在多个查询中重复公共联接。 

当 CETAS 生成 Parquet 文件时，当第一个查询针对此外部表时，将自动创建统计信息，您将获得更好的性能。

### <a name="next-steps"></a>后续步骤

如果您需要本文中未提供的信息，请使用此页面左侧的"搜索文档"来搜索所有 SQL 池文档。  [SQL 池论坛](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他用户和 SQL 池产品组提出问题的地方。  

我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  如果您喜欢在堆栈溢出上提问，我们也有一个 Azure [SQL 池堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。
 