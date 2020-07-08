---
title: 使用有序聚集列存储索引优化性能
description: 使用有序聚集列存储索引改善查询性能时应了解的建议和注意事项。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6cd81031f27d772912383fa050e0f946bf9964c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204653"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>使用有序聚集列存储索引优化性能  

当用户查询 Synapse SQL 池中的列存储表时，优化器会检查每个段中存储的最小值和最大值。  超出查询谓词边界的段不会从磁盘读取到内存。  如果要读取的段的数目及其总大小较小，则查询性能可以更快。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>有序与无序聚集列存储索引

默认情况下，对于不是使用索引选项创建的每个表，某个内部组件（索引生成器）将在该表中创建无序的聚集列存储索引 (CCI)。  每个列中的数据压缩成单独的 CCI 行组段。  每个段的值范围都有元数据，因此，在执行查询期间，不会从磁盘中读取超出查询谓词边界的段。  CCI 提供最高级别的数据压缩，可减少要读取的段大小，因此查询可以更快地运行。 但是，由于索引生成器在将数据压缩成段之前不会将数据排序，因此可能会出现值范围重叠的段，从而导致查询从磁盘中读取更多的段，需要更长的时间才能完成。  

创建有序 CCI 时，Synapse SQL 引擎会先按顺序键将内存中的现有数据排序，然后，索引生成器将这些数据压缩成索引段。  使用有序数据可以减少段重叠的情况，使查询更有效地消除段，因而可提高性能，因为要从磁盘读取的段数更少。  如果可以一次性在内存中为所有数据排序，则可以避免段重叠的情况。  由于数据仓库中的表较大，因此这种情况不经常发生。  

若要检查列的段范围，请结合表名称和列名称运行以下命令：

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> 在有序 CCI 表中，同一批 DML 或数据加载操作生成的新数据将在该批中排序，而表中的所有数据不会经过全局排序。  用户可以重新生成 (REBUILD) 有序 CCI 来对表中的所有数据进行排序。  在 Synapse SQL 中，列存储索引重新生成是一项脱机操作。  对于已分区的表，每次将对一个分区执行重新生成。  重新生成的分区中的数据是“脱机”的，在对该分区完成重新生成之前，这些数据不可用。 

## <a name="query-performance"></a>查询性能

有序 CCI 带来的查询性能提升程度取决于查询模式、数据大小、数据排序的合理性、段的物理结构，以及为查询执行选择的 DWU 和资源类。  在设计有序 CCI 表时，用户应在选择排序列之前考虑所有这些因素。

具有所有这些模式的查询在使用有序 CCI 时运行速度往往更快。  
1. 查询具有相等性、不相等性或范围谓词
1. 谓词列和有序 CCI 列相同。  
1. 谓词列的使用顺序与有序 CCI 列的列序号相同。  
 
在此示例中，表 T1 具有一个已按 Col_C、Col_B 和 Col_A 顺序排序的聚集列存储索引。

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

如果使用有序 CCI，查询 1 的性能比其他三个查询更好。 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>数据加载性能

将数据载入有序 CCI 表中的性能类似于将数据载入已分区的表。  由于需要执行数据排序操作，将数据载入有序 CCI 表所需的时间可能比载入无序 CCI 表更长，但之后，查询可以使用有序 CCI 更快地运行。  

以下示例将数据载入采用不同架构的表的性能做了比较。

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


以下示例对使用 CCI 和有序 CCI 的查询性能做了比较。

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>减少段重叠

重叠段的数目取决于要排序的数据的大小、可用内存，以及创建有序 CCI 期间的最大并行度 (MAXDOP) 设置。 以下选项可以在创建有序 CCI 时减少段重叠情况。

- 在更高的 DWU 上使用 xlargerc 资源类，以便在索引生成器将数据压缩成段之前，有更多的内存可用于数据排序。  进入索引段后，数据的物理位置不可更改。  段内部或者段之间不会发生数据排序。  

- 使用 MAXDOP = 1 创建有序 CCI。  用于创建有序 CCI 的每个线程针对一部分数据运行，并在本地为数据排序。  已由不同线程排序的数据不会经过全局排序。  使用并行线程可以减少创建有序 CCI 所需的时间，但生成的重叠段比使用单个线程时更多。  目前，MAXDOP 选项只可用于通过 CREATE TABLE AS SELECT 命令创建有序 CCI 表。  通过 CREATE INDEX 或 CREATE TABLE 命令创建有序 CCI 时不支持 MAXDOP 选项。 例如，

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- 将数据载入表之前，预先按排序键将数据排序。

下面是有序 CCI 表分布示例，该表根据上述建议清除了段重叠情况。 该有序 CCI 表是使用 MAXDOP 1 和 xlargerc，基于 20-GB 的堆表通过 CTAS 在 DWU1000c 数据库中创建的。  CCI 已按照不包含重复项的 BIGINT 列进行排序。  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>在大型表中创建有序 CCI

创建有序 CCI 是一项脱机操作。  对于不包含分区的表，在有序 CCI 创建过程完成之前，用户无法访问数据。   对于已分区的表，由于引擎将按分区创建有序的 CCI 分区，因此，在尚未进行有序 CCI 创建操作的情况下，用户仍可以访问分区中的数据。   在大型表中创建有序 CCI 的过程中，可以使用此选项来尽量减少停机时间： 

1.    在目标大型表（名为 Table_A）中创建分区。
2.    使用与表 A 相同的表架构和分区架构创建空的有序 CCI 表（名为 Table_B）。
3.    将一个分区从表 A 切换到表 B。
4.    针对 表 B 运行 ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID>，以重新生成换入的分区。  
5.    针对 Table_A 中的每个分区重复步骤 3 和 4。
6.    将所有分区从 Table_A 切换到 Table_B 并重新生成这些分区后，删除 Table_A，并将 Table_B 重命名为 Table_A。 

## <a name="examples"></a>示例

**A.检查有序列和序号：**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B.若要更改列序号，请在顺序列表中添加或删除列，或者从 CCI 更改为有序 CCI：**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。
