---
title: 用于在 Azure Synapse Analytics 中开发数据仓库的资源
description: SQL 数据仓库的开发概念、设计决策、建议和编程技术。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153309"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中数据仓库的设计决策和编码方法 
 在本文中，你将找到其他资源，可帮助你更好地了解 Azure Synapse 中的数据仓库的关键设计决策、建议和编码技术。

## <a name="key-design-decisions"></a>关键设计决策
以下文章重点介绍了使用 Azure Synapse 中的 SQL 分析功能开发分布式数据仓库的概念和设计决策：

* [连接](sql-data-warehouse-connect-overview.md)
* [并发](resource-classes-for-workload-management.md)
* [事务](sql-data-warehouse-develop-transactions.md)
* [用户定义的架构](sql-data-warehouse-develop-user-defined-schemas.md)
* [表分布](sql-data-warehouse-tables-distribute.md)
* [表索引](sql-data-warehouse-tables-index.md)
* [表分区](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [统计信息](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>开发建议和编程技术
以下文章提供了有关使用 SQL Analytics 开发数据仓库的特定编码方法、提示和建议：

* [存储过程](sql-data-warehouse-develop-stored-procedures.md)
* [标签](sql-data-warehouse-develop-label.md)
* [视图](sql-data-warehouse-develop-views.md)
* [临时表](sql-data-warehouse-tables-temporary.md)
* [动态 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [循环](sql-data-warehouse-develop-loops.md)
* [group by 选项](sql-data-warehouse-develop-group-by-options.md)
* [变量赋值](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>后续步骤
有关更多参考信息，请参阅[t-sql 语句](sql-data-warehouse-reference-tsql-statements.md)。
