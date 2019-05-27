---
title: 创建、更新统计信息 - Azure SQL 数据仓库 | Microsoft Docs
description: 用于创建和更新 Azure SQL 数据仓库中表的查询优化统计信息的建议和示例。
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: c5043d99dd130bc7dc7b35eaa5ecadf11d7644db
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851525"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的表统计信息

用于创建和更新 Azure SQL 数据仓库中表的查询优化统计信息的建议和示例。

## <a name="why-use-statistics"></a>为何使用统计信息

Azure SQL 数据仓库对数据了解得越多，其针对数据执行查询的速度就越快。 将数据加载到 SQL 数据仓库，收集有关你的数据统计信息之后可以如何优化查询的最重要事情之一。 SQL 数据仓库查询优化器是基于成本的优化器。 它的各种查询计划，成本进行比较，并选择成本最低的计划。 在大多数情况下，还选择将执行速度最快的计划。 例如，如果优化器估计查询筛选的日期会返回一个行它将选择一个计划。 如果估计的所选的日期会返回 1 百万行，它将返回一个不同的计划。

## <a name="automatic-creation-of-statistic"></a>自动创建统计信息

当数据库 AUTO_CREATE_STATISTICS 选项时，SQL 数据仓库分析传入的用户查询有关缺少的统计信息。 如果缺少统计信息，查询优化器改进查询计划的基数估计在查询谓词或联接条件中的单独列上创建统计信息。 默认情况下，自动创建统计信息目前处于开启状态。

可以检查您的数据仓库是否有 AUTO_CREATE_STATISTICS 配置通过运行以下命令：

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

如果您的数据仓库不具有 AUTO_CREATE_STATISTICS 配置，我们建议通过运行以下命令启用此属性：

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

这些语句将触发自动创建的统计信息：

- 选择
- INSERT-SELECT
- CTAS
- UPDATE
- DELETE
- 解释包含联接或检测到的谓词的状态显示

> [!NOTE]
> 不在临时或外部表上创建“自动创建统计信息”。

自动创建的统计信息是以同步方式完成，因此如果缺少列统计信息可能会略有降低的查询性能。 若要为单个列创建统计信息的时间取决于表的大小。 若要避免可测量的性能下降，尤其是在性能基准测试，应确保首先通过分析系统前执行基准工作负荷创建统计信息。

> [!NOTE]
> 将在中记录的统计信息创建[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)其他用户上下文。

创建自动统计信息时，它们将采用以下格式：_WA_Sys_<以十六进制表示的 8 位列 ID>_<以十六进制表示的 8 位表 ID>。 您可以查看已通过运行创建的统计信息[DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest)命令：

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name 是表的包含要显示的统计信息的名称。 该表不能为外部表。 目标是目标索引、 统计信息或为其显示统计信息列的名称。

## <a name="updating-statistics"></a>更新统计信息

最佳实践之一是每天在添加新日期后，更新有关日期列的统计信息。 每次有新行载入数据仓库时，就会添加新的加载日期或事务日期。 这些操作会更改数据分布情况并使统计信息过时。 相反地，customer 表中的国家/地区列的统计信息可能永远不会需要更新，因为值的分布通常不会变化。 假设客户间的分布固定不变，将新行添加到表变化并不会改变数据分布情况。 但是，如果您的数据仓库只包含一个国家/地区，并且从新的国家/地区中的数据引入，从而导致数据从多个国家/地区存储，则需要更新有关国家/地区列的统计信息。

下面是关于更新统计信息的建议：

|||
|-|-|
| **统计信息更新频率**  | 保守：每日 </br> 加载或转换数据之后 |
| **采样** |  不超过 10 亿行，请使用默认采样 （20%)。 </br> 使用超过 1 亿行，使用 %2%的采样。 |

在排查查询问题时，首先要询问的问题之一就是 **“统计信息是最新的吗？”**

此问题不可以根据数据期限提供答案。 如果对基础数据未做重大更改，则最新的统计信息对象有可能非常陈旧。 如果行数有明显变化或给定列的值分布有重大变化，则需要更新统计信息。

没有动态管理视图来确定更新的最后一个时间统计信息以来表中的数据是否发生更改。 了解统计信息的保留时间可以为您的图片部分。 可以使用以下查询来确定上次更新每个表的统计信息的时间。

> [!NOTE]
> 如果没有重大变化的分布列的值，则应更新而不考虑上次更新统计信息。

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

