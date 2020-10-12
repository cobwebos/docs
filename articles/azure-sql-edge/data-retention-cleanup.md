---
title: 使用保留策略管理历史数据 - Azure SQL Edge
description: 了解如何使用 Azure SQL Edge 中的保留策略管理历史数据
keywords: SQL Edge, 数据保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 45ce874ffb626f63b2239c66afdefd091114cbd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888136"
---
# <a name="manage-historical-data-with-retention-policy"></a>使用保留策略管理历史数据

可以分别在数据库和任何基础表上启用数据保留，从而支持用户创建用于表和数据库的灵活的老化策略。 应用数据保留很简单：仅需要在创建表或更改表操作期间设置一个参数。 

定义数据库和基础表的数据保留策略后，将运行后台时间计时器任务，以从启用了数据保留的表中删除任何已过时的记录。 识别匹配行并将其从表中删除，这些操作以透明方式在系统计划和运行的后台任务中进行。 根据表定义中用作 `filter_column` 的列检查表行的期限条件。 例如，如果保留期设置为一周，则应清理的表行应符合以下条件之一： 

- 如果筛选器列使用 DATETIMEOFFSET 数据类型，那么条件为 `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- 否则条件为 `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>数据保留清理阶段

数据保留清理操作包括两个阶段。 
- 发现阶段 - 在此阶段中，清理操作将标识用户数据库中的所有表，以生成清理列表。 发现一天运行一次。
- 清理阶段 - 在此阶段中，对发现阶段标识的具有有限数据保留期的所有表运行清理。 如果无法对表执行清理操作，则会在当前运行中跳过该表，并将在下一次迭代中重试。 清理过程中使用以下原则
    - 如果已过时的行被另一个事务锁定，则会跳过该行。 
    - 清理运行，默认锁定超时设置为 5 秒。 如果无法在超时时限内获取表上的锁定，则会在当前运行中跳过该表，并将在下一次迭代中重试。
    - 如果在清理表的过程中发生错误，则会跳过该表，并将在下一次迭代中选取该表。

## <a name="manual-cleanup"></a>手动清理

自动清理线程可能无法在其运行期间完全删除所有已过时的行，具体取决于表上的数据保留设置以及数据库上工作负载的性质。 为了解决此问题并允许用户手动删除过时的行，Azure SQL Edge 中引入了 `sys.sp_cleanup_data_retention` 存储过程。 

此存储过程采用三个参数。 
    - 架构名称 - 表自身的架构的名称。 这是必需参数。 
    - 表名称 - 正在为其运行手动清理的表的名称。 这是必需参数。 
    - rowcount（输出）- 输出变量。 返回由手动清理 sp 清理的行数。 这是一个可选参数。 

以下示例演示了如何为表 `dbo.data_retention_table` 执行手动清理 sp。

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>如何删除已过时的行

清理过程取决于表的索引布局。 对于具有有限保留期的所有表，系统会创建一个后台任务来执行已过时数据的清理。 行存储（B 树或堆）索引的清理逻辑以较小的区块（最大 10K）删除陈旧行，从而可以最大程度地减轻数据库日志和 IO 子系统的压力。 虽然清理逻辑使用要求的 B 树索引，但无法完全保证删除超过保留期的行的顺序。 因此，请不要对应用程序中的清理顺序有任何期待  。

聚集列存储的清理任务可同时移除整个行组（每组通常包含 100 万行），效率非常高，在数据高速生成和老化时尤其如此。

![数据保留清理](./media/data-retention-cleanup/data-retention-cleanup.png)

聚集列存储索引具有优秀的数据压缩和高效的保留清理能力，是工作负载快速生成大量数据时的最佳选择。

> [!Note]
> 对于 B 树索引和堆，数据保留会对基础表运行删除查询，这可能会与表的删除触发器相冲突。 建议删除表中的删除触发器，或不启用具有删除 DML 触发器的表的数据保留。

## <a name="monitoring-data-retention-cleanup"></a>监视数据保留清理

可以使用 Azure SQL Edge 中的扩展事件 (XEvents) 监视数据保留策略清理操作。 有关扩展事件的详细信息，请参阅 [XEvents 概述](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)。 

以下六个扩展事件有助于跟踪清理操作的状态。 

| 名称 | 说明 |
|------| ------------|
| data_retention_task_started  | 在使用保留策略清理表的后台任务开始时发生。 |
| data_retention_task_completed  | 在使用保留策略清理表的后台任务结束时发生。 |
| data_retention_task_exception  | 在特定于表的保留清理过程外，使用保留策略清理表的后台任务失败时发生。 |
| data_retention_cleanup_started  | 在使用数据保留策略的表的清理过程开始时发生。 |
| data_retention_cleanup_exception  | 在使用保留策略清理表的过程失败时发生。 |
| data_retention_cleanup_completed  | 在使用数据保留策略的表的清理过程结束时发生。 |  

此外，sys.dm_os_ring_buffers 动态管理视图中添加了名为 `RING_BUFFER_DATA_RETENTION_CLEANUP` 的新环形缓冲区类型。 此视图可用于监视数据保留清理操作。 


## <a name="next-steps"></a>后续步骤
- [数据保留策略](data-retention-overview.md)
- [启用和禁用数据保留策略](data-retention-enable-disable.md)
