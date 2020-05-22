---
title: 分布式表设计指南
description: 有关在 Synapse SQL 池中设计哈希分布式表和轮循机制分布式表的一些建议。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 599514f6e7b97208194fc4c1660712f4d5e0c4cb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585345"
---
# <a name="guidance-for-designing-distributed-tables-in-synapse-sql-pool"></a>在 Synapse SQL 池中设计分布式表的指南

在 Synapse SQL 池中设计哈希分布式表和轮循机制分布式表的一些建议。

本文要求读者熟悉 Synapse SQL 池中的数据分布和数据移动概念。  有关详细信息，请参阅 [Azure Synapse Analytics 大规模并行处理 (MPP) 体系结构](massively-parallel-processing-mpp-architecture.md)。

## <a name="what-is-a-distributed-table"></a>什么是分布式表？

分布式表显示为单个表，但表中的行实际存储在 60 个分布区中。 这些行使用哈希或轮循机制算法进行分布。  

**哈希分布式表**可提高大型事实数据表的查询性能，本文会重点进行介绍。 **轮循机制表**可用于提高加载速度。 这些设计选择对提高查询和加载性能具有重大影响。

另一个表存储选项是跨所有计算节点复制一个小型表。 有关详细信息，请参阅[复制表的设计准则](design-guidance-for-replicated-tables.md)。 若要在这三个选项之间快速选择其一，请参阅[表概述](sql-data-warehouse-tables-overview.md)中的分布式表。

在设计表的过程中，尽可能多地了解数据以及数据查询方式。  例如，考虑以下问题：

- 表有多大？
- 表的刷新频率是多少？
- Synapse SQL 池中有事实数据表和维度表吗？

### <a name="hash-distributed"></a>哈希分布

