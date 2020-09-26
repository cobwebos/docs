---
title: 使用结果集缓存优化性能
description: Azure Synapse Analytics 中 Synapse SQL 池的结果集缓存功能概述
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: aeeca38afb82e2dcd86e111d1ae5dcb2e7499f42
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362259"
---
# <a name="performance-tuning-with-result-set-caching"></a>使用结果集缓存优化性能

启用结果集缓存后，Synapse SQL 会自动将查询结果缓存到用户数据库中，以供重复使用。  这样，后续的查询执行就能直接从持久性缓存中获取结果，因此无需重新计算。   结果集缓存提高了查询性能，并减少了计算资源的用量。  此外，使用缓存结果集的查询不会占用任何并发槽，因此不会计入现有的并发限制。 出于安全考虑，如果访问方用户的数据访问权限与创建缓存结果的用户相同，则访问方用户只能访问缓存的结果。  

## <a name="key-commands"></a>关键命令

[对用户数据库启用/禁用结果集缓存](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[对会话启用/禁用结果集缓存](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[检查缓存结果集的大小](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[清理缓存](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>不缓存哪些内容  

对数据库启用结果集缓存后，在缓存填满之前将缓存所有查询的结果，但以下查询除外：

- 使用 DateTime.Now() 等非确定性函数的查询。
- 使用用户定义的函数的查询
- 使用启用了行级安全性或列级安全性的表的查询
- 其返回数据中的行大小超过 64KB 的查询
- 返回大数据（大于 10 GB）的查询 

> [!IMPORTANT]
> 创建结果集缓存以及从缓存中检索数据的操作在 Synapse SQL 池实例的控制节点上进行。
> 当结果集缓存处于打开状态时，运行返回大型结果集（例如，超过 1 GB）的查询可能会导致控制节点上带宽限制较高，并降低实例上的整体查询响应速度。  这些查询通常在数据探索或 ETL 操作期间使用。 为了避免控制节点压力过大并出现性能问题，用户在运行此类查询之前应该对数据库禁用结果集缓存。  

此查询的运行持续时间以针对某个查询执行结果集缓存操作所需的时间为宜：

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

下面是在禁用结果集缓存的情况上执行的某个查询的示例输出。

![屏幕截图显示查询结果，其中包括位置类型和命令。](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

下面是在启用结果集缓存的情况上执行的某个查询的示例输出。

![屏幕截图显示查询结果，其中的命令已选中 * from [D W ResultCache D b] 点 D b o 称为 out。](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>何时使用缓存结果

如果满足以下所有要求，则会对查询重复使用缓存结果集：

- 运行查询的用户有权访问该查询中引用的所有表。
- 新查询与生成结果集缓存的前一查询之间完全匹配。
- 从中生成缓存结果集的表中未发生任何数据或架构更改。

运行以下命令可以检查某个查询是否已执行并出现结果缓存命中或未命中情况。 result_cache_hit 列对缓存命中返回 1，对缓存失误返回 0，而对未使用结果集缓存的原因，则返回负值。 有关详细信息，请检查 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>管理缓存结果

每个数据库的结果集缓存最大大小为 1 TB。  当底层查询数据发生更改时，缓存结果会自动失效。  

缓存逐出由 Synapse SQL 按照以下计划自动管理：

- 如果结果集在 48 小时间隔时间内未使用，或者已失效。
- 当结果集缓存接近最大大小时。

用户可以使用以下选项之一手动清空整个结果集缓存：

- 对数据库禁用结果集缓存功能
- 连接到数据库后运行 DBCC DROPRESULTSETCACHE

暂停数据库不会清空缓存结果集。  

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。
