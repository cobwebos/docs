---
title: 使用 Synapse SQL 设计表
description: 在 SynapsE SQL 中设计表的简介。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431873"
---
# <a name="design-tables-using-synapse-sql"></a>使用 Synapse SQL 设计表

本文档包括设计具有 SQL 池和 SQL 按需（预览）的表的关键概念。  

[SQL 按需（预览）](on-demand-workspace-overview.md)是数据湖中数据的查询服务。 它没有用于数据引入的本地存储。 [SQL 池](best-practices-sql-pool.md)表示在使用 Synapse SQL 时正在预配的分析资源的集合。 SQL 池的大小由数据仓库单位 (DWU) 决定。

下表列出了与 SQL 池与 SQL 按需相关的主题：

| 主题                                                        | SQL 池 | 按需 SQL |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [确定表类别](#determine-table-category)        | 是                | 否                      |
| [架构名称](#schema-names)                                | 是                | 是                     |
| [表名称](#table-names)                                  | 是                | 否                      |
| [表暂留](#table-persistence)                      | 是                | 否                      |
| [常规表](#regular-table)                              | 是                | 否                      |
| [临时表](#temporary-table)                          | 是                | 是                     |
| [外部表](#external-table)                            | 是                | 是                     |
| [数据类型](#data-types)                                    | 是                | 是                     |
| [分布式表](#distributed-tables)                    | 是                | 否                      |
| [哈希分布表](#hash-distributed-tables)          | 是                | 否                      |
| [复制表](#replicated-tables)                      | 是                | 否                      |
| [循环表](#round-robin-tables)                    | 是                | 否                      |
| [表的常用分布方法](#common-distribution-methods-for-tables) | 是                | 否                      |
| [分区](#partitions)                                    | 是                | 是                     |
| [列存储索引](#columnstore-indexes)                  | 是                | 否                      |
| [统计信息](#statistics)                                    | 是                | 是                     |
| [主键和唯一键](#primary-key-and-unique-key)    | 是                | 否                      |
| [用于创建表的命令](#commands-for-creating-tables) | 是                | 否                      |
| [使源数据与数据仓库相符](#aligning-source-data-with-the-data-warehouse) | 是                | 否                      |
| [不支持的表功能](#unsupported-table-features)    | 是                | 否                      |
| [表大小查询](#table-size-queries)                    | 是                | 否                      |

## <a name="determine-table-category"></a>确定表类别

[星型架构](https://en.wikipedia.org/wiki/Star_schema)将数据组织成事实数据表和维度表。 某些表用于在移动到事实或维度表之前集成或暂存数据。 设计某个表时，请确定该表的数据是属于事实数据表、维度表还是集成表。 此项决策可以明确相应的表结构和分布方式。

- **事实数据表**包含定量数据，这些数据通常在事务系统中生成，然后加载到数据仓库中。 例如，零售企业每天会生成销售事务，然后将数据载入数据仓库事实数据表进行分析。

- **维度表**包含属性数据，这些数据可能会更改，但一般不会经常更改。 例如，客户的姓名和地址存储在维度表中，仅当客户的个人资料发生更改时，这些数据才会更新。 为了最小化大型事实表的大小，客户的姓名和地址不需要出现在事实数据表的每一行中。 事实数据表和维度表可以共享一个客户 ID。 查询可以联接两个表，以关联客户的个人资料和事务。

- **集成表**为集成或暂存数据提供位置。 可以将集成表创建为常规表、外部表或临时表。 例如，可将数据加载到临时表，在暂存位置对数据执行转换，然后将数据插入生产表中。

## <a name="schema-names"></a>架构名称

架构是组合以类似方式使用的对象的好方法。 以下代码创建名为 wwi 的[用户定义的架构](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>表名

如果要将多个数据库从 prem 解决方案迁移到 SQL 池，最佳做法是将所有事实、维度和集成表迁移到一个 SQL 池架构。 例如，可将所有表存储在 [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 示例数据仓库中一个名为 wwi 的架构内。

要在 SQL 池中显示表的组织，可以使用事实、暗和 int 作为表名称的前缀。 下表显示了 WideWorld 导入者DW 的一些架构和表名称。  

| WideWorldImportersDW 表  | 表类型 | SQL 池 |
|:-----|:-----|:------|:-----|
| 城市 | 维度 | wwi.DimCity |
| 订单 | Fact | wwi.FactOrder |

## <a name="table-persistence"></a>表暂留

表将数据永久存储在 Azure 存储中、临时在 Azure 存储中或数据存储在数据仓库外部的数据存储中。

### <a name="regular-table"></a>常规表

常规表将 Azure 存储中的数据存储为数据仓库的一部分。 无论会话是否打开，表和数据都将保留。  下面的示例创建一个包含两列的常规表。

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>临时表

临时表只在会话持续期间存在。 您可以使用临时表来防止其他用户看到临时结果。 使用临时表也会减少清理的需要。  临时表利用本地存储，在 SQL 池中可以提供更快的性能。  

SQL 按需支持临时表。 但是，它的用法是有限的，因为您可以从临时表中选择，但不能将其与存储中的文件联接。

有关详细信息，请参阅[临时表](develop-tables-temporary.md)。

### <a name="external-table"></a>外部表

[外部表](develop-tables-external-tables.md)指向位于 Azure 存储 Blob 或 Azure 数据湖存储中的数据。

使用["创建表作为选择](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)"语句将数据从外部表导入 SQL 池。 有关加载教程，请参阅[使用 PolyBase 从 Azure Blob 存储加载数据](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

对于 SQL 按需，可以使用[CETAS](develop-tables-cetas.md)将查询结果保存到 Azure 存储中的外部表。

## <a name="data-types"></a>数据类型

SQL 池支持最常用的数据类型。 有关受支持数据类型的列表，请参阅 CREATE TABLE 语句中的 [CREATE TABLE 引用中的数据类型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes)。 有关使用数据类型的详细信息，请参阅[数据类型](../sql/develop-tables-data-types.md)。

## <a name="distributed-tables"></a>分布式表

SQL 池的一个基本功能是它可以跨[分布](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)在表上存储和操作的方式。  SQL 池支持三种数据分发方法：

- 轮循机制（默认）
- 哈希
- 复制

### <a name="hash-distributed-tables"></a>哈希分布表

哈希分布表根据分布列中的值来分布行。 根据设计，在对大型表进行查询时，哈希分布表可以实现高性能。 选择分布列时，需考虑多项因素。

有关详细信息，请参阅[分布式表的设计准则](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="replicated-tables"></a>复制表

复制表在每个计算节点上提供表的完整副本。 查询在复制的表上运行很快，因为复制的表上的联接不需要数据移动。 但是，复制需要额外的存储，对于大型表来说并不实用。

有关详细信息，请参阅[复制表的设计准则](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="round-robin-tables"></a>循环表

循环表将表行均匀地分布到所有分布区中。 行将随机分布。 将数据加载到循环表中的速度很快。  但是，查询可能需要比其他分发方法更多的数据移动。

有关详细信息，请参阅[分布式表的设计准则](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="common-distribution-methods-for-tables"></a>表的常用分布方法

表类别通常确定表分布的最佳选项。

| 表类别 | 建议的分布选项 |
|:---------------|:--------------------|
| Fact           | 结合聚集列存储索引使用哈希分布。 在同一个分布列中联接两个哈希表时，可以提高性能。 |
| 维度      | 对小型表使用复制表。 如果表太大，以致无法在每个计算节点上存储，可以使用哈希分布式表。 |
| 过渡        | 对临时表使用轮循机制表。 使用 CTAS 执行加载的速度较快。 将数据存储到临时表后，可以使用 INSERT...SELECT 将数据移到生产表。 |

## <a name="partitions"></a>分区

在 SQL 池中，分区的表根据数据范围存储并执行表行的操作。 例如，可以按日、月或年将某个表分区。 可以通过分区消除来提高查询性能，否则查询扫描范围将限制为分区中的数据。

还可以通过分区切换来维护数据。 由于 SQL 池中的数据已经分发，因此过多的分区可能会降低查询性能。 有关详细信息，请参阅[分区指南](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。  

> [!TIP]
> 以分区切换的方式切换成不为空的表分区时，若要截断现有数据，可考虑在 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 语句中使用 TRUNCATE_TARGET 选项。

以下代码将转换后的数据转换为 SalesFact 分区并覆盖任何现有数据。

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

在 SQL 按需中，您可以限制查询将读取的文件/文件夹（分区）。 使用[查询存储文件](develop-storage-files-overview.md)中描述的文件路径和文件信息函数支持按路径分区。 下面的示例读取包含 2017 年数据的文件夹：

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>列存储索引

默认情况下，SQL 池将表存储为群集列存储索引。 对于大型表而言，这种数据存储形式可以实现较高的数据压缩率和查询性能。  聚集列存储索引通常是最佳选择，但在某些情况下，聚集索引或堆是适当的存储结构。  

> [!TIP]
> 堆表对于加载瞬态数据（如过渡表）特别有用，该暂存表将转换为最终表。

有关列存储功能的列表，请参阅[列存储索引的新增功能](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 若要提高列存储索引性能，请参阅[最大化列存储索引的行组质量](../sql/data-load-columnstore-compression.md)。

## <a name="statistics"></a>统计信息


查询优化器在创建用于执行查询的计划时，使用列级统计信息。 若要提高查询性能，必须有基于各个列（尤其是查询联接中使用的列）的统计信息。 Synapse SQL 支持自动创建统计信息。 

统计更新不会自动进行。 添加或更改了大量的行之后更新统计信息。 例如，在加载后更新统计信息。 ["统计信息"指导](develop-tables-statistics.md)文章中提供了其他信息。

## <a name="primary-key-and-unique-key"></a>主键和唯一键

仅当同时使用 NONCLUSTERED 和 NOT ENFORCED 时才支持 PRIMARY KEY。  仅当使用"不强制"时，才支持 UNIQUE 约束。  有关详细信息，请参阅 SQL[池表约束](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="commands-for-creating-tables"></a>用于创建表的命令

可以创建一个新的空表。 还可以创建一个表并在其中填充 select 语句的结果。 下面是用于创建表的 T-SQL 命令。

| T-SQL 语句 | 说明 |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 通过定义所有表列和选项来创建空表。 |
| [创建外部表](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 创建外部表。 表的定义存储在 SQL 池中。 表数据存储在 Azure Blob 存储或 Azure 数据湖存储中。 |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 在新表中填充 select 语句的结果。 表列和数据类型基于 select 语句的结果。 若要导入数据，此语句可从外部表中进行选择。 |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 通过将 select 语句的结果导出到外部位置，来创建新的外部表。  位置是 Azure Blob 存储或 Azure 数据湖存储。 |

## <a name="aligning-source-data-with-the-data-warehouse"></a>使源数据与数据仓库相符

从其他数据源加载数据可以填充数据仓库表。 要实现成功加载，源数据中的列的数量和数据类型必须与数据仓库中的表定义一致。

> [!NOTE]
> 使数据相符可能是设计表时的最难部分。

如果数据来自多个数据存储，则可以将数据移植到数据仓库并将其存储在集成表中。 数据在集成表中后，可以使用 SQL 池的强大功能来实现转换操作。 准备好数据后，可以将其插入到生产表中。

## <a name="unsupported-table-features"></a>不支持的表功能

SQL 池支持其他数据库提供的许多（但不是全部）表功能。  下面的列表显示了 SQL 池中不支持的一些表功能。

- 外键，检查[表约束](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [计算列](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [索引视图](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [序列](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [稀疏列](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- 代理密钥，使用[标识](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)实现
- [同义词](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [触发器](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [唯一索引](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [用户定义类型](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>表大小查询

标识 60 个分布中每个分布中表占用的空间和行的一种简单方法是使用[DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

请记住，使用 DBCC 命令可能相当有限。  动态管理视图 (DMV) 显示的信息比 DBCC 命令更详细。 首先创建下面的视图。

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>表空间摘要

此查询返回行以及按表划分的空间。  表空间摘要允许您查看哪些表是最大表。 您还将查看它们是循环、复制还是哈希分布。  对于哈希分布式表，此查询会显示分布列。  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>按分布类型划分的表空间

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>按索引类型划分的表空间

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>分布空间摘要

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>后续步骤

为数据仓库创建表后，接下来可将数据载入该表。  有关加载教程，请参阅[将数据加载到 SQL 池](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)中。
