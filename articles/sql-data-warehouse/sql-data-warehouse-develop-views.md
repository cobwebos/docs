---
title: 在 Azure SQL 数据仓库中使用 T-SQL 视图 | Microsoft Docs
description: 有关在开发解决方案时使用 Azure SQL 数据仓库中的 T-SQL 视图的技巧。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901713"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的视图
可以通过多种不同的方式使用视图来提升解决方案的质量。 

Azure SQL 数据仓库支持标准视图和具体化视图。 两者都是用 SELECT 表达式创建的虚拟表，并以逻辑表的形式呈现给查询。 视图封装常见数据计算的复杂性，并向计算更改添加抽象层，因此无需重新编写查询。

## <a name="standard-view"></a>标准视图
标准视图将在每次使用视图时计算其数据。 磁盘上没有存储任何数据。 用户通常将标准视图用作一种工具，可帮助组织数据库中的逻辑对象和查询。 若要使用标准视图，查询需要直接引用它。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) 文档。

SQL 数据仓库中的视图仅存储为元数据。 因此无法使用以下选项：
* 不提供任何架构绑定选项
* 无法通过视图更新基表
* 无法基于临时表创建视图
* 不支持 EXPAND/NOEXPAND 提示
* SQL 数据仓库中没有索引视图

可以利用标准视图来强制表之间的性能优化联接。 例如，视图可以合并冗余分布键作为联接条件的一部分，以便最大程度地减少数据移动。 视图的另一个好处是强制执行特定查询或联接提示。 以这种方式使用视图可确保始终以最佳方式执行联接，用户不需要记住其联接的正确构造。

## <a name="materialized-view"></a>具体化视图
像表一样，具体化视图预先计算、存储和维护其在 Azure SQL 数据仓库中的数据。 使用具体化视图时，每次都不需要重新计算。 在将数据加载到基表中时，Azure SQL 数据仓库会以同步方式刷新具体化视图。  即使查询中未引用视图，查询优化器也会自动使用已部署的具体化视图来提高查询性能。  查询从具体化视图中获益最大的是生成小结果集的大型表上的复杂查询（通常是带有联接和聚合的查询）。  

有关具体化视图语法和其他要求的详细信息，请参阅[CREATE 具体化视图为 SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)。  

有关查询优化指南，请参阅[利用具体化视图进行性能优化](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)。 

## <a name="example"></a>示例
一种常见的应用模式是在加载数据时，使用 CREATE TABLE AS SELECT (CTAS) 并后接对象重命名模式来重建表。  以下示例向日期维度添加新的日期记录。 请注意，这里先创建了一个新表 DimDate_New，然后将它重命名以替换表的原始版本。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```
但是，此方法可能导致表在用户的视图中出现然后消失，并返回“表不存在”错误消息。 使用视图可为用户提供一致的呈现层，同时将基础对象重命名。 通过借助视图来提供对数据的访问，用户不需要看到基础表。 此层可提供一致的用户体验，同时确保数据仓库设计人员可以改进数据模型。 能够改进基础表意味着设计人员可以使用 CTAS 来使数据加载过程中的性能最大化。   

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅 [SQL 数据仓库开发概述](sql-data-warehouse-overview-develop.md)。


