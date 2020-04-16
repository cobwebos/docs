---
title: 临时表
description: 在 Synapse SQL 池中使用临时表的基本指南，突出显示会话级别临时表的原则。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408031"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Synapse SQL 池中的临时表
本文包含使用临时表的基本指导，并重点介绍会话级别临时表的原则。 

使用本文中的信息可以帮助您模块化代码，提高可重用性和易于维护性。

## <a name="what-are-temporary-tables"></a>什么是临时表？
临时表在处理数据时很有用，尤其是在转换期间，中间结果是暂时的。 在 SQL 池中，临时表存在于会话级别。  

临时表仅对创建临时表的会话可见，并且当该会话注销时会自动删除。  

临时表可以提高性能，因为其结果将写入到本地而不是远程存储。

临时表在处理数据时很有用，尤其是在转换期间，中间结果是暂时的。 使用 SQL 分析，临时表存在于会话级别。  它们仅在创建它们的会话中可见。 因此，当会话注销时，它们会自动丢弃。 

## <a name="temporary-tables-in-sql-pool"></a>SQL 池中的临时表

在 SQL 池资源中，临时表提供性能优势，因为它们的结果写入本地存储而不是远程存储。

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

## <a name="dropping-temporary-tables"></a>删除临时表
创建新会话时，应不存在任何临时表。  

如果要调用同一存储过程（创建具有相同名称的临时过程，以确保`CREATE TABLE`语句成功，则使用 a`DROP`的简单存在前检查可以使用，如以下示例所示：

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

对于编码一致性，最好将此模式同时用于表和临时表。  在代码中完成临时表后，最好`DROP TABLE`使用它删除临时表。  

在存储过程开发中，通常可以看到放置命令在过程结束时捆绑在一起，以确保清理这些对象。

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>模块化代码
由于在用户会话的任意位置都可以看到临时表，因此可以利用此功能帮助您模块化应用程序代码。  

例如，以下存储过程生成 DDL 以按统计名称更新数据库中的所有统计信息：

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

在此阶段发生的唯一操作是创建存储过程，该存储过程使用 DDL 语句生成临时表 #stats_ddl。  

此存储过程将删除现有#stats_ddl以确保在会话中运行多次时不会失败。  

但是，由于存储过程的末尾没有 `DROP TABLE`，当存储过程完成后，它将保留创建的表，以便能够在存储过程之外进行读取。  

在 SQL 池中，与其他 SQL Server 数据库不同，可以在创建它的过程之外使用临时表。  SQL 池临时表可以在会话内的**任意位置**使用。 此功能可以带来更模块化和可管理的代码，如以下示例所示：

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

## <a name="temporary-table-limitations"></a>临时表的限制
在实现临时表时，SQL 池确实会施加一些限制。  目前，仅支持会话范围的临时表。  不支持全局临时表。  

此外，无法在临时表上创建视图。  只能使用哈希分布或轮循机制分布来创建临时表。  不支持复制的临时表分发。 

## <a name="next-steps"></a>后续步骤

要了解有关开发表的更多信息，请参阅使用[SQL Analytics 资源设计表](sql-data-warehouse-tables-overview.md)一文。

