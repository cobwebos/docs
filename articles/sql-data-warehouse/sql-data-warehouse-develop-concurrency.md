---
title: "SQL 数据仓库中的并发和工作负荷管理 | Microsoft 文档"
description: "在开发解决方案之前，了解 SQL 数据仓库中的并发性和工作负荷管理。"
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
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.contentlocale: zh-cn
ms.lasthandoff: 08/25/2017

---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>SQL 数据仓库中的并发性和工作负荷管理
若要大规模提供可预测性能，可以通过 Microsoft Azure SQL 数据仓库控制并发级别和资源分配（例如内存和 CPU 优先级）。 本文介绍并发性和工作负荷管理的概念，说明如何实现这两种功能，以及如何在数据仓库中控制它们。 SQL 数据仓库工作负荷管理旨在协助提供多用户环境支持。 它不适用于多租户工作负荷。

## <a name="concurrency-limits"></a>并发限制
SQL 数据仓库允许多达 1,024 个并发连接。 所有 1,024 个连接都可以通过并发方式提交查询。 但是，为了优化吞吐量，SQL 数据仓库可能会让某些查询排队，确保每个查询都能收到系统授予的最小内存。 在执行查询时会让查询排队。 SQL 数据仓库可以在达到并发限制时让查询排队，以便确保活动查询能够访问迫切需要的内存资源，从而提高总吞吐量。  

影响并发限制的有两个概念：并发查询和并发槽。 执行查询时，必须符合查询并发限制和并发槽分配要求。

