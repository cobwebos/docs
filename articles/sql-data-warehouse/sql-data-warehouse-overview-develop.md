---
title: 用于开发 Azure 中的数据仓库的资源 | Microsoft Docs
description: SQL 数据仓库的开发概念、设计决策、建议和编程技术。
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: d9a272b2f43e080cd44b7179fe6f9dc55507142b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL 数据仓库的设计决策和编程技术
请阅读以下开发文章，以更好地了解 SQL 数据仓库的关键设计决策、建议和编程技术。

## <a name="key-design-decisions"></a>关键设计决策
以下文章重点介绍有关使用 SQL 数据仓库开发分布式数据仓库的概念和设计决策：

* [连接][connections]
* [并发][concurrency]
* [事务][transactions]
* [用户定义的架构][user-defined schemas]
* [表分发][table distribution]
* [表索引][table indexes]
* [表分区][table partitions]
* [CTAS][CTAS]
* [统计信息][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>开发建议和编程技术
以下文章重点介绍用于开发 SQL 数据仓库的具体编程技术、技巧和建议：

* [存储过程][stored procedures]
* [标签][labels]
* [视图][views]
* [临时表][temporary tables]
* [动态 SQL][dynamic SQL]
* [循环][looping]
* [“分组依据”选项][group by options]
* [变量赋值][variable assignment]

## <a name="next-steps"></a>后续步骤
有关更多参考信息，请参阅 [SQL 数据仓库 T-SQL 语句](sql-data-warehouse-reference-tsql-statements.md)。

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
