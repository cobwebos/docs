---
title: 通过结果集缓存进行性能优化
description: Azure Synapse Analytics 中的 Synapse SQL 池的结果集缓存功能概述
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: ee513c141c1690b769363d813c252fe021cf33e6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656500"
---
# <a name="performance-tuning-with-result-set-caching"></a>通过结果集缓存进行性能优化

启用结果集缓存后，Synapse SQL 会自动将查询结果缓存到用户数据库中，以供重复使用。  这允许后续查询执行直接从持久化缓存中获取结果，因此不需要重新计算。   结果集缓存提高了查询性能并减少了计算资源的使用。  此外，使用缓存结果集的查询不使用任何并发槽位，因此不对现有的并发限制进行计数。 为了安全起见，用户只能在与创建缓存结果的用户具有相同的数据访问权限时访问缓存结果。  

## <a name="key-commands"></a>关键命令

[开启/关闭用户数据库的结果集缓存](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[开启/关闭用户数据库的结果集缓存](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[开启/关闭会话的结果集缓存](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[检查缓存结果集的大小](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[清理缓存](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>未缓存的内容  

数据库启用结果集缓存之后，将缓存所有查询的结果，直到缓存已满，以下查询除外：

- 使用非确定性函数的查询（如 DateTime.Now()）
- 使用用户定义的函数的查询
- 使用启用了行级安全性或列级安全性的表的查询
- 返回行大小大于 64 KB 的数据的查询
- 返回大数据（大于 10GB）的查询 

> [!IMPORTANT]
> 用于创建结果集缓存和从缓存中检索数据的操作发生在 Synapse SQL 池实例的控制节点上。
> 当结果集缓存处于打开状态时，运行返回大型结果集（例如，超过 1 GB）的查询可能会导致控制节点上带宽限制较高，并降低实例上的整体查询响应速度。  这些查询通常在数据浏览或 ETL 操作过程中使用。 若要避免对控制节点造成压力并导致性能问题，用户应在运行此类查询之前关闭数据库的结果集缓存。  

运行此查询以获取查询的结果集缓存操作所用的时间：

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

下面是在禁用结果集缓存的情况下执行查询的示例输出。

![Query-steps-with-rsc-disabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

下面是在启用结果集缓存的情况下执行查询的示例输出。

![Query-steps-with-rsc-enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>使用缓存结果时

如果满足以下所有要求，则会为查询重用缓存的结果集：

- 运行查询的用户可以访问该查询中引用的所有表。
- 新查询与生成结果集缓存的上一个查询之间存在完全匹配。
- 生成缓存结果集的表中没有任何数据或架构更改。

运行此命令以检查所执行的查询的结果缓存是命中还是失误。 result_cache_hit 列对缓存命中返回 1，对缓存失误返回 0，而对未使用结果集缓存的原因，则返回负值。 有关详细信息，请检查 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>管理缓存的结果

每个数据集的结果集缓存的最大大小为 1 TB。  当基础查询数据更改时，缓存的结果将自动失效。  

缓存逐出由 Synapse SQL 按照以下计划自动管理：

- 尚未使用结果集或已失效（每 48 小时执行一次）。
- 结果集缓存达到最大大小。

用户可以使用以下选项之一手动清空整个结果集缓存：

- 关闭数据库的结果集缓存功能
- 连接到数据库时运行 DBCC DROPRESULTSETCACHE

暂停数据库不会清空缓存的结果集。  

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。
