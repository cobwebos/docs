---
title: 用于在 Azure Synapse Analytics 中开发 Synapse SQL 池的资源
description: 适用于 SQL 数据仓库的开发概念、设计决策、建议和编码技术。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 12f91633834ee98582eaad886b48eb8619378265
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411682"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中 Synapse SQL 池的设计决策和编码方法 
 在本文中，你将找到其他资源，可帮助你更好地了解 Azure Synapse 中 SQL 池的关键设计决策、建议和编码技术。

## <a name="key-design-decisions"></a>关键设计决策
以下文章重点介绍了使用 Azure Synapse 中的 SQL 池功能开发分布式数据仓库的概念和设计决策：

* [连接](../sql/connect-overview.md)
* [并发](resource-classes-for-workload-management.md)
* [事务](sql-data-warehouse-develop-transactions.md)
* [用户定义的架构](sql-data-warehouse-develop-user-defined-schemas.md)
* [表分布](sql-data-warehouse-tables-distribute.md)
* [表索引](sql-data-warehouse-tables-index.md)
* [表分区](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [统计信息](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>开发建议和编程技术
以下文章介绍了用于开发 SQL 池的特定编码方法、提示和建议：

* [存储过程](sql-data-warehouse-develop-stored-procedures.md)
* [标签](sql-data-warehouse-develop-label.md)
* [视图](sql-data-warehouse-develop-views.md)
* [临时表](sql-data-warehouse-tables-temporary.md)
* [动态 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [循环](sql-data-warehouse-develop-loops.md)
* [group by 选项](sql-data-warehouse-develop-group-by-options.md)
* [变量赋值](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>后续步骤
有关更多参考信息，请参阅 [T-SQL 语句](sql-data-warehouse-reference-tsql-statements.md)。
