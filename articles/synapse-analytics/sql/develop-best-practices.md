---
title: Synapse SQL 最佳开发方案
description: 为 Synapse SQL 开发时应了解的建议和最佳实践。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086345"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 最佳开发方案
本文介绍在开发数据仓库解决方案时的指导和最佳做法。 

## <a name="sql-pool-development-best-practices"></a>SQL 池开发最佳做法

### <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本

若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="maintain-statistics"></a>维护统计信息

请确保每日或每次加载后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。 如果发现维护所有统计信息所需的时间太长，则更有选择性地了解哪些列有统计信息，哪些列需要频繁更新。  

例如，你可能想要更新日期列，在这种情况下，可能会每天添加新的值。 

> [!NOTE]
> 对于联接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列的统计信息，你将获得最大优势。

另请参阅[管理表统计信息](develop-tables-statistics.md)、[创建统计](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)信息和[更新统计信息](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="hash-distribute-large-tables"></a>哈希分布大型表

默认情况下，表是以轮循机制分布的。  这样，用户就可以轻松地开始创建表，而无需决定如何分散表。  轮循机制表在某些工作负荷上可能会完全执行。 但在大多数情况下，选择分布列会更好地执行。  

按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  

例如，如果你有一个 "orders" 表，它由 order_id 分发，而 "事务" 表也由 order_id 分发，则当你将 orders 表联接到 order_id 上的事务表时，此查询将成为传递查询。 

这意味着我们消除了数据移动操作。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。

> [!TIP]
> 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  

请参阅以下链接，了解有关选择分布列如何提高性能的其他详细信息，以及如何在 CREATE TABLES 语句的 WITH 子句中定义分布式表。

另请参阅[表概述](develop-tables-overview.md)、[表分布](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[选择表分布](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[CREATE TABLE 选择](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="do-not-over-partition"></a>不要过度分区
尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  通常可以很好地处理 SQL Server 的高粒度分区策略可能无法在 SQL 池上正常工作。  

> [!NOTE]
> 通常可以很好地处理 SQL Server 的高粒度分区策略可能无法在 SQL 池上正常工作。  

如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。 SQL 池将你的数据分区到60数据库。 

因此，如果您创建一个包含100个分区的表，则结果将是6000个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  

要考虑的一种选择是使用的粒度低于在 SQL Server 中的工作方式。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="minimize-transaction-sizes"></a>最小化事务大小

在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  

例如，如果您的插入需要1小时，则可以将插入分解为四个部分，从而缩短每个运行15分钟。

> [!TIP]
> 使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  

另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  

例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  

针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 需要的时间一样长，则较安全的操作，是在它具有极小事务记录的条件下运行它，且必要时可以快速地取消。

另请参阅[了解事务](develop-transactions.md)、[优化事务](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、 [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、 [ALTER Table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[Create table as select （CTAS）](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小

在定义 DDL 时，使用可支持数据的最小数据类型，能够改善查询性能。  这对 CHAR 和 VARCHAR 列尤其重要。  

如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述](develop-tables-overview.md)、[表数据类型](develop-tables-data-types.md)和[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表

聚集列存储索引是将数据存储在 SQL 池中的最有效方法之一。  默认情况下，SQL 池中的表创建为聚集列存储。  

为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  

压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表的段质量的分步说明，请参阅[列存储索引质量不佳](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)和[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。  

由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](resource-consumption-models.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常不会将数据推送到压缩的列存储段，因此在每个表中有超过1000000行的情况下，每个 SQL 池表都分区为60表，除非该表的行数超过了60000000，否则列存储表将不会对查询产生益处。  

> [!TIP]
> 对于少于60000000行的表，聚集索引可能不是最佳解决方案。  

此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表包含100个分区，则必须至少有6000000000行才能受益于聚集列存储（60分布*100 分区*1000000 行）。  

如果您的表没有6000000000行，请减少分区数，或考虑改用堆表。  还值得一提的是，通过使用带有辅助索引的堆表而不是列存储表来了解能否获得更好的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列存储索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、重新[生成列存储索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)。

## <a name="sql-on-demand-development-best-practices"></a>SQL 点播开发最佳实践

### <a name="general-considerations"></a>一般注意事项

SQL 点播允许查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能，这意味着，查询所面向的所有文件都是 SQL 点播的外部文件。 因此，与从存储读取文件相关的所有内容都可能会影响查询性能。

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>归置 Azure 存储帐户和 SQL 点播

若要最大程度地减少延迟，请将 Azure 存储帐户和 SQL 点播终结点归置。 创建工作区期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果使用 SQL 点播访问其他存储帐户，请确保它们位于同一区域。 否则，数据从远程区域到终结点的区域的网络传输延迟将会增加。

### <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用程序和服务可以访问你的存储帐户。 当应用程序、服务和 SQL 按需工作负荷生成的组合 IOPS 或吞吐量超过存储帐户的限制时，将发生存储限制。 当发生存储限制时，对查询性能有重大影响。

一旦检测到限制，SQL 点播就会对此方案进行内置处理。 SQL 点播将以较慢的速度向存储请求，直到限制得以解决。 

但是，为实现最佳查询执行，建议你不要在执行查询的过程中将存储帐户用于其他工作负荷。

### <a name="prepare-files-for-querying"></a>准备文件以进行查询

如果可能，可以准备文件以获得更好的性能：

- 将 CSV 转换为 Parquet – Parquet 为纵栏格式。 由于它是压缩的，因此它的文件大小比具有相同数据的 CSV 文件的文件大小小，需要更少的时间和存储请求来读取数据。
- 如果查询以单个大文件为目标，则将其拆分为多个较小的文件将会带来好处。
- 请尝试将 CSV 文件的大小保持在10GB 以下。
- 最好为单个 OPENROWSET 路径或外部表位置设置大小相同的文件。
- 通过将分区存储到不同文件夹或文件名来对数据进行分区，请检查[使用 filename 和 filepath 函数以针对特定分区](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函数针对特定分区

数据通常在分区中进行组织。 可以指示 SQL 按需查询特定文件夹和文件。 这会减少查询读取和处理所需的文件数和数据量。 

因此，您将获得更好的性能。 有关详细信息，请查看[filename](develop-storage-files-overview.md#filename-function)和[filepath](develop-storage-files-overview.md#filepath-function)函数以及如何[查询特定文件](query-specific-files.md)的示例。

如果存储中的数据未分区，请考虑对数据进行分区，以便可以使用这些函数来优化针对这些文件的查询。

从 SQL 按需[查询分区 Spark 表](develop-storage-files-spark-tables.md)时，查询将自动仅针对所需的文件。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 增强查询性能和联接

[CETAS](develop-tables-cetas.md)是 SQL 点播中最重要的功能之一。 CETAS 是一种并行操作，用于创建外部表元数据并将 SELECT 查询的结果导出到存储帐户中的一组文件。

您可以使用 CETAS 将经常使用的查询的一部分（如联接的引用表）存储到一组新的文件中。 稍后，可以联接到此单个外部表，而不是重复多个查询中的常用联接。 

当 CETAS 生成 Parquet 文件时，当第一个查询针对此外部表时，将自动创建统计信息，并且你将获得更高的性能。

### <a name="next-steps"></a>后续步骤

如果你需要本文中未提供的信息，请使用此页面左侧的 "搜索文档" 来搜索所有 SQL 池文档。  [Sql Pool 论坛](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他用户和 SQL Pool 产品组提出问题的地方。  

我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  如果你希望在 Stack Overflow 上提出问题，还会有一个[AZURE SQL 池 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)。
 