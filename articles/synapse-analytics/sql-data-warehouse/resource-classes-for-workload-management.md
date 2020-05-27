---
title: 用于工作负荷管理的资源类
description: 有关使用资源类管理并发性以及计算 Azure Synapse Analytics 中查询的资源的指导。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 6214c7f0d7728d39e36a7b555f503e130b405e81
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653068"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的资源类管理工作负载

有关在 Azure Synapse 中使用资源类管理 Synapse SQL 池查询的内存和并发性的指导。  

## <a name="what-are-resource-classes"></a>什么是资源类

查询的性能容量由用户的资源类决定。  资源类是 Synapse SQL 池中预先确定的资源限制，用于控制查询执行的计算资源和并发性。 资源类可以通过对并发运行的查询数和分配给每个查询的计算资源数设置限制，帮助你配置查询资源。  我们需要在内存和并发性之间进行权衡。

- 较小的资源类可以减少每个查询的最大内存量，但同时会提高并发性。
- 较大的资源类可以增加每个查询的最大内存量，但同时会降低并发性。

有两种类型的资源类：

- 静态资源类：非常适用于在数据集大小固定的情况下提高并发性。
- 动态资源类：非常适用于大小和性能随着服务级别的扩展而增加和提升的数据集。

资源类使用并发性槽位来测量资源消耗。  本文稍后将介绍[并发性槽位](#concurrency-slots)。

- 若要查看资源类的资源利用率，请参阅[内存和并发性限制](memory-concurrency-limits.md)。
- 若要调整资源类，可以使用不同的用户身份运行查询，或[更改当前用户的资源类](#change-a-users-resource-class)成员身份。

### <a name="static-resource-classes"></a>静态资源类

不管当前性能级别是什么，静态资源类都会分配相同的内存量（以[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)表示）。 由于不管性能级别是什么，查询都会获得相同的内存分配，因此，[横向扩展数据仓库](quickstart-scale-compute-portal.md)可以在资源类中运行更多的查询。  如果数据量已知且保持不变，则最理想的选择是静态资源类。

静态资源类是使用以下预定义的数据库角色实现的：

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>动态资源类

动态资源类根据当前服务级别分配可变内存量。 静态资源类适用于较高的并发性和静态数据量，而动态资源类更适合数据量会增长或有所变化的情况。  提升到更高的服务级别时，查询可自动获得更多的内存。  

动态资源类是使用以下预定义的数据库角色实现的：

- smallrc
- mediumrc
- largerc
- xlargerc

每个资源类的内存分配如下所示。

| 服务级别  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6.25%             | 10%                    | 22%                    | 70%                    |
| DW500c         | 5%                | 10%                    | 22%                    | 70%                    |
| DW1000c 到<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |

### <a name="default-resource-class"></a>默认资源类

默认情况下，每个用户都是动态资源类 (**smallrc**) 的成员。

服务管理员的资源类在 smallrc 中是固定的，不可更改。  服务管理员是预配过程中创建的用户。  使用新服务器新建 Synapse SQL 池时，此上下文中的服务管理员是为“服务器管理员登录名”指定的登录名。

> [!NOTE]
> 定义为 Active Directory 管理员的用户或组也是服务管理员。
>
>

## <a name="resource-class-operations"></a>资源类操作

资源类旨在改进数据管理和操作活动设计的性能。 复杂的查询在大型资源类下运行也能受益。 例如，如果资源类足够大，使查询能够在内存中执行，则针对大型联接和排序的查询的性能可以得到改善。

### <a name="operations-governed-by-resource-classes"></a>资源类控制的操作

以下操作由资源类控制：

- INSERT-SELECT、UPDATE、DELETE
- SELECT（查询用户表时）
- ALTER INDEX - REBUILD 或 REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- 数据加载
- 数据移动服务 (DMS) 执行的数据移动操作

> [!NOTE]  
> 针对动态管理视图 (DMV) 或其他系统视图执行的 SELECT 语句不受任何并发限制的约束。 用户可以对系统进行监视，而不用考虑在系统中执行的查询的数目。
>
>

### <a name="operations-not-governed-by-resource-classes"></a>资源类不会控制的操作

某些查询始终在 smallrc 资源类中运行，即使用户是更大资源类的成员。 这些例外的查询不会计入并发性限制。 例如，如果并发性限制为 16，则许多用户都可以从系统视图中进行选择，而不影响可用的并发性槽位。

以下语句属于资源类的例外情况，始终在 smallrc 中运行：

- CREATE 或 DROP TABLE
- ALTER TABLE ...SWITCH、SPLIT 或 MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE、UPDATE 或 DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE、ALTER 或 DROP USER
- CREATE、ALTER 或 DROP PROCEDURE
- CREATE 或 DROP VIEW
- INSERT VALUES
- SELECT（从系统视图和 DMV）
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>并发槽位

使用并发槽位可以方便地跟踪可用于执行查询的资源。 这些槽位就像是演唱会的门票，因为席位有限，必须预订。 每个数据仓库的并发性槽位总数由服务级别决定。 在查询可以开始执行之前，必须预留足够的并发槽位。 查询完成后，会释放其并发槽位。  

- 使用 10 个并发槽位运行的查询可以访问的计算资源，是使用 2 个并发槽位运行的查询的 5 倍。
- 如果每个查询需要 10 个并发槽位并且有 40 个并发槽位，则只有 4 个查询可以并发运行。

只有受资源控制的查询消耗并发槽位。 系统查询和一些不重要的查询不消耗任何槽位。 消耗的确切并发槽位数由查询的资源类决定。

## <a name="view-the-resource-classes"></a>查看资源类

资源类实现为预定义的数据库角色。 有两种类型的资源类：动态和静态。 若要查看资源类列表，请使用以下查询：

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>更改用户的资源类

资源类是通过将用户分配到数据库角色来实现的。 当用户运行查询时，该查询将使用该用户的资源类来运行。 例如，如果某个用户是 staticrc10 数据库角色的成员，则其查询将使用较小的内存量来运行。 如果某个数据库用户是 xlargerc 或 staticrc80 数据库角色的成员，则其查询将使用较大的内存量来运行。

若要提高用户的资源类，请使用 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 将用户添加到大型资源类的数据库角色。  以下代码将用户添加到 largerc 数据库角色。  每个请求获取 22% 的系统内存。

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

若要降低资源类，可使用 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。  如果“loaduser”不是成员或任何其他资源类，则会转到具有 3% 内存授予的默认 smallrc 资源类。  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>资源类优先顺序

用户可以是多个资源类的成员。 如果用户属于多个资源类：

- 动态资源类优先于静态资源类。 例如，如果某个用户是 mediumrc（动态）和 staticrc80（静态）的成员，则查询将使用 mediumrc 来运行。
- 更大的资源类优先于更小的资源类。 例如，如果某个用户是 mediumrc 和 largerc 的成员，则查询将使用 largerc 来运行。 同样，如果某个用户是 staticrc20 和 statirc80 的成员，则查询将使用 staticrc80 资源分配来运行。

## <a name="recommendations"></a>建议

>[!NOTE]
>请考虑利用工作负荷管理功能（[工作负荷隔离](sql-data-warehouse-workload-isolation.md)、[分类](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)），以更好地控制工作负荷和可预测的性能。  
>
>

我们建议创建一个专门用于运行特定类型的查询或负载操作的用户。 为该用户提供永久性的资源类，而不是频繁更改资源类。 静态资源类对工作负荷提供的整体控制度更高，因此，我们建议先使用静态资源类，然后再考虑动态资源类。

### <a name="resource-classes-for-load-users"></a>负载用户的资源类

`CREATE TABLE` 默认使用聚集列存储索引。 将数据压缩成列存储索引是一种内存密集型操作，内存压力可能会降低索引质量。 加载数据时，内存压力可能导致需要更高的资源类。 为确保负载具有足够的内存，可以创建一个专门用于运行负载的用户，并将该用户分配到较高的资源类。

有效处理负载所需的内存量取决于所加载表的性质以及数据大小。 有关内存要求的详细信息，请参阅[最大程度地提高行组的质量](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

确定内存要求后，选择是要将负载用户分配到静态还是动态资源类。

- 当表的内存要求在特定的范围以内时，可使用静态资源类。 负载将使用适当的内存来运行。 扩展数据仓库时，负载不需要更多的内存。 使用静态资源类时，内存分配会保持恒定。 这种一致性可以节省内存，并允许更多的查询并发运行。 我们建议在新解决方案中先使用静态资源类，因为这些资源类提供更高的控制度。
- 当表的内存要求差别很大时，可使用动态资源类。 负载所需的内存量可能超过了当前 DWU 或者 cDWU 级别能够提供的内存量。 扩展数据仓库可为负载操作添加更多的内存，从而使负载的执行速度加快。

### <a name="resource-classes-for-queries"></a>查询的资源类

有些查询是计算密集型的，有些则不是。  

- 当查询较为复杂但不需要高并发性时，可以选择动态资源类。  例如，生成每日或每周报告只是偶尔需要资源。 如果报告要处理大量的数据，则扩展数据仓库可将更多的内存提供给用户的现有资源类。
- 当一天中的资源预期有变化时，可选择静态资源类。 例如，如果有许多人查询数据仓库，则静态资源类就很合适。 缩放数据仓库时，分配给用户的内存量不会变化。 因此，可在系统中同时执行多个查询。

适当的内存授予取决于许多因素，例如，查询的数据量、表架构的性质，以及各种联接、选择和组合谓词。 一般而言，分配更多的内存可让查询更快完成，但同时会降低整体并发性。 如果并发性不是个问题，则过度分配内存不会给吞吐量带来坏处。

若要优化性能，可使用不同的资源类。 下一部分提供了一个可以帮助推算最佳资源类的存储过程。

## <a name="example-code-for-finding-the-best-resource-class"></a>用于找出最佳资源类的示例代码

可以使用以下指定的存储过程，根据给定的 SLO 推算每个资源类的并发性和内存授予，以及根据给定的资源类推算对非分区 CCI 表执行内存密集型 CCI 操作时可用的最佳资源类：

下面是此存储过程的用途：

1. 用于查看每个资源类的、根据给定 SLO 推算的并发性和内存授予。 如此示例中所示，用户需要为架构和表名提供 NULL。  
2. 用于查看根据给定的资源类推算对非分区 CCI 表执行内存密集型 CCI 操作（加载、复制表、重建索引等）时可用的最佳资源类。 该存储过程使用表架构来找出所需的内存授予。

### <a name="dependencies--restrictions"></a>依赖关系和限制

- 此存储过程并不旨在计算分区 CCI 表的内存要求。
- 此存储过程不会针对 CTAS/INSERT-SELECT 的 SELECT 部分考虑内存要求，而是假设它是一个 SELECT。
- 此存储过程使用其创建时所在的会话中提供的临时表。
- 此存储过程依赖于当前的供应值（例如硬件配置、DMS 配置），如果其中的任何值发生更改，则此存储过程将无法正常工作。  
- 此存储过程依赖于现有的并发限制选项，如果这些选项发生更改，则此存储过程将无法正常工作。  
- 此存储过程依赖于现有的资源类选项，如果这些选项发生更改，则此存储过程将无法正常工作。  

>[!NOTE]  
>如果结合提供的参数执行存储过程后未获得输出，则可能存在两种情况。
>
>1. DW 参数包含无效的 SLO 值
>2. 或者，针对表执行的 CCI 操作没有匹配的资源类。
>
>例如，在 DW100c 级别，可用的最高内存授予是 1 GB。如果表架构很宽，就会超过 1 GB 的要求。

### <a name="usage-example"></a>用例

语法：  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: 提供 NULL 参数以从 DW DB 中提取当前 DWU，或者以“DW100c”的形式提供任何受支持的 DWU
2. @SCHEMA_NAME: 提供表的架构名称
3. @TABLE_NAME: 提供相关的表名

有关执行此存储过程的示例：

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
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

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
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
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
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
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>后续步骤

有关如何管理数据库用户和安全性的详细信息，请参阅[在 Synapse SQL 中保护数据库](sql-data-warehouse-overview-manage-security.md)。 有关较大资源类如何改进聚集列存储索引质量的详细信息，请参阅[列存储压缩的内存优化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。
