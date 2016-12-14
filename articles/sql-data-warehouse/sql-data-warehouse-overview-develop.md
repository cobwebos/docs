---
title: "SQL 数据仓库开发的设计决策和编码技术 | Microsoft 文档"
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
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d1c2255264a4240eecc51199050f8406d141ace2


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL 数据仓库的设计决策和编程技术
请阅读以下开发文章，以更好地了解 SQL 数据仓库的关键设计决策、建议和编程技术。

## <a name="key-design-decisions"></a>关键设计决策
以下文章重点介绍了在使用 SQL 数据仓库开发分布式数据仓库时，需要了解的一些关键概念和设计决策：

* [连接][连接]
* [并发][并发]
* [事务][事务]
* [用户定义的架构][用户定义的架构]
* [表分布][表分布]
* [表索引][表索引]
* [表分区][表分区]
* [CTAS][CTAS]
* [统计信息][统计信息]

## <a name="development-recommendations-and-coding-techniques"></a>开发建议和编程技术
以下文章重点介绍了用于开发 SQL 数据仓库的具体编程技术、技巧和建议：

* [存储过程][存储过程]
* [标签][标签]
* [视图][视图]
* [临时表][临时表]
* [动态 SQL][动态 SQL]
* [循环][循环]
* [group by 选项][group by 选项]
* [变量赋值][变量赋值]

## <a name="next-steps"></a>后续步骤
阅读开发文章后，请浏览 [Transact-SQL 参考][Transact-SQL 参考]页，以了解有关 SQL 数据仓库支持的语法的更多详细信息。

<!--Image references-->

<!--Article references-->
[并发]: ./sql-data-warehouse-develop-concurrency.md
[连接]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[动态 SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by 选项]: ./sql-data-warehouse-develop-group-by-options.md
[标签]: ./sql-data-warehouse-develop-label.md
[循环]: ./sql-data-warehouse-develop-loops.md
[统计信息]: ./sql-data-warehouse-tables-statistics.md
[存储过程]: ./sql-data-warehouse-develop-stored-procedures.md
[表分布]: ./sql-data-warehouse-tables-distribute.md
[表索引]: ./sql-data-warehouse-tables-index.md
[表分区]: ./sql-data-warehouse-tables-partition.md
[临时表]: ./sql-data-warehouse-tables-temporary.md
[事务]: ./sql-data-warehouse-develop-transactions.md
[用户定义的架构]: ./sql-data-warehouse-develop-user-defined-schemas.md
[变量赋值]: ./sql-data-warehouse-develop-variable-assignment.md
[视图]: ./sql-data-warehouse-develop-views.md
[Transact-SQL 参考]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[重命名对象]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


