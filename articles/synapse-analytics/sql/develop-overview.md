---
title: 用于开发突触 SQL 功能的资源
description: Synapse SQL 的开发概念、设计决策、建议和编码技术。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429013"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Azure 突触分析中突触 SQL 功能的设计决策和编码技术
在本文中，您将找到 Synapse SQL 的 SQL 池和 SQL 按需（预览）功能的资源列表。 推荐的文章分为两个部分：关键设计决策以及开发和编码技术。

这些文章的目标是帮助您为 Synapse 分析中的 Synapse SQL 组件开发最佳技术方法。

## <a name="key-design-decisions"></a>关键设计决策
以下文章重点介绍了 Synapse SQL 开发的概念和设计决策：

|                                                          |   SQL 池   | 按需 SQL |
| -----------------------------------------------------    | ---- | ---- |
| [连接](connect-overview.md)                    | 是 | 是 |
| [资源类和并发性](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 是    | 否 |
| [事务](develop-transactions.md)              | 是 | 否 |
| [用户定义的架构](develop-user-defined-schemas.md) | 是 | 是 |
| [表分发](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | 是 | 否 |
| [表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | 是 | 否 |
| [表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | 是 | 否 |
| [统计信息](develop-tables-statistics.md)            | 是 | 是 |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | 是 | 否 |
| [外部表](develop-tables-external-tables.md) | 是 | 是 |
| [CETAS](develop-tables-cetas.md)                     | 是 | 是 |


## <a name="recommendations"></a>建议

下面您将找到强调特定编码技术、提示和开发建议的基本文章：

|                                            | SQL 池 | 按需 SQL |
| ------------------------------------------ | ------------------ | ----------------------- |
| [存储过程](develop-stored-procedures.md)  | 是                | 否                      |
| [标签](develop-label.md)                           | 是                | 否                      |
| [视图](develop-views.md)                             | 是                | 是                     |
| [临时表](develop-tables-temporary.md)       | 是                | 是                     |
| [动态 SQL](develop-dynamic-sql.md)                 | 是                | 是                     |
| [循环](develop-loops.md)                         | 是                | 是                     |
| [Group By 选项](develop-group-by-options.md)       | 是                | 否                      |
| [变量赋值](develop-variable-assignment.md) | 是                | 是                     |

## <a name="next-steps"></a>后续步骤
有关详细信息参考，请参阅[SQL 池 T-SQL 语句](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

