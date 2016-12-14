---
title: "将 SQL 代码迁移到 SQL 数据仓库 | Microsoft 文档"
description: "有关在开发解决方案时将 SQL 代码迁移到 Azure SQL 数据仓库的技巧。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 19c252a3-0e41-4eec-9d3e-09a68c7e7add
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0ff5ad648d429da433170301205eafb850be5d81


---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>将 SQL 代码迁移到 SQL 数据仓库
将代码从其他数据库迁移到 SQL 数据仓库时，你很可能需要更改代码库。 某些 SQL 数据仓库功能设计为以分布方式运行，因此可以大幅改善性能。 但是，为了保持性能和缩放性，某些功能还无法使用。

## <a name="common-t-sql-limitations"></a>常见的 T-SQL 限制
以下列表汇总了最常用但却不受 Azure SQL 数据仓库支持的功能。 单击这些链接可以转到解决不支持功能的方法：

* [UPDATE 中的 ANSI JOIN][UPDATE 中的 ANSI JOIN]
* [DELETE 中的 ANSI JOIN][DELETE 中的 ANSI JOIN]
* [Merge 语句][Merge 语句]
* 跨数据库联接
* [游标][游标]
* [SELECT..INTO][SELECT..INTO]
* [INSERT..EXEC][INSERT..EXEC]
* output 子句
* 内联用户定义的函数
* 多语句函数
* [通用表表达式](#Common-table-expressions)
* [递归通用表表达式 (CTE)](#Recursive-common-table-expressions-(CTE)
* CLR 函数和过程
* $partition 函数
* 表变量
* 表值参数
* 分布式事务
* 提交/回滚工作
* 保存事务
* 执行上下文 (EXECUTE AS)
* [结合 rollup / cube / grouping sets 选项的 Group By 子句][结合 rollup / cube / grouping sets 选项的 Group By 子句]
* [嵌套级别超过 8][嵌套级别超过 8]
* [通过视图更新][通过视图更新]
* [使用 select 分配变量][使用 select 分配变量]
* [动态 SQL 字符串没有 MAX 数据类型][动态 SQL 字符串没有 MAX 数据类型]

幸好可以解决其中的大多数限制。 上面提到的相关开发文章已提供了说明。

## <a name="supported-cte-features"></a>支持的 CTE 功能
SQL 数据仓库支持部分通用表表达式 (CTE)。  目前支持以下 CTE 功能：

* 可以在 SELECT 语句中指定 CTE。
* 可以在 CREATE VIEW 语句中指定 CTE。
* 可以在 CREATE TABLE AS SELECT (CTAS) 语句中指定 CTE。
* 可以在 CREATE REMOTE TABLE AS SELECT (CRTAS) 语句中指定 CTE。
* 可以在 CREATE EXTERNAL TABLE AS SELECT (CETAS) 语句中指定 CTE。
* 可以从 CTE 引用远程表。
* 可以从 CTE 引用外部表。
* 可以在 CTE 中定义多个 CTE 查询定义。

## <a name="cte-limitations"></a>CTE 限制
在 SQL 数据仓库中，通用表表达式存在一些限制，其中包括：

* CTE 必须后接单个 SELECT 语句。 不支持 INSERT、UPDATE、DELETE 和 MERGE 语句。
* 包含自身引用的通用表表达式（即递归通用表表达式）不受支持（参见以下部分）。
* 不允许在 CTE 中指定多个 WITH 子句。 例如，如果 CTE_query_definition 包含子查询，则该子查询不能包含用于定义其他 CTE 的嵌套式 WITH 子句。
* ORDER BY 子句不能用于 CTE_query_definition，除非指定了 TOP 子句。
* 将 CTE 用在属于批处理一部分的语句中时，该 CTE 之前的语句必须以分号结尾。
* 用在通过 sp_prepare 进行准备的语句中时，CTE 的表现方式与 PDW 中的其他 SELECT 语句相同。 但是，如果 CTE 用作 sp_prepare 所准备的 CETAS 的一部分，则因为针对 sp_prepare 实现绑定的方式不同，CTE 的行为将与 SQL Server 和其他 PDW 语句不同。 如果引用 CTE 的 SELECT 使用了不在 CTE 中的错误列，sp_prepare 将会通过且不检测错误，但在 sp_execute 期间将引发错误。

## <a name="recursive-ctes"></a>递归 CTE
SQL 数据仓库不支持递归 CTE。  递归 CTE 的迁移过程可能有点复杂，最好是将其分为多个步骤来进行。 通常你可以使用循环，并在循环访问递归的临时查询时填充临时表。 填充临时表之后，你可以使用单个结果集返回数据。 类似的方法已用于解决[结合 rollup / cube / grouping sets 选项的 Group By 子句][结合 rollup / cube / grouping sets 选项的 Group By 子句]一文中所述的 `GROUP BY WITH CUBE`。

## <a name="unsupported-system-functions"></a>不支持的系统函数
还有一些不支持的系统函数。 在数据仓库中，你可能经常发现使用了下面这些主要函数：

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

其中的许多问题都可以得到解决。

## <a name="rowcount-workaround"></a>@@ROWCOUNT 解决方法
若要解决缺少对 @@ROWCOUNT, 支持的问题，创建一个将从 sys.dm_pdw_request_steps 中检索最后一个行计数的存储过程，然后在 DML 语句后执行 `EXEC LastRowCount`。

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>后续步骤
有关所有支持的 T-SQL 语句的完整列表，请参阅 [Transact-SQL 主题][Transact-SQL 主题]。

<!--Image references-->

<!--Article references-->
[UPDATE 中的 ANSI JOIN]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE 中的 ANSI JOIN]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Merge 语句]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL 主题]: ./sql-data-warehouse-reference-tsql-statements.md

[游标]: ./sql-data-warehouse-develop-loops.md
[SELECT..INTO]: ./sql-data-warehouse-develop-ctas.md#selectinto
[结合 rollup / cube / grouping sets 选项的 Group By 子句]: ./sql-data-warehouse-develop-group-by-options.md
[嵌套级别超过 8]: ./sql-data-warehouse-develop-transactions.md
[通过视图更新]: ./sql-data-warehouse-develop-views.md
[使用 select 分配变量]: ./sql-data-warehouse-develop-variable-assignment.md
[动态 SQL 字符串没有 MAX 数据类型]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


