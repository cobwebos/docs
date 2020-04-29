---
title: 使用临时表和 Synapse SQL
description: 在 Synapse SQL 中使用临时表的基本指南。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428753"
---
# <a name="temporary-tables-in-synapse-sql"></a>Synapse SQL 中的临时表

本文包含有关使用临时表的基本指导，并重点介绍了 Synapse SQL 中的会话级别临时表的原则。 

SQL 池和 SQL 点播（预览版）资源都可以利用临时表。 SQL 点播包含本文末尾介绍的限制。 

## <a name="what-are-temporary-tables"></a>什么是临时表？

临时表在处理数据时非常有用，尤其是在转换期间，中间结果是暂时性的。 对于 Synapse SQL，临时表存在于会话级别。  它们仅对创建它们的会话可见。 因此，当该会话注销时，它们会自动删除。 

## <a name="temporary-tables-in-sql-pool"></a>SQL 池中的临时表

在 SQL 池资源中，临时表可以提高性能，因为其结果将写入到本地而不是远程存储。

### <a name="create-a-temporary-table"></a>创建临时表

只需为表名添加 `#` 前缀，即可创建临时表。  例如：

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

此外可以使用 `CTAS` 通过完全相同的方法来创建临时表：

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` 是一个强大的命令并具有附加优势，可有效利用事务日志空间。 
> 
> 

### <a name="dropping-temporary-tables"></a>删除临时表
创建新会话时，应不存在任何临时表。  但是，如果调用的是使用相同名称创建临时的同一存储过程，则为了确保`CREATE TABLE`语句成功，请使用简单的预存在检查，其中`DROP`包括： 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

为实现编码一致性，最好对表和临时表使用此模式。  在完成临时表后，最好使用`DROP TABLE`将其删除。  

在存储过程开发中，通常会看到在过程结束时捆绑在一起的 drop 命令，以确保清除这些对象。

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>模块化代码
可以在用户会话中的任何位置使用临时表。 然后，可以利用此功能来帮助模块化你的应用程序代码。  为了演示，以下存储过程将生成 DDL，以按统计名称更新数据库中的所有统计信息：

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

在此阶段，只会创建一个用于生成 #stats_ddl 临时表的存储过程。  如果存储过程已存在，则该存储过程将删除 #stats_ddl。 此删除可确保在会话内多次运行时，它不会失败。  

由于存储过程的`DROP TABLE`末尾没有，当存储过程完成时，创建的表将保持不变，并且可以在存储过程之外读取。  

与其他 SQL Server 数据库相比，Synapse SQL 允许您在创建该数据库的过程外部使用该临时表。  可以在会话中的**任何位置**使用通过 SQL 池创建的临时表。 因此，你将拥有更多模块化且可管理的代码，如以下示例中所示：

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>临时表的限制

SQL 池对临时表有一些实现限制：

- 仅支持会话范围的临时表。  不支持全局临时表。
- 不能在临时表上创建视图。
- 只能使用哈希分布或轮循机制分布来创建临时表。  不支持复制的临时表分布。 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>SQL 点播（预览版）中的临时表

支持 SQL 点播中的临时表，但它们的使用受到限制。 它们不能用于以文件为目标的查询。 

例如，无法使用存储中的文件的数据来联接临时表。 临时表的数目限制为100，并且它们的总大小限制为 100 MB。

## <a name="next-steps"></a>后续步骤

若要了解有关开发表的详细信息，请参阅[使用 SYNAPSE SQL 资源设计表](develop-tables-overview.md)一文。

