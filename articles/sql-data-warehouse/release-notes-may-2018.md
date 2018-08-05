---
title: Azure SQL 数据仓库发行说明（2018 年 5 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 01b571beba012ae0a1fa27d03f5e0e5454f62aa5
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324949"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 5 月 
Azure SQL 数据仓库持续得到改进。 本文介绍 2018 年 5 月发行的版本中所引入的新功能和所做的更改。 

## <a name="gen-2-instances"></a>第 2 代实例
![alt](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL 数据仓库计算优化的第 2 代层为云数据仓库制定了新的性能标准。 与当前代次相比，客户现在获得的查询性能提高了 5 倍、并发性提高了 4 倍，计算能力提高了 5 倍。 SQL 数据仓库现在可以处理来自单个群集的 128 个并发查询，是所有云数据仓库服务中出类拔萃。

请参阅 Azure 数据部门企业副总裁 Rohan Kumar 撰写的博客通告 [Turbocharge cloud analytics with Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/)（使用 Azure SQL 数据仓库推进云分析）。

## <a name="auto-statistics"></a>自动统计
在基于模型成本的优化器（例如 SQL 数据仓库中的引擎）中，统计对于优化查询计划的生成的至关重要。 如果事先已知道所有查询，则可以确定需要创建哪些统计对象。 但是，如果系统面对临时查询和随机查询（对于数据仓库工作负荷很常见），则系统管理员可能很难预测需要创建哪些统计，从而可能导致查询执行计划的性能欠佳，并延长查询响应时间。 缓解此问题的方法之一是提前在所有表列中创建统计对象。 但是，由于在加载表的过程中需要维护统计对象，导致加载时间变长，因此，这种过程也会造成代价。

SQL 数据仓库现在支持自动创建统计对象，为系统管理员和开发人员提供更高的灵活性、工作效率和易用性，同时可确保系统继续提供优质的执行计划和最佳响应时间。

若要在 SQL 数据仓库中启用或禁用统计的自动创建，请执行以下语句：
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

作为最佳做法和指导，我们建议将 `AUTO_CREATE_STATISTICS` 选项设置为 `ON`。

> [!NOTE]
> 默认已对所有新数据仓库启用自动统计创建。
>  

有关更多详细信息，请参阅 [ALTER DATABASE SET 选项](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)一文。

## <a name="rejected-row-support"></a>拒绝行支持
由于数据加载的高性能和并行性，客户往往使用 [PolyBase（外部表）将数据载入](design-elt-data-loading.md) SQL 数据仓库。 通过 [Azure 数据工厂](http://azure.com/adf)加载数据时，PolyBase 也是默认的加载模型。 

SQL 数据仓库添加了在 [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) 语句中通过 `REJECTED_ROW_LOCATION` 参数定义拒绝行位置的功能。 从外部表执行 [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 后，无法加载的所有行将存储在靠近源的某个文件中，供进一步调查。 

有关拒绝行行为的详细信息，请参阅 [Load confidently with SQL Data Warehouse PolyBase Rejected Row Location](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/)（使用 SQL 数据仓库 PolyBase 拒绝行位置自信地加载数据）博客。

以下示例演示用于指定拒绝行的新语法。

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
用户可以使用 [ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) 修改以前创建的视图，而无需删除/创建该视图并重新应用权限。 

以下示例修改以前创建的视图。
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
[CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) 函数返回端到端串联两个或更多个值后生成的字符串。 它使用第一个参数中指定的分隔符分隔串联值。 `CONCAT_WS` 函数用于生成逗号分隔值 (CSV) 输出。

以下示例演示使用逗号串联一组整数值。
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
该语句返回以下结果：
```
result
---------
1,2,3
```
以下示例演示使用逗号串联一组混合数据类型值。
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
该语句返回以下结果：
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
[sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) 系统存储过程返回有关当前环境支持的数据类型的信息。 通过 ODBC 连接进行连接的工具通常使用它来进行数据类型调查。

以下示例检索 SQL 数据仓库支持的所有数据类型的详细信息。

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>包含 ORDER BY 的 SELECT INTO 行为变更
SQL 数据仓库现在会阻止包含 `ORDER BY` 子句的 `SELECT INTO` 查询。 以前，此操作先将内存中的数据排序，然后将数据插入目标表，并根据表的形状将数据重新排序，因此会成功。

### <a name="previous-behavior"></a>以前的行为
以下语句会成功，但是造成额外的处理开销。
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>当前行为
以下语句会引发错误，指示 `SELECT INTO` 语句中不支持 `ORDER BY` 子句。
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
返回的错误语句：
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>SET PARSEONLY ON 查询状态（行为变更）
用户可以使用 `SET PARSEONLY ON` 语法来让 SQL 数据仓库引擎检查每个 T-SQL 语句的语法并返回任何错误消息，而无需编译或执行该语句。 以前，在 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 系统视图中，这些语句的状态保留在 `Running` 状态中。 `sys.dm_pdw_exec_requests` 视图现在返回 `Complete` 形式的状态。

## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请了解如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]。 如果不熟悉 Azure，在遇到新术语时，可以参考 [Azure 术语表][Azure glossary]。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

* [客户成功案例]
* [博客]
* [功能请求]
* [视频]
* [客户顾问团队博客]
* [堆栈溢出论坛]
* [Twitter]


[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客户顾问团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客户成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[堆栈溢出论坛]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md