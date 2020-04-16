---
title: 使用突触 SQL 的 T-SQL 视图
description: 使用 T-SQL 视图和使用 Synapse SQL 开发解决方案的提示。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428649"
---
# <a name="t-sql-views-using-synapse-sql"></a>使用突触 SQL 的 T-SQL 视图
在本文中，您将找到使用 T-SQL 视图和使用 Synapse SQL 开发解决方案的提示。 

## <a name="why-use-views"></a>为何使用视图？

可以通过多种不同的方式使用视图来提升解决方案的质量。  本文重点介绍了一些如何使用视图丰富解决方案的示例，并包括需要考虑的限制。

### <a name="sql-pool---create-view"></a>SQL 池 - 创建视图

> [!NOTE]
> **SQL 池**：本文中未讨论 CREATE VIEW 的语法。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文档。

### <a name="sql-on-demand-preview---create-view"></a>SQL 按需（预览） - 创建视图

> [!NOTE]
> **SQL 按需**：本文中未讨论 CREATE VIEW 的语法。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文档。

## <a name="architectural-abstraction"></a>体系结构摘要

常见的应用程序模式是使用[CREATE TABLE 作为 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) （CTAS） 重新创建表，在加载数据时，后面是对象重命名模式。

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

请记住，此方法可能会导致表出现并从用户视图中消失，并提示"表不存在"错误消息。 视图可用于在重命名基础对象时为用户提供一致的表示层。

通过视图提供对数据的访问，用户不需要查看基础表。 除了一致的用户体验外，此层还可确保分析设计人员能够改进数据模型。 发展基础表的能力意味着设计人员可以使用 CTAS 在数据加载过程中最大限度地提高性能。

## <a name="performance-optimization"></a>性能优化

视图还可用于在表之间强制实施性能优化联接。 例如，视图可以合并冗余分布键作为联接条件的一部分，以便最大程度地减少数据移动。

强制特定查询或联接提示是使用 T-SQL 视图的另一个好处。 因此，视图功能可确保始终以最佳方式执行联接。 您将避免用户记住其联接的正确构造。

## <a name="limitations"></a>限制

Synapse SQL 中的视图仅存储为元数据。 因此，以下选项不可用：

* 没有架构绑定选项
* 无法通过视图更新基表
* 无法通过临时表创建视图
* 不支持 EXPAND/ NOEXPAND 提示
* Synapse SQL 中没有索引视图

## <a name="next-steps"></a>后续步骤

有关更多开发提示，请参阅[Synapse SQL 开发概述](develop-overview.md)。



