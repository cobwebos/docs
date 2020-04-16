---
title: 创建、更新统计信息
description: 在 SynapsE SQL 中创建和更新查询优化统计信息的建议和示例。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 798fec4dacb33a9f16de319062baf12adaffdbd0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428740"
---
# <a name="statistics-in-synapse-sql"></a>SynapsE SQL 中的统计信息

本文中提供了使用 Synapse SQL 资源创建和更新查询优化统计信息的建议和示例：SQL 池和 SQL 按需（预览）。

## <a name="statistics-in-sql-pool"></a>SQL 池中的统计信息

### <a name="why-use-statistics"></a>为何使用统计信息

SQL 池资源对数据了解得越多，执行查询的速度就越快。 将数据加载到 SQL 池后，收集数据的统计信息是可用于查询优化的最重要操作之一。  

SQL 池查询优化器是基于成本的优化器。 此优化器会对各种查询计划的成本进行比较，并选择成本最低的计划。 在大多数情况下，它会选择执行速度最快的计划。

例如，如果优化器估计查询筛选的日期会返回一行数据，则它会选择一个计划。 如果优化器估计选定的日期会返回 1 百万行数据，则它会返回另一个计划。

### <a name="automatic-creation-of-statistics"></a>自动创建统计信息

当数据库AUTO_CREATE_STATISTICS选项设置为 时，SQL 池将分析传入用户查询中缺少的`ON`统计信息。  如果缺少统计信息，查询优化器将创建查询谓词或联接条件中各个列的统计信息。 此功能用于改进查询计划的基数估计。

> [!IMPORTANT]
> 默认情况下，自动创建统计信息目前处于开启状态。

可以运行以下命令来检查是否为数据仓库配置了 AUTO_CREATE_STATISTICS：

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

如果数据仓库未启用AUTO_CREATE_STATISTICS，我们建议您通过运行以下命令启用此属性：

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

这些语句将触发统计信息的自动创建：

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- DELETE
- EXPLAIN

> [!NOTE]
> 不自动创建统计信息，不会在临时表或外部表上生成统计信息。

自动创建统计信息是同步完成的。 因此，如果列缺少统计信息，则查询性能可能会略有下降。 为单个列创建统计信息所需的时间取决于表的大小。

为了避免可衡量的性能下降，您应该首先通过在分析系统之前执行基准工作负载来创建统计信息。

