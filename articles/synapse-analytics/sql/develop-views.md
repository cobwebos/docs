---
title: 使用 Synapse SQL 的 t-sql 视图
description: 使用 T-sql 视图和使用 Synapse SQL 开发解决方案的技巧。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81428649"
---
# <a name="t-sql-views-using-synapse-sql"></a>使用 Synapse SQL 的 t-sql 视图
在本文中，你将了解有关使用 T-sql 视图的技巧，以及如何使用 Synapse SQL 开发解决方案。 

## <a name="why-use-views"></a>为何使用视图？

可以通过多种不同的方式使用视图来提升解决方案的质量。  本文重点介绍几个示例，说明如何使用视图来丰富解决方案，并包括需要考虑的限制。

### <a name="sql-pool---create-view"></a>SQL 池-创建视图

> [!NOTE]
> **SQL pool**：本文不讨论 CREATE VIEW 的语法。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文档。

### <a name="sql-on-demand-preview---create-view"></a>SQL 点播（预览版）-创建视图

> [!NOTE]
> **SQL 点播**：创建视图的语法不在本文中讨论。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文档。

## <a name="architectural-abstraction"></a>体系结构摘要

常见的应用程序模式是使用[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) （CTAS）重新创建表，在加载数据时，将使用后跟对象重命名模式。

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

请记住，此方法可能导致表在用户的视图中出现和消失，并提示 "表不存在" 错误消息。 使用视图可为用户提供一致的呈现层，同时对基础对象重命名。

用户可以借助视图来访问数据，这样就不需要看到基础表。 除了具有一致的用户体验外，此层还确保分析设计人员可以改进数据模型。 改进基础表的能力意味着，设计人员可以使用 CTAS 在数据加载过程中最大程度地提高性能。

## <a name="performance-optimization"></a>性能优化

视图还可用于强制表之间的性能优化联接。 例如，视图可以合并冗余分布键作为联接条件的一部分，以便最大程度地减少数据移动。

使用 T-sql 视图的另一个好处是强制执行特定查询或联接提示。 因此，views 功能可确保始终以最佳方式执行联接。 你将不需要用户记住正确的联接构造。

## <a name="limitations"></a>限制

Synapse SQL 中的视图仅存储为元数据。 因此，无法使用以下选项：

* 没有架构绑定选项
* 无法通过视图更新基表
* 无法基于临时表创建视图
* 不支持 EXPAND/NOEXPAND 提示
* Synapse SQL 中没有索引视图

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [Synapse SQL 开发概述](develop-overview.md)。