例如，数据仓库中的**日期列**往往需要经常更新统计信息。 每次有新行载入数据仓库时，就会添加新的加载日期或事务日期。 这些操作会更改数据分布情况并使统计信息过时。 相反地，客户表上性别列的统计信息可能永远不需要更新。 假设客户间的分布固定不变，将新行添加到表变化并不会改变数据分布情况。 不过，如果数据仓库只包含一种性别，而新的要求导致多种性别，则需要更新性别列的统计信息。

有关详细信息，请参阅[统计信息](/sql/relational-databases/statistics/statistics)的通用指南。

## <a name="implementing-statistics-management"></a>实施统计信息管理

扩展数据加载过程通常是个不错的想法，可确保在加载结束时更新统计信息。 当表更改其大小和/或其值分布时，数据加载最为频繁。 因此，这是实施某些管理过程的合理位置。

下面提供了有关在加载过程中更新统计信息的一些指导原则：

* 确保加载的每个表至少包含一个更新的统计信息对象。 这会在统计信息更新过程中更新表大小（行计数和页计数）信息。
* 将重点放在参与 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的列上。
* 考虑更频繁地更新“递增键”列（例如事务日期），因为这些值不包含在统计信息直方图中。
* 考虑较不经常更新静态分布列。
* 请记住，每个统计信息对象是按顺序更新的。 仅实现 `UPDATE STATISTICS <TABLE_NAME>` 不一定总很理想 - 尤其是对包含许多统计信息对象的宽型表而言。

有关详细信息，请参阅[基数估计](/sql/relational-databases/performance/cardinality-estimation-sql-server)。

## <a name="examples-create-statistics"></a>示例：创建统计信息

以下示例演示如何使用各种选项来创建统计信息。 用于每个列的选项取决于数据特征以及在查询中使用列的方式。

### <a name="create-single-column-statistics-with-default-options"></a>使用默认选项创建单列统计信息

若要基于某个列创建统计信息，只需提供统计信息对象的名称和列的名称。

此语法使用所有默认选项。 默认情况下，SQL 数据仓库在创建统计信息时对 **20%** 的表采样。

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

例如:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>通过检查每个行创建单列统计信息

20% 的默认采样率足以应付大多数情况。 不过，可以调整采样率。

若要采样整个表，请使用此语法：

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

例如:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>通过指定样本大小创建单列统计信息

或者，可以以百分比指定样本大小：

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>只对某些行创建单列统计信息

也可以对表中的部分行创建统计信息。 这称为筛选的统计信息。

例如，在计划查询大型分区表的特定分区时，可以使用筛选的统计信息。 只对分区值创建统计信息，统计信息的准确度会改善，并因而改善查询性能。

此示例会基于一系列的值创建统计信息。 可以轻松定义这些值以匹配分区中的值范围。

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> 若要让查询优化器在选择分布式查询计划时考虑使用筛选的统计信息，查询必须符合统计信息对象的定义。 使用上述示例，查询的 WHERE 子句需要指定介于 2000101 和 20001231 之间的 col1 值。

### <a name="create-single-column-statistics-with-all-the-options"></a>使用所有选项创建单列统计信息

也可以将选项组合在一起。 以下示例使用自定义样本大小创建筛选的统计信息对象：

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

有关完整参考，请参阅 [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql)。

### <a name="create-multi-column-statistics"></a>创建多列统计信息

若要创建多列统计信息对象，只需使用上述示例，但要指定更多的列。

> [!NOTE]
> 用于估计查询结果中行数的直方图只适用于统计信息对象定义中所列的第一个列。

在此示例中，直方图位于 product\_category。 跨列统计信息是根据 *product\_category* 和 *product\_sub_category* 计算的：

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

由于 *product\_category* 与 *product\_sub\_category* 之间存在关联，因此在同时访问这些列时，多列统计信息对象相当有用。

### <a name="create-statistics-on-all-columns-in-a-table"></a>基于表中的所有列创建统计信息

创建统计信息的方法之一是在创建表后发出 CREATE STATISTICS 命令：

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>使用存储过程基于数据库中的所有列创建统计信息

SQL 数据仓库不提供相当于 SQL Server 中 sp_create_stats 的系统存储过程。 此存储过程将基于数据库中尚不包含统计信息的每个列创建单列统计信息对象。

以下示例可以帮助你开始进行数据库设计。 可以根据需要任意改写此存储过程：

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

若要使用默认值表中的所有列创建统计信息，请执行存储的过程。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

