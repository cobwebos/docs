---
title: 使用 T-SQL 视图
description: 在 Synapse SQL 池中使用 T-sql 视图和开发解决方案的技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633391"
---
# <a name="views-in-synapse-sql-pool"></a>Synapse SQL 池中的视图

可以通过多种不同的方式使用视图来提升解决方案的质量。

SQL 池支持标准视图和具体化视图。 两者都是使用 SELECT 表达式创建的虚拟表，作为逻辑表提供给查询。

视图封装了常见数据计算的复杂性，并为计算更改添加了抽象层，因此无需重写查询。

## <a name="standard-view"></a>标准视图

标准视图在每次使用视图时都会计算其数据。 磁盘上没有存储数据。 用户通常使用标准视图作为帮助组织数据库中的逻辑对象和查询的工具。

若要使用标准视图，查询需要直接引用它。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 文档。

SQL 池中的视图仅存储为元数据。 因此，下列选项不可用：

* 不提供任何架构绑定选项
* 不能通过视图更新基表
* 无法在临时表上创建视图
* 不支持 EXPAND/NOEXPAND 提示
* SQL 池中没有索引视图

可以利用标准视图在表之间实施性能优化联接。 例如，视图可以合并冗余分布键作为联接条件的一部分，以便最大程度地减少数据移动。

视图的另一个好处是强制执行特定查询或联接提示。 以这种方式使用视图可确保始终以最佳方式执行联接，用户不需要记住其联接的正确构造。

## <a name="materialized-view"></a>具体化视图

像表一样，具体化视图预先计算、存储和维护 SQL 池中的数据。 每次使用具体化视图时都不需要重新计算。

在将数据加载到基表中时，SQL 池会同步刷新具体化视图。  即使查询中未引用视图，查询优化器也会自动使用已部署的具体化视图来提高查询性能。  

从具体化视图中获益最大的查询是对产生小结果集的大型表的复杂查询（通常是使用联接和聚合的查询）。  

有关具体化视图语法和其他要求的详细信息，请参阅 [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。  

有关查询优化指南，请查看[通过具体化视图进行性能优化](performance-tuning-materialized-views.md)。

## <a name="example"></a>示例

常见的应用程序模式是在加载数据时，使用 CREATE TABLE AS SELECT （CTAS）后跟对象重命名模式来重新创建表。  

以下示例向日期维度添加新的日期记录。 请注意，这里先创建了一个新表 DimDate_New，然后将它重命名以替换表的原始版本。

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

但是，这种方法可能导致表在用户的视图中出现和消失，同时发出 "表不存在" 错误消息。

视图可用于向用户提供一致的呈现层，同时将基础对象重命名。 通过视图提供对数据的访问，用户不需要对基础表的可见性。

此层可提供一致的用户体验，同时确保数据仓库设计人员可以改进数据模型。 能够改进基础表意味着设计人员可以使用 CTAS 来使数据加载过程中的性能最大化。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[SQL 池开发概述](sql-data-warehouse-overview-develop.md)。
