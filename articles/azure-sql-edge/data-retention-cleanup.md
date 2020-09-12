---
title: '用保留策略管理历史数据-Azure SQL Edge (预览版) '
description: '了解如何通过 Azure SQL Edge 中的保留策略管理历史数据 (预览) '
keywords: SQL 边缘，数据保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 9acec467819f159623176edf2f3f763a55019eb4
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89550618"
---
# <a name="manage-historical-data-with-retention-policy"></a>用保留策略管理历史数据

数据保留可以在数据库和任何基础表上单独启用，从而允许用户为其表和数据库创建灵活的老化策略。 应用数据保留很简单：只需在创建表或更改表操作期间设置一个参数。 

在定义数据库和基础表的数据保留策略后，将运行后台时间计时器任务，以从表中删除任何已过时的记录以进行数据保留。 在计划并由系统运行的后台任务中，标识匹配行及其从表中删除的操作以透明方式进行。 表行的期限条件根据用作表定义中的列进行检查 `filter_column` 。 例如，如果保留期设置为一周，则符合以下条件的表行可满足以下条件： 

```sql
filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())
```

## <a name="data-retention-cleanup-phases"></a>数据保留清理阶段

数据保留清除操作包括两个阶段。 
- 发现阶段-在此阶段中，清除操作将标识用户数据库中的所有表，以生成用于清理的列表。 发现一天运行一次。
- 清理阶段-在此阶段中，将对具有有限数据保留期的所有表运行清理，并在发现阶段中标识。 如果在表中无法执行清理操作，则会在当前运行中跳过该表，并将在下一次迭代中重试。 清理过程中将使用以下原则
    - 如果已过时的行被另一个事务锁定，则会跳过该行。 
    - 使用默认的5秒锁超时设置清除运行。 如果无法在超时时段内的表中获取锁，则会在当前运行中跳过该表，并将在下一次迭代中重试。
    - 如果在表的清理过程中出现错误，则将跳过该表，并在下一次迭代中选取该表。

## <a name="manual-cleanup"></a>手动清理

根据表中的数据保留设置和数据库的工作负荷的性质，在运行期间，自动清理线程可能无法完全删除所有过时的行。 为了协助此操作并允许用户手动删除过时的行，已 `sys.sp_cleanup_data_retention` 在 AZURE SQL Edge (预览) 中引入了该存储过程。 

此存储过程采用三个参数。 
    - 架构名称-表的所属架构的名称。 这是必需参数。 
    - 表名-正在运行手动清除的表的名称。 这是必需参数。 
    - 行计数 (输出) 输出变量。 返回手动清理 sp 清除的行数。 这是一个可选参数。 

下面的示例演示了如何为表执行手动清理 sp `dbo.data_retention_table` 。

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>如何删除过时行

清理过程取决于表的索引布局。 将创建一个后台任务，以对具有有限保留期的所有表执行过时的数据清理。 清除行存储 (B 树或堆) 索引的逻辑将删除较小块区中的过期行 (最多 10K) 最大程度地降低数据库日志和 IO 子系统的压力。 虽然清理逻辑使用要求的 B 树索引，但无法完全保证删除超过保留期的行的顺序。 因此，请不要对应用程序中的清理顺序有任何期待  。

聚集列存储的清理任务立即删除整个行组 (通常包含1000000行) ，这非常高效，尤其是在生成数据时，更高的速度。

![数据保持清除](./media/data-retention-cleanup/data-retention-cleanup.png)

当工作负荷快速生成大量数据时，理想的数据压缩和高效的保留清理使聚集列存储索引成为最佳选择。

> [!Note]
> 在 B 树索引和堆的情况下，数据保留对基础表运行删除查询，这可能会与表的 delete 触发器冲突。 建议删除表中的 delete 触发器，或不启用具有删除 DML 触发器的表的数据保留。

## <a name="monitoring-data-retention-cleanup"></a>监视数据保持清除

可以使用 Azure SQL Edge (预览) 中的扩展事件 (XEvents) 监视数据保留策略清理操作。 有关扩展事件的详细信息，请参阅 [XEvents 概述](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)。

下面六个扩展事件有助于跟踪清理操作的状态。 

| 名称 | 说明 |
|------| ------------|
| data_retention_task_started  | 在启动带有保留策略的表清理的后台任务时发生。 |
| data_retention_task_completed  | 在对具有保留策略的表进行清理的后台任务结束时发生。 |
| data_retention_task_exception  | 在特定于表的保留清理过程外，用保留策略清理表的后台任务失败时发生。 |
| data_retention_cleanup_started  | 当启动带有数据保留策略的表的清理过程开始时发生。 |
| data_retention_cleanup_exception  | 出现保留策略的表的清理过程失败。 |
| data_retention_cleanup_completed  | 当包含数据保留策略的表的清理过程结束时发生。 |


## <a name="next-steps"></a>后续步骤
- [数据保留策略](data-retention-overview.md)
- [启用和禁用数据保留策略](data-retention-enable-disable.md)