要通过全面扫描基于表中的所有列创建统计信息，只需调用该过程即可：

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

要对表中的所有列创建示例统计信息，请输入 3 和示例百分比。 此过程使用 20% 的采样率。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

基于所有列创建示例统计信息

## <a name="examples-update-statistics"></a>示例：Update statistics

要更新统计信息，可以：

- 更新一个统计信息对象。 指定要更新的统计信息对象名称。
- 更新表中的所有统计信息对象。 指定表名称，而不是一个特定的统计信息对象。

### <a name="update-one-specific-statistics-object"></a>更新一个特定的统计信息对象

使用以下语法来更新特定的统计信息对象：

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

例如:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

通过更新特定统计信息对象，可以减少管理统计信息所需的时间和资源。 在选择要更新的最佳统计信息对象之前，需要经过一定的思考。

### <a name="update-all-statistics-on-a-table"></a>更新表中的所有统计信息

更新表中的所有统计信息对象的简单方法是：

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

例如:

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS 语句是易于使用。 只要记住，这会更新表中的所有统计信息，因此执行的工作可能会超过所需的数量。 如果性能不是一个问题，这是保证统计信息是最新的最简单、 最全面方法。

> [!NOTE]
> 更新表中的所有统计信息时，SQL 数据仓库将执行扫描，以针对每个统计信息对象进行表采样。 如果表很大、包含许多列和许多统计信息，则根据需要更新各项统计信息可能比较有效率。

有关 `UPDATE STATISTICS` 过程的实现，请参阅[临时表](sql-data-warehouse-tables-temporary.md)。 实现方法与上述 `CREATE STATISTICS` 过程略有不同，但最终结果相同。

有关完整语法，请参阅[更新统计信息](/sql/t-sql/statements/update-statistics-transact-sql)。

## <a name="statistics-metadata"></a>统计信息元数据

可以使用多个系统视图和函数来查找有关统计信息的信息。 例如，使用 stats-date 函数查看上次创建或更新统计信息的时间，即可了解统计信息对象是否可能已过时。

### <a name="catalog-views-for-statistics"></a>统计信息的目录视图

这些系统视图提供有关统计信息的信息：

| 目录视图 | 描述 |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |针对每个列提供一行。 |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |针对数据库中的每个对象提供一行。 |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |针对数据库中的每个架构提供一行。 |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |针对每个统计信息对象提供一行。 |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |针对统计信息对象中的每个列提供一行。 链接回到 sys.columns。 |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |针对每个表（包括外部表）提供一行。 |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |针对每个数据类型提供一行。 |

### <a name="system-functions-for-statistics"></a>统计信息的系统函数

这些系统函数适合用于处理统计信息：

| 系统函数 | 描述 |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |上次更新统计信息对象的日期。 |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |有关统计信息对象识别的值分布的摘要级别和详细信息。 |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>将统计信息列和函数合并成一个视图

此视图将统计信息相关的列以及 STATS_DATE() 函数的结果合并在一起。

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>DBCC SHOW_STATISTICS() 示例

DBCC SHOW_STATISTICS() 显示统计信息对象中保存的数据。 这些数据包括三个组成部分：

- 页眉
- 密度矢量
- 直方图

有关统计信息的标头元数据。 直方图显示统计信息对象的第一个键列中的值分布。 密度向量可度量跨列相关性。 SQL 数据仓库使用统计信息对象中的任何数据来计算基数估计值。

### <a name="show-header-density-and-histogram"></a>显示标头、密度和直方图

此简单示例显示了统计信息对象的所有三个组成部分：

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

例如:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>显示 DBCC SHOW_STATISTICS() 的一个或多个组成部分

如果只想要查看特定部分，请使用 `WITH` 子句并指定要查看哪些部分：

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

例如:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() 差异

相比于 SQL Server，在 SQL 数据仓库中，DBCC SHOW_STATISTICS() 的实现更加严格：

- 未阐述的功能不受支持。
- 不能使用 Stats_stream。
- 不能联接特定统计信息子集的结果。 例如，STAT_HEADER JOIN DENSITY_VECTOR。
- 不能针对消息隐藏设置 NO_INFOMSGS。
- 不能在统计信息名称的前后使用方括号。
- 不能使用列名来标识统计信息对象。
- 不支持自定义错误 2767。

## <a name="next-steps"></a>后续步骤

有关进一步提升查询性能的信息，请参阅[监视工作负荷](sql-data-warehouse-manage-monitor.md)