* 并发查询是指同时执行的查询。 就大型 DWU 来说，SQL 数据仓库支持最多 32 个并发查询。
* 根据 DWU 分配并发槽。 每 100 个 DWU 提供 4 个并发槽。 例如，DW100 分配 4 个并发槽，DW1000 分配 40 个。 每个查询使用一个或多个并发槽，具体取决于查询的[资源类](#resource-classes)。 在 smallrc 资源类中运行的查询占用一个并发槽。 在更高资源类中运行的查询占用更多并发槽。

下表描述了不同 DWU 大小的并发查询数和并发槽数的限制。

### <a name="concurrency-limits"></a>并发限制
| DWU | 并发查询数上限 | 分配的并发槽数 |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

满足其中一个阈值时，就会按“先进先出”原则排队执行新查询。  如果查询已经完成且查询和槽的数目降至限制以下，则会释放排队的查询。 

> [!NOTE]  
> 以独占方式在动态管理视图 (DMV) 或目录视图上执行的 *Select* 查询不受任何并发限制的约束。 用户可以对系统进行监视，而不用考虑在系统中执行的查询的数目。
> 
> 

## <a name="resource-classes"></a>资源类
资源类有助于控制针对查询的内存分配和 CPU 周期。 可以数据库角色的形式向用户分配两种资源类。 这两种资源类如下：
1. 动态资源类（**smallrc、mediumrc、largerc、xlargerc**）根据当前 DWU 分配可变内存量。 这意味着，如果扩展到更大的 DWU，则查询可自动获得更多的内存。 
2. 静态资源类（**staticrc10、staticrc20、staticrc30、staticrc40、staticrc50、staticrc60、staticrc70、staticrc80**）分配相同的内存量，而不管当前 DWU 是多少（前提是 DWU 本身提供足够的内存）。 这意味着，DWU 越大，在每个资源类中可并发运行的查询就越多。

**smallrc** 和 **staticrc10** 中的用户获得的内存量较小，但是可以利用更高的并发性。 与之相反，分配给 **xlargerc** 或 **staticrc80** 的用户获得的内存量大，因此可以并发运行的查询数较少。

默认情况下，每个用户都是小型资源类 (**smallrc**) 的成员。 过程 `sp_addrolemember` 用于提高资源类的级别，过程 `sp_droprolemember` 用于降低资源类的级别。 例如，以下命令会将 loaduser 的资源类提高到 **largerc** 级别：

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>不遵循资源类的查询

有几种类型的查询不会得益于更大的内存分配。 系统会忽略其资源类分配，而始终运行小资源类中的这些查询。 如果这些查询始终在小资源类中运行，则当并发槽数紧张时仍可以运行这些查询，它们不会占用比所需的更多的槽。 有关详细信息，请参阅[资源类例外](#query-exceptions-to-concurrency-limits)。

## <a name="details-on-resource-class-assignment"></a>有关资源类分配的详细信息


关于资源类的更多详细信息：

* 需要更改角色权限才能更改用户的资源类。
* 虽然可将用户添加到一个或多个更高级别的资源类，但动态资源类优先于静态资源类。 也就是说，如果将某个用户同时分配到 **mediumrc**（动态）和 **staticrc80**（静态），则 **mediumrc** 是遵循的资源类。
 * 如果将某个用户分配到特定资源类类型中的多个资源类（多个动态资源类或多个静态资源类），则遵循级别最高的资源类。 也就是说，如果将某个用户同时分配到 mediumrc 和 largerc，则遵循级别更高的资源类 (largerc)。 如果将某个用户同时分配到 **staticrc20** 和 **statirc80**，则遵循 **staticrc80**。
* 不能更改系统管理用户的资源类。

有关详细的示例，请参阅[更改用户资源类示例](#changing-user-resource-class-example)。

## <a name="memory-allocation"></a>内存分配
提高用户资源类的级别既有优点也有缺点。 提升用户的资源类可以让其查询访问更多的内存，也许可以提高查询执行速度。  但是，在提升资源类的同时，也会降低可以运行的并发查询数。 这就需要在分配大量内存给单个查询与允许其他查询并发运行（也需进行内存分配）之间进行权衡。 如果为某个用户提供了用于查询的高内存分配，则其他用户将无法访问同一内存来运行查询。

下表将所分配的内存与按 DWU 和资源类划分的每种分布情况进行了映射。

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>动态资源类的按分布情况进行的内存分配 (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

下表将所分配的内存与按 DWU 和静态资源类划分的每种分布情况进行了映射。 请注意，级别较高的资源类减少了内存，以遵守全局 DWU 限制。

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>静态资源类的按分布情况进行的内存分配 (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

从前面的表中可以看到，在 **xlargerc** 资源类的 DW2000 上运行的查询在 60 个分布式数据库的每个数据库中可以访问 6,400 MB 内存。  在 SQL 数据仓库中，有 60 种分布情况。 因此，在计算给定资源类的某个查询的总内存分配时，应将上述值乘以 60。

### <a name="memory-allocations-system-wide-gb"></a>系统范围的内存分配 (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

从包含系统范围内存分配情况的此表中可以看到，就 SQL 数据仓库的整体来说，在 xlargerc 资源类的 DW2000 上运行的查询总共分配了 375 GB 的内存（6,400 MB * 60 个分布区/1,024 即可转换为 GB）。

可对静态资源类运用相同的计算。
 
## <a name="concurrency-slot-consumption"></a>并发槽使用量  
SQL 数据仓库对在更高资源类级别中运行的查询赋予了更多内存。 内存是固定的资源。  因此，每个查询分配的内存越多，可以执行的并发查询数就越少。 下表在单个视图中重述了上述所有概念，显示了按 DWU 分配的并发槽数，以及每个资源类使用的槽数。  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>动态资源类并发槽的分配和使用  
| DWU | 并发查询数上限 | 分配的并发槽数 | smallrc 使用的槽数 | mediumrc 使用的槽数 | largerc 使用的槽数 | xlargerc 使用的槽数 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>静态资源类并发槽的分配和使用  
| DWU | 并发查询数上限 | 分配的并发槽数 |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

从这些表格中可以看到，以 DW1000 运行的 SQL 数据仓库可分配最多 32 个并发查询和总共 40 个并发槽。 如果所有用户都在 smallrc 中运行，则允许 32 个并发查询，因为每个查询只使用 1 个并发槽。 如果 DW1000 上的所有用户均在 mediumrc 中运行，则在每个分布中对每个查询分配 800 MB 内存，每个查询将总共分配 47 GB 内存，同时将用户的并发数限制为 5 个（40 个并发槽 / 每个 mediumrc 用户 8 个槽）。

## <a name="selecting-proper-resource-class"></a>选择合适的资源类  
较好的做法是将用户永久分配给资源类，而不是更改用户的资源类。 例如，加载到聚集列存储表时，如果分配了更多的内存，则可创建质量更高的索引。 为了确保加载项能够访问更多的内存，可创建一个用户来专门加载数据，并将该用户永久分配给更高级的资源类。
此处需要遵循几项最佳做法。 如上所述，SQL 数据仓库支持两种资源类类型：静态资源类和动态资源类。
### <a name="loading-best-practices"></a>有关加载的最佳做法
1.  如果预期负载中包含恒定的数据量，则静态资源类是个不错的选择。 以后进行扩展以获得更大的计算能力时，数据仓库可以现成地运行更多并发查询，因为负载用户不会消耗更多的内存。
2.  如果预期在某些场合下会出现较大的负载，则动态资源类是个不错的选择。 以后进行扩展以获得更大的计算能力时，负载用户可以现成地获得更多内存，因此可以加快负载的执行速度。

有效处理负载所需的内存量取决于所加载表的性质以及处理的数据量。 例如，将数据载入 CCI 表需要消耗一些内存来让 CCI 行组实现最优性。 有关更多详细信息，请参阅《列存储索引 - 数据加载指南》。

作为一种最佳做法，我们建议至少为负载使用 200MB 内存。

### <a name="querying-best-practices"></a>有关查询的最佳做法
查询根据其复杂性提出不同的要求。 根据查询需求增大每个查询的内存或增大并发性，都是增加整体吞吐量的有效方式。
1.  如果预期运行规则的复杂查询（例如，生成每日和每周报告）且不需要利用并发性，则动态资源类是个不错的选择。 如果系统需要处理更多的数据，则扩展数据仓库即可自动向运行查询的用户提供更多的内存。
2.  如果预期运行可变或日间并发模式（例如，通过可在各地访问的 Web UI 查询数据库），则静态资源类是个不错的选择。 以后在扩展数据仓库时，与静态资源类关联的用户可自动运行更多的并发查询。

根据查询需求选择适当的内存授予也很重要，因为这取决于许多因素，例如，查询的数据量、表架构的性质，以及各种联接、选择和组合谓词。 从常规角度来看，分配更多的内存可让查询更快完成，但同时会降低整体并发性。 如果并发性不是个问题，则过度分配内存不会带来坏处。 若要优化吞吐量，可能需要尝试不同种类的资源类。

可以使用以下存储过程，根据给定的 SLO 推算每个资源类的并发性和内存授予，以及根据给定的资源类推算对非分区 CCI 表执行内存密集型 CCI 操作时可用的尽量最佳资源类：

#### <a name="description"></a>说明:  
下面是此存储过程的用途：  
1. 帮助用户根据给定的 SLO 推算每个资源类的并发性和内存授予。 如以下示例中所示，用户需要为架构和表名提供 NULL。  
2. 帮助用户根据给定的资源类推算对非分区 CCI 表执行内存密集型 CCI 操作（加载、复制表、重建索引等）时可用的尽量最佳资源类。 该存储过程使用表架构来找出所需的内存授予。

#### <a name="dependencies--restrictions"></a>依赖关系和限制：
- 此存储过程并不旨在计算分区 CCI 表的内存要求。    
- 此存储过程不会针对 CTAS/INSERT-SELECT 的 SELECT 部分考虑内存要求，而是假设它是一个简单的 SELECT。
- 此存储过程使用临时表，因此，可在创建此存储过程的会话中使用。    
- 此存储过程依赖于当前的供应值（例如硬件配置、DMS 配置），如果其中的任何值发生更改，则此存储过程无法正常工作。  
- 此存储过程依赖于现有提供的并发限制，如果此限制发生更改，则此存储过程无法正常工作。  
- 此存储过程依赖于现有的资源类供应值，如果这些值发生更改，则此存储过程无法正常工作。  

>  [!NOTE]  
>  如果结合提供的参数执行存储过程后未获得输出，则可能存在两种情况。 <br />1.DW 参数包含无效的 SLO 值 <br />2.或者，在提供了表名的情况下，CCI 操作没有匹配的资源类。 <br />例如，在 DW100 级别，可用的最高内存授予是 400MB。如果表架构很宽，则就会超过 400MB 的要求。
      
#### <a name="usage-example"></a>用法示例：
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

可按如下所示创建上述示例中使用的 Table1：  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>下面是存储过程定义：
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
(   @DWU VARCHAR(7),
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

-- Creating ref. temptable (CTAS) to hold mapping info.
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

## <a name="query-importance"></a>查询重要性
SQL 数据仓库通过使用工作负荷组来实现资源类。 总共有八个工作负荷组用于控制不同 DWU 大小的资源类的行为。 对于任何 DWU，SQL 数据仓库只使用八个工作负荷组中的四个。 这样做是合理的，因为每个工作负荷组会被分配到以下四个资源类中的一个：smallrc、mediumrc、largerc 或 xlargerc。 了解工作负荷组的重要性在于其中一些工作负荷组已设置为较高的重要性。 重要性用于 CPU 计划。 重要性为高的查询在运行时所获得的 CPU 周期数将是重要性为中的查询的 3 倍。 因此，并发槽映射也决定了 CPU 优先级。 如果某个查询使用了至少 16 个槽，则该查询是作为重要性高的查询运行的。

下表显示了每个工作负荷组的重要性映射。

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>并发槽和重要性的工作负荷组映射
| 工作负荷组 | 并发槽映射 | MB / 分布区 | 重要性映射 |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |中型 |
| SloDWGroupC01 |2 |200 |中型 |
| SloDWGroupC02 |4 |400 |中型 |
| SloDWGroupC03 |8 |800 |中型 |
| SloDWGroupC04 |16 |1,600 |高 |
| SloDWGroupC05 |32 |3,200 |高 |
| SloDWGroupC06 |64 |6,400 |高 |
| SloDWGroupC07 |128 |12,800 |高 |

从**分配和使用并发槽**图中可以看到，对于 smallrc、mediumrc、largerc 和 xlargerc，DW500 分别使用 1、4、8 和 16 个并发槽。 可以在上面的图中查找这些值，以找到每个资源类的重要性。

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 的资源类到重要性的映射
| 资源类 | 工作负荷组 | 使用的并发槽数 | MB / 分布区 | 重要性 |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |中型 |
| mediumrc |SloDWGroupC02 |4 |400 |中型 |
| largerc |SloDWGroupC03 |8 |800 |中型 |
| xlargerc |SloDWGroupC04 |16 |1,600 |高 |
| staticrc10 |SloDWGroupC00 |1 |100 |中型 |
| staticrc20 |SloDWGroupC01 |2 |200 |中型 |
| staticrc30 |SloDWGroupC02 |4 |400 |中型 |
| staticrc40 |SloDWGroupC03 |8 |800 |中型 |
| staticrc50 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc60 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc70 |SloDWGroupC03 |16 |1,600 |高 |
| staticrc80 |SloDWGroupC03 |16 |1,600 |高 |

在进行故障诊断时，可以使用以下 DMV 查询，从资源调控器的角度来详细查看内存资源分配的差异，或者分析工作负荷组目前的和历史上的使用情况：

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>遵循并发限制的查询
大多数查询都受资源类的约束。 这些查询必须同时不超出并发查询和并发槽的阈值。 用户无法选择从并发槽模型中排除查询。

重申一下，以下语句接受资源类：

* INSERT-SELECT
* UPDATE
* 删除
* SELECT（查询用户表时）
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* 数据加载
* 数据移动服务 (DMS) 执行的数据移动操作

## <a name="query-exceptions-to-concurrency-limits"></a>并发限制的查询例外
有些查询不能接受将用户分配到的资源类。 当特定命令所需的内存资源较低时（通常是因为该命令是元数据操作），会产生并发限制的例外情况。 这些例外的产生是为了避免为始终不需要较大内存的查询分配较大内存。 在这些情况下，无论分配给用户的实际资源类是什么，都将始终使用默认的小型资源类 (smallrc)。 例如，`CREATE LOGIN` 将始终在 smallrc 中运行。 执行此操作所需的资源非常少，因此没有必要将查询纳入并发槽模型中。  另外，这些查询也不受 32 用户并发限制的限制，在不受数量限制时，这些查询最多可以运行 1,024 个会话，即达到会话限制。

以下语句不接受资源类：

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

##  <a name="changing-user-resource-class-example"></a> 更改用户资源类示例
1. **创建登录名：**在托管 SQL 数据仓库数据库的 SQL Server 上与 **master** 数据库建立连接，并执行以下命令。
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > 最好是在针对 Azure SQL 数据仓库用户的 master 数据库中创建一个用户。 在 master 中创建用户以后，用户即可使用 SSMS 之类的工具登录，不需指定数据库名称。  此外，用户还可以使用对象资源管理器查看 SQL Server 上的所有数据库。  有关创建和管理用户的详细信息，请参阅[保护 SQL 数据仓库中的数据库][Secure a database in SQL Data Warehouse]。
   > 
   > 
2. **创建 SQL 数据仓库用户：**与 **SQL 数据仓库数据库**建立连接，并执行以下命令。
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **授予权限：**以下示例在 **SQL 数据仓库** 数据库上授予 `CONTROL`。 数据库级别的 `CONTROL` 相当于 SQL Server 中的 db_owner。
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **提高资源类的级别：**要将用户添加到更高级别的工作负荷管理角色，请使用以下查询。
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **降低资源类的级别：**要将用户从工作负荷管理角色中删除，请使用以下查询。
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > 无法从 smallrc 中删除用户。
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>对排队的查询进行的检测，以及其他 DMV
可以使用 `sys.dm_pdw_exec_requests` DMV 来确定在并发队列中等待的查询。 正在等待并发槽的查询的状态为**挂起**。

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

可以使用 `sys.database_principals` 来查看工作负荷管理角色。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

以下查询显示分配给每个用户的角色。

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL 数据仓库具有以下等待类型：

* **LocalQueriesConcurrencyResourceType**：位于并发槽框架外部的查询。 DMV 查询和 `SELECT @@VERSION` 等系统函数是本地查询的示例。
* **UserConcurrencyResourceType**：位于并发槽框架内部的查询。 针对最终用户表的查询代表使用此资源类型的示例。
* **DmsConcurrencyResourceType**：数据移动操作生成的等待。
* **BackupConcurrencyResourceType**：此等待表明正在备份数据库。 此资源类型的最大值为 1。 如果同时请求了多个备份，其他备份会排队。

可以使用 `sys.dm_pdw_waits` DMV 来查看请求所等待的具体资源。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV 仅显示给定查询所占用的资源等待。 资源等待时间只度量等待提供资源的时间，与信号等待时间相反，后者是基础 SQL Server 将查询调度到 CPU 所需的时间。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

可以使用 `sys.dm_pdw_wait_stats` DMV 对等待进行历史趋势分析。

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>后续步骤
有关如何管理数据库用户和安全性的详细信息，请参阅[保护 SQL 数据仓库中的数据库][Secure a database in SQL Data Warehouse]。 有关如何通过更大型资源类来改进聚集列存储索引质量的详细信息，请参阅[重新生成索引以提升段质量]。

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[重新生成索引以提升段质量]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

