---
title: "用于工作负荷管理的资源类 - Azure SQL 数据仓库 | Microsoft Docs"
description: "有关使用资源类管理并发性以及计算 Azure SQL 数据仓库中查询的资源的指导。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 122646f73b6e4e7c62eb0e6d4b6672b603d8acb2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="resource-classes-for-workload-management"></a>用于工作负荷管理的资源类
有关使用资源类来管理并发运行的并发查询数，以及计算 Azure SQL 数据仓库中查询的资源的指导。
 
## <a name="what-is-workload-management"></a>什么是工作负荷管理？
工作负荷管理是指优化所有查询的整体性能的功能。 适当优化的工作负荷能够有效地运行查询和负载操作，而不管这些查询和操作是计算密集型还是 IO 密集型。 

SQL 数据仓库为多用户环境提供工作负荷管理功能。 数据仓库不适用于多租户工作负荷。

## <a name="what-are-resource-classes"></a>什么是资源类？
资源类是预先确定的资源限制，用于控制查询的执行。 SQL 数据仓库根据资源类限制每个查询的计算资源。 

资源类可帮助管理数据仓库工作负荷的整体性能。借助资源类，可以通过针对并发运行的查询数以及分配给每个查询的计算资源设置限制，来有效地管理工作负荷。 

- 资源类越小，其使用的计算资源就越少，但实现的整体查询并发性就越高
- 资源类越大，其提供计算资源就越多，但会限制查询并发性

资源类是针对数据管理和操作活动设计的。 如果存在大型的联接和排序操作，以致系统必须在内存中执行查询而不是将查询溢出到磁盘，则某些极复杂的查询也会从资源类受益。

资源类控制以下操作：

* INSERT-SELECT、UPDATE、DELETE
* SELECT（查询用户表时）
* ALTER INDEX - REBUILD 或 REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* 数据加载
* 数据移动服务 (DMS) 执行的数据移动操作

> [!NOTE]  
> 针对动态管理视图 (DMV) 或其他系统视图执行的 SELECT 语句不受任何并发限制的约束。 用户可以对系统进行监视，而不用考虑在系统中执行的查询的数目。
> 
> 

## <a name="static-and-dynamic-resource-classes"></a>静态和动态资源类

有两种类型的资源类：动态和静态。

- 不管当前服务级别是什么，**静态资源类**都会分配相同的内存量（以[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)表示）。 这种静态分配意味着在使用更高的服务级别时，可在每个资源类中运行更多的查询。  静态资源类名为 staticrc10、staticrc20、staticrc30、staticrc40、staticrc50、staticrc60、staticrc70 和 staticrc80。 这些资源类最适合用于可提高资源类来获取更多计算资源的解决方案。

- **动态资源类**根据当前服务级别分配可变内存量。 扩展到更高的服务级别时，查询可自动获得更多的内存。 动态资源类命名为 smallrc、mediumrc、largerc 和 xlargerc。 这些资源类最适合用于可提高计算规模来获取更多资源的解决方案。 

[性能层](performance-tiers.md)使用相同的资源类名称，但具有不同的[内存和并发性规范](performance-tiers.md)。 


## <a name="assigning-resource-classes"></a>分配资源类

资源类是通过将用户分配到数据库角色来实现的。 当用户运行查询时，该查询将使用该用户的资源类来运行。 例如，如果某个用户是 smallrc 或 staticrc10 数据库角色的成员，则其查询将使用较小的内存量来运行。 如果某个数据库用户是 xlargerc 或 staticrc80 数据库角色的成员，则其查询将使用较大的内存量来运行。 

若要提高用户的资源类，可使用存储过程 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)。 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

若要降低资源类，可使用 [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)。  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

服务管理员的资源类是固定的，不可更改。  服务管理员是预配过程中创建的用户。

### <a name="default-resource-class"></a>默认资源类
默认情况下，每个用户都是小型资源类 (**smallrc**) 的成员。 

### <a name="resource-class-precedence"></a>资源类优先顺序
用户可以是多个资源类的成员。 如果用户属于多个资源类：

- 动态资源类优先于静态资源类。 例如，如果某个用户是 mediumrc（动态）和 staticrc80（静态）的成员，则查询将使用 mediumrc 来运行。
- 更大的资源类优先于更小的资源类。 例如，如果某个用户是 mediumrc 和 largerc 的成员，则查询将使用 largerc 来运行。 同样，如果某个用户是 staticrc20 和 statirc80 的成员，则查询将使用 staticrc80 资源分配来运行。

### <a name="queries-exempt-from-resource-classes"></a>资源类中的查询例外情况
某些查询始终在 smallrc 资源类中运行，即使用户是更大资源类的成员。 这些例外的查询不会计入并发性限制。 例如，如果并发性限制为 16，则许多用户都可以从系统视图中进行选择，而不影响可用的并发性槽位。

以下语句属于资源类的例外情况，始终在 smallrc 中运行：

* CREATE 或 DROP TABLE
* ALTER TABLE ...SWITCH、SPLIT 或 MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE、UPDATE 或 DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE、ALTER 或 DROP USER
* CREATE、ALTER 或 DROP PROCEDURE
* CREATE 或 DROP VIEW
* INSERT VALUES
* SELECT（从系统视图和 DMV）
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="recommendations"></a>建议
我们建议创建一个专门用于运行特定类型的查询或负载操作的用户。 然后，为该用户提供永久性的资源类，而不是频繁更改资源类。 此外，既然静态资源类对工作负荷提供的整体控制度更高，我们建议先使用静态资源类，然后再考虑动态资源类。