哈希分布表通过使用确定性的哈希函数将每一行分配给一个[分布区](massively-parallel-processing-mpp-architecture.md#distributions)，实现表行的跨计算节点分布。

![分布式表](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "分布式表")  

由于相同的值始终哈希处理到相同的分布区，因此，数据仓库本身就具有行位置方面的信息。 在 Synapse SQL 池中，利用此信息可最大程度地减少查询期间的数据移动，从而提高查询性能。

哈希分布表适用于星型架构中的大型事实数据表。 它们可以包含大量行，但仍实现高性能。 当然，用户应该了解一些设计注意事项，它们有助于获得分布式系统本应具有的性能。 本文所述的选择合适的分布列就是其中之一。

在以下情况下，考虑使用哈希分布表：

- 磁盘上的表大小超过 2 GB。
- 对表进行频繁的插入、更新和删除操作。

### <a name="round-robin-distributed"></a>轮循机制分布

轮循机制分布表将表行均衡分布在所有分布区中。 将行分配到分布区的过程是随机的。 与哈希分布表不同的是，值相等的行不一定分配到相同的分布区。

因此，系统有时候需要调用数据移动操作，以便在求解查询前更加合理地组织数据。  此附加步骤可能使查询变慢。 例如，联接轮循机制表通常需要对行重新进行数据重组，这会给性能带来影响。

在以下情况下，考虑对表使用轮循机制分布：

- 在最开始将其用作一个简单的起点，因为该分布是默认选项
- 没有明显的联接键时
- 没有合适的候选列对表进行哈希分布时
- 表没有与其他表共享通用的联接键时
- 该联接比查询中的其他联接更不重要时
- 表是临时过渡表时

教程[加载纽约出租车数据](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse)提供了将数据加载到轮循机制临时表的示例。

## <a name="choosing-a-distribution-column"></a>选择分布列

哈希分布表中有一个分布列是哈希键。 例如，以下代码会创建一个以 ProductKey 作为分布列的哈希分布表。

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

可以更新分布列中存储的数据。 对分布列中的数据进行更新可能导致数据无序操作。

选择分布列是一个重要的设计决策，因为此列中的值确定行的分布方式。 最佳选择取决于多种因素，通常需要对各方面进行权衡。 选择分布列后，将无法对其进行更改。  

如果第一次未选择最合适的列，可以使用 [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 重新创建具有不同分布列的表。

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>选择数据均衡分布的分布列

为了获得最佳性能，所有分布区都应当具有大致相同的行数。 当一个或多个分布区的行数不相称时，某些分布区会先于其他分布区完成其并行查询部分。 由于必须等到所有分布区都完成处理，才能完成查询，因此，每个查询的速度取决于最慢分布区的速度。

- 数据倾斜意味着数据未均衡分布在分布区中
- 处理倾斜意味着在运行并行查询时，某些分布区所用的时间比其他分布区长。 数据倾斜时可能会出现这种情况。
  
若要平衡并行处理，请选择符合以下条件的分布列：

- **具有许多唯一值。** 分布列可以有一些重复值。 但具有相同值的所有行都分配到相同的分布区。 由于有 60 个分布区，分布列应具有至少 60 个唯一值。  通常情况下，唯一值的数量要多得多。
- **没有 NULL 值，或者只有几个 NULL 值。** 在极端示例中，如果列中的所有值均为 NULL，则所有行都分配到相同的分布区。 因此，查询处理会向一个分布区倾斜，无法从并行处理中受益。
- **不是日期列**。 同一日期的所有数据都落在相同的分布区。 如果多个用户都筛选同一个日期，则会由 60 个分布区中的 1 个分布区单独执行所有处理工作。

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>选择能最大程度减少数据移动的分布列

为了获取正确的查询结果，查询可能将数据从一个计算节点移至另一个计算节点。 当查询对分布式表执行联接和聚合操作时，通常会发生数据移动。 选择能最大程度减少数据移动的分布列，这是优化 Synapse SQL 池性能最重要的策略之一。

若要最大程度减少数据移动，请选择符合以下条件的分布列：

- 用于 `JOIN`、`GROUP BY`、`DISTINCT`、`OVER` 和 `HAVING` 子句。 当两个大型事实数据表频繁联接时，如果将这两个表分布在某个联接列上，查询性能将得到提升。  如果某个表不进行联接操作，则考虑将该表分布在经常出现在 `GROUP BY` 子句中的列上。
- *不*用于 `WHERE` 子句。 这可以缩小查询范围，从而不必在所有分布区上运行查询。
- *不*是日期列。 WHERE 子句通常按日期进行筛选。  在这种情况下，可能会在少数几个分布区上运行所有处理。

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>没有合适分布列时怎么办

如果列没有足够的不同值用于分步列，可创建一个新列作为一个或多个值的复合。 要避免查询执行时出现数据移动，可在查询中使用复合分布列作为联接列。

完成哈希分布表的设计后，下一步就是将数据加载到表。  有关加载指南，请参阅[加载概述](design-elt-data-loading.md)。

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>如何判断分布列是否合适

将数据加载到哈希分布表之后，查看行在 60 个分布区中分布的均衡程度。 如果每个分布区的行数相差不超过 10%，性能不会受到明显影响。

### <a name="determine-if-the-table-has-data-skew"></a>确定表是否有数据倾斜现象

一种快速的数据倾斜检查方法是使用 [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 以下 SQL 代码返回 60 个分布区中每个分布区存储的表行数。 为了获得平衡的性能，分布式表中的行应该均衡分布在所有分布区中。

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

若要确定哪些表的数据倾斜率超过 10%，请执行以下操作：

1. 创建[表概述](sql-data-warehouse-tables-overview.md#table-size-queries)一文中所示的 dbo.vTableSizes 视图。  
2. 运行以下查询：

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>检查查询计划中是否有数据移动

合适的分布列可最大程度减少联接和聚合操作中的数据移动。 这会影响联接的编写方式。 若要在联接两个哈希分布表时最大程度减少数据移动，联接列中必须有一个是分布列。  当两个哈希分布表在一个数据类型相同的分布列上联接时，该联接不需要移动数据。 联接可以使用附加列，而不发生数据移动。

若要避免在联接过程中移动数据，应遵循以下做法：

- 参与联接的列的相关表必须哈希分布在**一个**联接列中。
- 两个表之间联接列的数据类型必须匹配。
- 必须使用 equals 运算符联接列。
- 联接类型不能是 `CROSS JOIN`。

若要了解查询是否正在移动数据，可以查看查询计划。  

## <a name="resolve-a-distribution-column-problem"></a>解决分布列问题

无需解决所有数据倾斜问题。 分布数据就是为了找出将数据倾斜降至最低与将数据移动降至最低两者之间的适当平衡。 因为现实中无法每次都做到将数据倾斜和数据移动同时降至最低。 有时，最少的数据移动带来的好处可能胜过数据倾斜造成的影响。

要确定是否应该解决表中的数据偏斜，应该尽可能了解工作负荷中的数据卷和查询。 可以按照[查询监视](sql-data-warehouse-manage-monitor.md)一文中的步骤监视倾斜对查询性能的影响。 具体而言，就是了解在各个分布区上完成大型查询所需的时间。

由于不能更改现有表中的分布列，因此，解决数据倾斜的典型方法是重新创建具有不同分布列的表。  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>重新创建具有新分布列的表

本示例使用 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 重新创建具有不同哈希分布列的表。

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>后续步骤

若要创建分布式表，请使用以下语句之一：

- [CREATE TABLE（Synapse SQL 池）](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT（Synapse SQL 池）](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
