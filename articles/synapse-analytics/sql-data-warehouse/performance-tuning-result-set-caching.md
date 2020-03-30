---
title: 使用结果集缓存优化性能
description: Azure 突触分析中 SQL 分析的结果集缓存功能概述
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
ms.openlocfilehash: 0c2190c29054301a8e21a9a27eb078802fbc9612
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350862"
---
# <a name="performance-tuning-with-result-set-caching"></a>使用结果集缓存优化性能  
启用结果集缓存后，SQL Analytics 会自动缓存用户数据库中的查询结果，以便重复使用。  这样，后续的查询执行就能直接从持久性缓存中获取结果，因此无需重新计算。   结果集缓存提高了查询性能，并减少了计算资源的用量。  此外，使用缓存结果集的查询不会占用任何并发槽，因此不会计入现有的并发限制。 出于安全考虑，如果访问方用户的数据访问权限与创建缓存结果的用户相同，则访问方用户只能访问缓存的结果。  

## <a name="key-commands"></a>关键命令
[对用户数据库启用/禁用结果集缓存](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[对会话启用/禁用结果集缓存](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[检查缓存结果集的大小](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[清理缓存](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>不缓存哪些内容  

数据库启用结果集缓存之后，将缓存所有查询的结果，直到缓存已满，以下查询除外：
- 使用非确定性函数的查询（如 DateTime.Now()）
- 使用用户定义的函数的查询
- 使用启用了行级安全性或列级安全性的表的查询
- 返回行大小大于 64 KB 的数据的查询

> [!IMPORTANT]
> 创建结果集缓存并从缓存中检索数据的操作发生在 SQL Analytics 实例的控制节点上。
> 当结果集缓存处于打开状态时，运行返回大型结果集（例如，超过 1 百万行）的查询可能会导致控制节点上 CPU 使用率较高，并降低实例上的整体查询响应速度。  这些查询通常在数据浏览或 ETL 操作过程中使用。 若要避免对控制节点造成压力并导致性能问题，用户应在运行此类查询之前关闭数据库的结果集缓存。  

此查询的运行持续时间以针对某个查询执行结果集缓存操作所需的时间为宜：

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

下面是在禁用结果集缓存的情况上执行的某个查询的示例输出。

![Query-steps-with-rsc-disabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

下面是在启用结果集缓存的情况上执行的某个查询的示例输出。

![Query-steps-with-rsc-enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>何时使用缓存结果

如果满足以下所有要求，则会为查询重用缓存的结果集：
- 运行查询的用户可以访问该查询中引用的所有表。
- 新查询与生成结果集缓存的上一个查询之间存在完全匹配。
- 生成缓存结果集的表中没有任何数据或架构更改。

运行此命令以检查所执行的查询的结果缓存是命中还是失误。 如果是缓存命中，则 result_cache_hit 将返回 1。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>管理缓存结果 

每个数据集的结果集缓存的最大大小为 1 TB。  当底层查询数据发生更改时，缓存结果会自动失效。  

缓存逐出由 SQL 分析按照此计划自动管理： 
- 如果结果集在 48 小时间隔时间内未使用，或者已失效。 
- 当结果集缓存接近最大大小时。

用户可以使用以下选项之一手动清空整个结果集缓存： 
- 关闭数据库的结果集缓存功能 
- 连接到数据库后运行 DBCC DROPRESULTSETCACHE

暂停数据库不会清空缓存结果集。  

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。 
