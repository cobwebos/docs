---
title: Azure SQL 数据仓库发行说明（2018 年 6 月）| Microsoft Docs
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
ms.openlocfilehash: 1db503ea40a9f07720aa9c130cd3bcc22f87f2af
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324813"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 6 月
Azure SQL 数据仓库持续得到改进。 本文介绍了 2018 年 6 月发行的版本中所引入的新功能和所做的更改。 

## <a name="user-defined-restore-points"></a>用户定义的还原点
SQL 数据仓库每 8 小时就会自动拍摄数据仓库的快照，从而保证八小时恢复点目标 (RPO)。 虽然此自动化快照减轻了运行数据仓库的管理负担，但仍需要根据业务需求在关键时间拍摄快照。 例如，在数据仓库中加载重要的数据或部署新的脚本之前拍摄快照，以在操作前启用恢复点。 

SQL 数据仓库现在通过 [New-AzureRmSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoin) cmdlet 支持[用户定义的还原点](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/)。

```PowerShell
New-AzureRmSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>列级别安全性
管理数据仓库中的数据访问和安全性对于与客户和合作伙伴建立信任至关重要。 SQL 数据仓库[现在支持列级别安全性 (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/)，其允许你通过限制用户对表中特定列的访问来调整查看敏感数据的权限，而无需重新设计数据仓库。

借助 CLS，你可以使用标准的 [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) T-SQL 语句根据用户的执行上下文或其组成员身份，控制对表列的访问。 访问限制逻辑位于数据库层本身，而不是脱离另一应用程序中的数据，同时简化了整个安全性实施。


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
[OBJECT_SCHEMA_NAME()]() 函数返回架构范围内对象的数据库架构名称。 在执行对象架构验证时，此函数在 ETL 工具中已然常见。 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**示例结果**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>对 sys.time_zone_info 视图的支持
[sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) 视图会返回 Azure SQL 数据仓库中有关支持的时区的信息。

```sql
SELECT * FROM [sys].[time_zone_info];
```

**示例结果**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>自动统计信息操作出现在 sys.dm_pdw_exec_requests（行为更改）中

通过引入[自动创建统计信息](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics)，Azure SQL 数据仓库将生成统计信息来优化查询执行。 2018 年 6 月版本新增了以下功能：无论何时执行 [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) 操作，通过将记录添加到 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 视图即可监视统计信息何时自动生成。

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**示例结果**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

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