---
title: 在 Azure SQL 数据仓库中使用 T-SQL 视图 | Microsoft Docs
description: 有关在开发解决方案时使用 Azure SQL 数据仓库中的 T-SQL 视图的技巧。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 28280a067e7008c20361e0a0041c81ba84e7f74c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525989"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的视图
有关在开发解决方案时使用 Azure SQL 数据仓库中的 T-SQL 视图的技巧。 

## <a name="why-use-views"></a>为何使用视图？
可以通过多种不同的方式使用视图来提升解决方案的质量。  本文重点介绍几个示例，说明如何使用视图来丰富解决方案，以及需要注意的限制。

> [!NOTE]
> 本文未讨论 CREATE VIEW 的语法。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) 文档。
> 
> 

## <a name="architectural-abstraction"></a>体系结构摘要
一种常见的应用模式是在加载数据时，使用 CREATE TABLE AS SELECT (CTAS) 并后接对象重命名模式来重建表。

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

但是，此方法可能导致表在用户的视图中出现然后消失，并返回“表不存在”错误消息。 使用视图可为用户提供一致的呈现层，同时将基础对象重命名。 通过借助视图来提供对数据的访问，用户不需要看到基础表。 此层可提供一致的用户体验，同时确保数据仓库设计人员可以改进数据模型。 能够改进基础表意味着设计人员可以使用 CTAS 来使数据加载过程中的性能最大化。   

## <a name="performance-optimization"></a>性能优化
视图还可用于强化表之间的性能优化联接。 例如，视图可以合并冗余分布键作为联接条件的一部分，以便最大程度地减少数据移动。 视图的另一个好处是强制执行特定查询或联接提示。 以这种方式使用视图可确保始终以最佳方式执行联接，用户不需要记住其联接的正确构造。

## <a name="limitations"></a>限制
SQL 数据仓库中的视图仅存储为元数据。 因此无法使用以下选项：

* 不提供任何架构绑定选项
* 无法通过视图更新基表
* 无法基于临时表创建视图
* 不支持 EXPAND/NOEXPAND 提示
* SQL 数据仓库中没有索引视图

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅 [SQL 数据仓库开发概述](sql-data-warehouse-overview-develop.md)。


