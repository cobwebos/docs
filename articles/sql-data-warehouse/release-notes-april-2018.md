---
title: Azure SQL 数据仓库发行说明（2018 年 4 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: ae3d4c3e732024baae29f75fda6f6e821af701a2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630337"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 4 月
Azure SQL 数据仓库持续得到改进。 本文介绍 2018 年 4 月发行的版本中所引入的新功能和所做的更改。

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>在切换前截断分区的功能
客户通常使用分区切换作为将一个表的数据加载到另一个表的模式，具体方法是通过 `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` 语法来更改表的元数据。 当目标分区包含数据时，SQL 数据仓库不支持分区切换。 如果目标分区已包含数据，则客户将需要截断目标分区，然后执行切换。

SQL 数据仓库现在支持在单个 T-SQL 语句中执行此操作。

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
有关详细信息，请参阅 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) 一文。

## <a name="improved-query-compilation-performance"></a>改进的查询编译性能
SQL 数据仓库引入了一系列更改，以提升分布式查询的查询编译步骤。 这些更改将查询编译速度提升达 10 倍，从而减少了整个查询执行运行时。 对于含有大量对象（表、函数、视图、过程）的数据仓库，这些更改所带来的速度提升更为明显。

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>DBCC 命令不会占用并发插槽（行为变更）
SQL 数据仓库支持 T-SQL [DBCC 命令](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql)（例如，[DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql)）的子集。 以前，这些命令会占用[并发插槽](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots)，从而减少了可以执行的用户加载/查询数。 现在，`DBCC` 命令在不会占用资源插槽的本地队列中运行，从而提升了总体查询执行性能。

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>更新了文本过多时的错误消息（行为变更）
以前，当查询包含过多文本时，SQL 数据仓库将添加近似计数。
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

错误消息已被更新为仅指示你已达到文本上限。
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

有关详细信息，请参阅[容量限制](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits)文章中的[查询](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries)部分，以了解有关最大限制的详细信息。

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>删除了 SYS.PDW_DATABASE_MAPPINGS 视图（行为变更）
在 SQL 数据仓库中未使用此 `sys.pdw_database_mappings` 视图。 以前，选择此视图将不会返回任何结果。 此视图已被删除。 