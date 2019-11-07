---
title: 性能优化和结果集缓存
description: Azure SQL 数据仓库的结果集缓存功能概述
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692952"
---
# <a name="performance-tuning-with-result-set-caching"></a>性能优化和结果集缓存  
启用结果集缓存后，Azure SQL 数据仓库会自动缓存用户数据库中的查询结果以供重复使用。  这允许后续查询执行直接从持久缓存中获取结果，因此不需要重新计算。   结果集缓存提高了查询性能，并减少了计算资源的使用。  此外，使用缓存的结果集的查询不使用任何并发槽，因此不会对现有并发限制进行计数。 为安全，如果用户具有与创建缓存结果相同的数据访问权限，则用户只能访问缓存的结果。  

## <a name="key-commands"></a>关键命令
[开启/关闭用户数据库的结果集缓存](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[为会话打开/关闭结果集缓存](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[检查缓存的结果集的大小](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[清理缓存](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>未缓存的内容  

为数据库启用结果集缓存后，将缓存所有查询的结果，直到缓存已满，但以下查询除外：
- 使用非确定性函数（如 DateTime）的查询。 Now （）
- 使用用户定义函数的查询
- 使用启用了行级别安全性的表或列级安全的查询
- 查询返回行大小大于64KB 的数据

> [!IMPORTANT]
> 用于创建结果集缓存和从缓存中检索数据的操作在数据仓库实例的控制节点上发生。 当结果集缓存处于开启状态时，运行返回大型结果集的查询（例如，> 1 百万行）可能会导致控制节点上 CPU 使用率较高，并减缓对实例的总体查询响应。  这些查询通常在数据浏览或 ETL 操作过程中使用。 若要避免压力过大控制节点并导致性能问题，用户应在运行这些类型的查询之前关闭数据库的结果集缓存。  

在查询的结果集缓存操作所用的时间运行此查询：

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

下面是在禁用了结果集缓存的情况上执行的查询的示例输出。

![查询-带-rsc-disabled 的步骤](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

下面是已启用结果集缓存的查询的示例输出。

![查询-带-rsc-已启用的步骤](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>使用缓存结果时

如果满足以下所有要求，则在查询中重复使用缓存的结果集：
- 运行查询的用户可以访问查询中引用的所有表。
- 新查询和生成结果集缓存的上一个查询之间完全匹配。
- 在其中生成缓存的结果集的表中不存在数据或架构更改。

运行此命令以检查是否已执行查询，但结果缓存命中或未命中。 如果缓存命中，result_cache_hit 将返回1。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>管理缓存的结果 

每个数据库的结果集缓存的最大大小为 1 TB。  基础查询数据更改时，缓存的结果会自动失效。  

缓存逐出由 Azure SQL 数据仓库按以下计划自动管理： 
- 如果结果集未使用或已失效，每48小时。 
- 结果集缓存接近最大大小。

用户可以使用以下选项之一手动清空整个结果集缓存： 
- 关闭数据库的结果集缓存功能 
- 连接到数据库时运行 DBCC DROPRESULTSETCACHE

暂停数据库不会清空缓存的结果集。  

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅 [SQL 数据仓库开发概述](sql-data-warehouse-overview-develop.md)。 