> [!NOTE]
> 统计信息的创建记录在不同的用户上下文中的[sys.dm_pdw_exec_requests。](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

创建自动统计信息时，它们将采用以下形式 _：WA_Sys_十六进制>_的 8 位列 id<<十六进制>中的 8 位表 ID。 您可以通过运行[DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)命令来查看已创建的统计信息：

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name是包含要显示的统计信息的表的名称，该统计信息不能是外部表。 目标是要显示统计信息的目标索引、统计信息或列的名称。

### <a name="update-statistics"></a>更新统计信息

最佳实践之一是每天在添加新日期后，更新有关日期列的统计信息。 每次有新行载入数据仓库时，就会添加新的加载日期或事务日期。 这些添加将更改数据分布并使统计信息过时。

客户表中国家或地区列的统计信息可能永远不需要更新，因为值分布通常不会更改。 假设客户间的分布固定不变，将新行添加到表变化并不会改变数据分布情况。

但是，当数据仓库仅包含一个国家或地区，并且您带来了来自新国家或地区的数据时，您需要更新国家或地区列的统计信息。

下面是关于更新统计信息的建议：

|||
|-|-|
| **统计信息更新频率**  | 保守：每日 </br> 加载或转换数据之后 |
| **采样** |  如果行数少于 10 亿，则使用默认采样率 (20%)。 </br> 如果行数超过 10 亿，则使用 2% 的采样率。 |

### <a name="determine-last-statistics-update"></a>确定上次统计信息更新

在排查查询问题时，首先要询问的问题之一就是 **“统计信息是最新的吗？”**

这个问题不是按数据年龄可以回答的问题。 如果对基础数据未做重大更改，则最新的统计信息对象有可能非常陈旧。 当行数发生实质性更改，或者列的值分布发生重大更改*时，是时候*更新统计信息了。

没有动态管理视图可用于确定自上次更新统计信息以来表中的数据是否已更改。 如果知道统计信息的期限，可以大致猜出更新状态。 可以使用以下查询来确定上次更新每个表的统计信息的时间。

> [!NOTE]
> 如果给定列的值分布有重大变化，则应该更新统计信息，不管上次更新时间为何。

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

例如，数据仓库中**的日期列**通常需要频繁的统计信息更新。 每次有新行载入数据仓库时，就会添加新的加载日期或事务日期。 这些添加将更改数据分布并使统计信息过时。

客户表中的性别列统计信息可能永远不需要更新。 假设客户间的分布固定不变，将新行添加到表变化并不会改变数据分布情况。

但是，如果数据仓库仅包含一个性别，并且新的需求导致多个性别，则需要更新性别列的统计信息。 有关详细信息，请查看["统计信息"](/sql/relational-databases/statistics/statistics)一文。

### <a name="implementing-statistics-management"></a>实施统计信息管理

通常最好扩展数据加载过程，以确保在加载结束时更新统计信息。 数据加载是表最常更改其大小、值分布或两者时。 因此，加载过程是实现某些管理过程的逻辑位置。

下面提供了有关在加载过程中更新统计信息的一些指导原则：

- 确保加载的每个表至少包含一个更新的统计信息对象。 此过程将更新表大小（行计数和页数）信息作为统计信息更新的一部分。
- 将重点放在参与 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的列上。
- 请考虑更频繁地更新"提升键"列（如事务日期），因为这些值不会包含在统计直方图中。
- 考虑较不经常更新静态分布列。
- 请记住，每个统计信息对象是按顺序更新的。 仅实现 `UPDATE STATISTICS <TABLE_NAME>` 不一定总很理想 - 尤其是对包含许多统计信息对象的宽型表而言。

有关详细信息，请参阅[基数估计](/sql/relational-databases/performance/cardinality-estimation-sql-server)。

### <a name="examples-create-statistics"></a>示例：创建统计信息

以下示例演示如何使用各种选项来创建统计信息。 用于每个列的选项取决于数据特征以及在查询中使用列的方式。

#### <a name="create-single-column-statistics-with-default-options"></a>使用默认选项创建单列统计信息

要对列创建统计信息，请为统计信息对象提供名称和列的名称。
此语法使用所有默认选项。 默认情况下，SQL 池在创建统计信息时对表的**20% 进行**采样。

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

例如：

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>通过检查每个行创建单列统计信息

20% 的默认采样率足以应付大多数情况。 不过，可以调整采样率。 若要采样整个表，请使用此语法：

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

例如：

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>通过指定样本大小创建单列统计信息

另一个选项是指定样本大小为百分比：

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>只对某些行创建单列统计信息

您还可以在表中的行的一部分创建统计信息，这称为筛选统计信息。

例如，在计划查询大型分区表的特定分区时，可以使用筛选的统计信息。 通过仅创建分区值的统计信息，统计信息的准确性将提高。 您还将体验到查询性能的提高。

此示例会基于一系列的值创建统计信息。 可以轻松定义这些值以匹配分区中的值范围。

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> 若要让查询优化器在选择分布式查询计划时考虑使用筛选的统计信息，查询必须符合统计信息对象的定义。 使用上述示例，查询的 WHERE 子句需要指定介于 2000101 和 20001231 之间的 col1 值。

#### <a name="create-single-column-statistics-with-all-the-options"></a>使用所有选项创建单列统计信息

也可以将选项组合在一起。 以下示例使用自定义样本大小创建筛选的统计信息对象：

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

有关完整参考，请参阅 [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

#### <a name="create-multi-column-statistics"></a>创建多列统计信息

要创建多列统计信息对象，请使用前面的示例，但指定更多列。

> [!NOTE]
> 用于估计查询结果中行数的直方图只适用于统计信息对象定义中所列的第一个列。

在此示例中，直方图位于 product\_category**。 跨栏统计信息根据*产品\_类别*和*产品\_sub_category*计算：

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

由于*产品\_类别*和*\_产品子\_类别*之间存在关联，因此，如果同时访问这些列，则多列统计信息对象可能很有用。

#### <a name="create-statistics-on-all-columns-in-a-table"></a>基于表中的所有列创建统计信息

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>使用存储过程基于数据库中的所有列创建统计信息

SQL 池没有等效于 SQL Server 中sp_create_stats的系统存储过程。 此存储过程将基于数据库中尚不包含统计信息的每个列创建单列统计信息对象。
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

若要使用默认设置基于表中的所有列创建统计信息，请执行存储过程。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

要通过全面扫描基于表中的所有列创建统计信息，只需调用该过程即可：

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

要对表中的所有列创建示例统计信息，请输入 3 和示例百分比。 以下步骤使用 20% 的采样率。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>示例：更新统计信息

要更新统计信息，可以：

- 更新一个统计信息对象。 指定要更新的统计信息对象名称。
- 更新表中的所有统计信息对象。 指定表名称，而不是一个特定的统计信息对象。

#### <a name="update-one-specific-statistics-object"></a>更新一个特定的统计信息对象

使用以下语法来更新特定的统计信息对象：

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

例如：

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

通过更新特定统计信息对象，可以减少管理统计信息所需的时间和资源。 此操作需要一些考虑才能选择要更新的最佳统计信息对象。

#### <a name="update-all-statistics-on-a-table"></a>更新表的所有统计信息

用于更新表中所有统计信息对象的一个简单方法如下：

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

例如：

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS 语句很容易使用。 请记住，它更新了表上*的所有*统计信息，从而引发了比所需的更多工作。 如果性能不是问题，则此方法是保证统计信息是最新的最简单、最完整的方法。

> [!NOTE]
> 更新表上的所有统计信息时，SQL 池执行扫描，以采样每个统计信息对象的表。 如果表很大、包含许多列和许多统计信息，则根据需要更新各项统计信息可能比较有效率。

有关`UPDATE STATISTICS`过程的实现，请参阅[临时表](develop-tables-temporary.md)。 实现方法与上述 `CREATE STATISTICS` 过程略有不同，但最终结果相同。
有关完整语法，请参阅[更新统计信息](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="statistics-metadata"></a>统计信息元数据

可以使用多个系统视图和函数来查找有关统计信息的信息。 例如，可以使用 STATS_DATE（） 函数查看统计信息对象是否可能已过期。 STATS_DATE（） 允许您查看上次创建或更新统计信息的时间。

#### <a name="catalog-views-for-statistics"></a>统计信息的目录视图

这些系统视图提供有关统计信息的信息：

| 目录视图 | 说明 |
|:--- |:--- |
| [sys.列](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |针对每个列提供一行。 |
| [sys.对象](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |针对数据库中的每个对象提供一行。 |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |针对数据库中的每个架构提供一行。 |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |针对每个统计信息对象提供一行。 |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |针对统计信息对象中的每个列提供一行。 链接回到 sys.columns。 |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |针对每个表（包括外部表）提供一行。 |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |针对每个数据类型提供一行。 |

#### <a name="system-functions-for-statistics"></a>统计信息的系统函数

这些系统函数适合用于处理统计信息：

| 系统函数 | 说明 |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |上次更新统计信息对象的日期。 |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |有关统计信息对象识别的值分布的摘要级别和详细信息。 |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>将统计信息列和函数合并成一个视图

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

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() 示例

DBCC SHOW_STATISTICS() 显示统计信息对象中保存的数据。 这些数据包括三个组成部分：

- 标头
- 密度矢量
- 直方图

标头是关于统计信息的元数据。 直方图显示统计信息对象的第一个键列中的值分布。 密度向量可度量跨列相关性。 SQL 池使用统计信息对象中的任何数据计算基数估计值。

#### <a name="show-header-density-and-histogram"></a>显示标头、密度和直方图

此简单示例显示了统计信息对象的所有三个组成部分：

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

例如：

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>显示 DBCC SHOW_STATISTICS() 的一个或多个组成部分

如果只想要查看特定部分，请使用 `WITH` 子句并指定要查看哪些部分：

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

例如：

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() 差异

与 SQL Server 相比，DBCC SHOW_STATISTICS（） 在 SQL 池中实现更加严格：

- 不支持未记录的功能。
- 不能使用Stats_stream。
- 无法联接统计信息特定子集的结果。 例如 STAT_HEADER JOIN DENSITY_VECTOR。
- 无法为消息抑制设置NO_INFOMSGS。
- 不能使用统计信息名称周围的方括号。
- 不能使用列名称来标识统计信息对象。
- 不支持自定义错误 2767。

### <a name="next-steps"></a>后续步骤

有关进一步提升查询性能的信息，请参阅[监视工作负荷](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>SQL 按需统计（预览）

根据特定数据集（存储路径）的特定列创建统计信息。

### <a name="why-use-statistics"></a>为何使用统计信息

SQL 按需（预览）对数据了解得越多，对数据的查询执行速度就越快。 收集有关数据的统计信息是优化查询的最重要操作之一。 SQL 按需查询优化器是基于成本的优化器。 此优化器会对各种查询计划的成本进行比较，并选择成本最低的计划。 在大多数情况下，它会选择执行速度最快的计划。 例如，如果优化器估计查询筛选的日期会返回一行数据，则它会选择一个计划。 如果优化器估计选定的日期会返回 1 百万行数据，则它会返回另一个计划。

### <a name="automatic-creation-of-statistics"></a>自动创建统计信息

SQL 按需分析传入用户查询，以求缺少统计信息。 如果缺少统计信息，查询优化器将在查询谓词或联接条件中各个列上创建统计信息，以改进查询计划的基数估计。

SELECT 语句将触发自动创建统计信息。

> [!NOTE]
> 自动创建 Parquet 文件的统计信息。 对于 CSV 文件，您需要手动创建统计信息，直到支持自动创建 CSV 文件统计信息。

自动创建统计信息的过程是以同步方式完成的，因此，如果列中缺少统计信息，查询性能可能会轻微下降。 为单个列创建统计信息的时间取决于目标文件的大小。

### <a name="manual-creation-of-statistics"></a>手动创建统计数据

SQL 按需允许您手动创建统计信息。 对于 CSV 文件，您必须手动创建统计信息，因为 CSV 文件的自动创建统计信息不会打开。 有关如何手动创建统计信息的说明，请参阅以下示例。

### <a name="updating-statistics"></a>更新统计信息

对文件中的数据的更改、删除和添加文件会导致数据分发更改，并使统计信息过时。 在这种情况下，需要更新统计信息。

如果数据发生显著变化，SQL 按需自动重新创建统计信息。 每次自动创建统计信息时，也会保存数据集的当前状态：文件路径、大小、上次修改日期。

当统计信息过时时，将创建新统计信息。 算法遍遍数据，并将其与数据集的当前状态进行比较。 如果更改的大小大于特定阈值，则删除旧统计信息，并将在新数据集上重新创建。

手动统计信息永远不会声明过时。

> [!NOTE]
> 为 Parquet 文件打开统计信息的自动重新使用。 对于 CSV 文件，您需要手动删除和创建统计信息，直到支持自动创建 CSV 文件统计信息。 请查看以下有关如何删除和创建统计信息的示例。

在排查查询问题时，首先要询问的问题之一就是 **“统计信息是最新的吗？”**

当行数发生显著变化，或者列的值分布发生重大变化*时，是时候*更新统计信息了。

> [!NOTE]
> 如果给定列的值分布有重大变化，则应该更新统计信息，不管上次更新时间为何。

### <a name="implementing-statistics-management"></a>实施统计信息管理

您可能希望扩展数据管道，以确保在通过添加、删除或更改文件显著更改数据时更新统计信息。

更新统计信息提供了以下指导原则：

- 确保数据集至少更新了一个统计信息对象。 这将更新大小（行计数和页数）信息作为统计信息更新的一部分。
- 将重点放在参与 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的列上。
- 更频繁地更新"提升键"列（如事务日期），因为这些值不会包含在统计直方图中。
- 更新静态分布列的频率较低。

有关详细信息，请参阅[基数估计](/sql/relational-databases/performance/cardinality-estimation-sql-server)。

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>示例：为 OPENROWSET 路径中的列创建统计信息

以下示例演示如何使用各种选项创建统计信息。 用于每个列的选项取决于数据特征以及在查询中使用列的方式。

> [!NOTE]
> 此时只能创建单列统计信息。

以下存储过程用于创建统计信息：

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

参数： [ @stmt ] n'statement_text' - 指定一个 Transact-SQL 语句，该语句将返回用于统计的列值。 您可以使用 TABLESAMPLE 指定要使用的数据样本。 如果未指定 TABLESAMPLE，将使用 FULLSCAN。

```sql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV 采样目前不起作用，CSV 仅支持 FULLSCAN。

#### <a name="create-single-column-statistics-by-examining-every-row"></a>通过检查每个行创建单列统计信息

要对列创建统计信息，请提供返回需要统计信息的列的查询。

默认情况下，如果不另行指定，SQL 按需在创建统计信息时会使用数据集中 100% 的数据。

例如，要根据总体.csv 文件为数据集的一年列创建具有默认选项 （FULLSCAN） 的统计信息：

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>通过指定样本大小创建单列统计信息

您可以将样本大小指定为百分比：

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>示例：更新统计信息

要更新统计信息，您需要删除并创建统计信息。 以下存储过程用于删除统计信息：

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

参数： [ @stmt ] N'statement_text' - 指定创建统计信息时使用的相同 Transact-SQL 语句。

要更新基于 sands.csv 文件的数据集中的年份列的统计信息，您需要删除并创建统计信息：

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>示例：为外部表列创建统计信息

以下示例演示如何使用各种选项创建统计信息。 用于每个列的选项取决于数据特征以及在查询中使用列的方式。

> [!NOTE]
> 此时只能创建单列统计信息。

要对列创建统计信息，请为统计信息对象提供名称和列的名称。

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

参数：external_table指定应创建统计信息的外部表。

通过扫描所有行来计算统计信息。 FULLSCAN 和 SAMPLE 100 PERCENT 的结果相同。 FULLSCAN 不能与 SAMPLE 选项一起使用。

SAMPLE 编号%指定查询优化器创建统计信息时使用的表或索引视图中的大致百分比或行数。 数字可以从 0 到 100。

SAMPLE 不能与 FULLSCAN 选项一起使用。

> [!NOTE]
> CSV 采样目前不起作用，CSV 仅支持 FULLSCAN。

#### <a name="create-single-column-statistics-by-examining-every-row"></a>通过检查每个行创建单列统计信息

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>通过指定样本大小创建单列统计信息

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState on census_external_table (STATENAME) WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>示例：更新统计信息

要更新统计信息，您需要删除并创建统计信息。 首先删除统计信息：

```sql
DROP STATISTICS census_external_table.sState
```

并创建统计信息：

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>后续步骤

有关进一步的查询性能改进，请参阅[SQL 池的最佳做法](best-practices-sql-pool.md#maintain-statistics)。