### <a name="resource-classes-for-load-users"></a>负载用户的资源类
`CREATE TABLE` 默认使用聚集列存储索引。 将数据压缩成列存储索引是一种内存密集型操作，内存压力可能会降低索引质量。 因此，在加载数据时，很可能需要较高的资源类。 为确保负载具有足够的内存，可以创建一个专门用于运行负载的用户，并将该用户分配到较高的资源类。

有效处理负载所需的内存量取决于所加载表的性质以及数据大小。 有关内存要求的详细信息，请参阅[最大程度地提高行组的质量](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

确定内存要求后，选择是要将负载用户分配到静态还是动态资源类。

- 当表的内存要求在特定的范围以内时，可使用静态资源类。 负载将使用适当的内存来运行。 扩展数据仓库时，负载不需要更多的内存。 使用静态资源类时，内存分配会保持恒定。 这种一致性可以节省内存，并允许更多的查询并发运行。 我们建议在新解决方案中先使用静态资源类，因为这些资源类提供更高的控制度。
- 当表的内存要求差别很大时，可使用动态资源类。 负载所需的内存量可能超过了当前 DWU 或者 cDWU 级别能够提供的内存量。 因此，扩展数据仓库可为负载操作添加更多的内存，从而使负载的执行速度加快。

### <a name="resource-classes-for-queries"></a>查询的资源类

有些查询是计算密集型的，有些则不是。  

- 当查询较为复杂但不需要高并发性时，可以选择动态资源类。  例如，生成每日或每周报告只是偶尔需要资源。 如果报告要处理大量的数据，则扩展数据仓库可将更多的内存提供给用户的现有资源类。
- 当一天中的资源预期有变化时，可选择静态资源类。 例如，如果有许多人查询数据仓库，则静态资源类就很合适。 缩放数据仓库时，分配给用户的内存量不会变化。 因此，可在系统中同时执行多个查询。

如何选择适当的内存授予取决于许多因素，例如，查询的数据量、表架构的性质，以及各种联接、选择和组合谓词。 一般而言，分配更多的内存可让查询更快完成，但同时会降低整体并发性。 如果并发性不是个问题，则过度分配内存不会给吞吐量带来坏处。 

若要优化性能，可使用不同的资源类。 下一部分提供了一个可以帮助推算最佳资源类的存储过程。

## <a name="example-code-for-finding-the-best-resource-class"></a>用于找出最佳资源类的示例代码
 
可以使用以下存储过程，根据给定的 SLO 推算每个资源类的并发性和内存授予，以及根据给定的资源类推算对非分区 CCI 表执行内存密集型 CCI 操作时可用的尽量最佳资源类：

下面是此存储过程的用途：  
1. 用于查看每个资源类的、根据给定 SLO 推算的并发性和内存授予。 如此示例中所示，用户需要为架构和表名提供 NULL。  
2. 用于查看根据给定资源类推算的、对非分区 CCI 表执行内存密集型 CCI 操作（加载、复制表、重建索引等）时可用的尽量最佳资源类。 该存储过程使用表架构来找出所需的内存授予。

### <a name="dependencies--restrictions"></a>依赖关系和限制：
- 此存储过程并不旨在计算分区 CCI 表的内存要求。    
- 此存储过程不会针对 CTAS/INSERT-SELECT 的 SELECT 部分考虑内存要求，而是假设它是一个 SELECT。
- 此存储过程使用其创建时所在的会话中提供的临时表。    
- 此存储过程依赖于当前的供应值（例如硬件配置、DMS 配置），如果其中的任何值发生更改，则此存储过程无法正常工作。  
- 此存储过程依赖于现有提供的并发限制，如果此限制发生更改，则此存储过程无法正常工作。  
- 此存储过程依赖于现有的资源类供应值，如果这些值发生更改，则此存储过程无法正常工作。  

>  [!NOTE]  
>  如果结合提供的参数执行存储过程后未获得输出，则可能存在两种情况。 <br />1.DW 参数包含无效的 SLO 值 <br />2.或者，针对表执行的 CCI 操作没有匹配的资源类。 <br />例如，在 DW100 级别，可用的最高内存授予是 400 MB。如果表架构很宽，则就会超过 400 MB 的要求。
      
### <a name="usage-example"></a>用法示例：
语法：  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: 提供 NULL 参数，以便从 DW DB 提取当前 DWU，或者以“DW100”格式提供任何受支持的 DWU
2. @SCHEMA_NAME: 提供表的架构名称
3. @TABLE_NAME: 提供相关的表名

有关执行此存储过程的示例：  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

以下语句创建前面示例中所用的 Table1。
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>存储过程定义

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>后续步骤
有关如何管理数据库用户和安全性的详细信息，请参阅[保护 SQL 数据仓库中的数据库][Secure a database in SQL Data Warehouse]。 有关较大资源类如何改进聚集列存储索引质量的详细信息，请参阅[列存储压缩的内存优化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
