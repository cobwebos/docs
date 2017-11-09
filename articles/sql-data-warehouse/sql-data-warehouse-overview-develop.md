---
title: "用于开发 Azure 中的数据仓库的资源 | Microsoft Docs"
description: "SQL 数据仓库的开发概念、设计决策、建议和编程技术。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: b85a4f09e561e429aa5bf46ec680014487fb40c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL 数据仓库的设计决策和编程技术
请阅读以下开发文章，以更好地了解 SQL 数据仓库的关键设计决策、建议和编程技术。

## <a name="key-design-decisions"></a>关键设计决策
以下文章重点介绍了在使用 SQL 数据仓库开发分布式数据仓库时，需要了解的一些关键概念和设计决策：

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
以下文章重点介绍了用于开发 SQL 数据仓库的具体编程技术、技巧和建议：

* [存储过程][stored procedures]
* [标签][labels]
* [视图][views]
* [临时表][temporary tables]
* [动态 SQL][dynamic SQL]
* [循环][looping]
* [“分组依据”选项][group by options]
* [变量赋值][variable assignment]

## <a name="next-steps"></a>后续步骤
阅读开发类文章后，请浏览 [Transact-SQL 参考页][Transact-SQL reference]，以了解有关 SQL 数据仓库支持的语法的更多详细信息。

<!--Image references-->

<!--Article references-->
[concurrency]: ./sql-data-warehouse-develop-concurrency.md
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
[Transact-SQL reference]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
