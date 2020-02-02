---
title: 可靠集合中的事务和锁模式
description: Azure Service Fabric 可靠状态管理器和可靠集合事务和锁定。
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938912"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Azure Service Fabric 可靠集合中的事务和锁模式

## <a name="transaction"></a>事务

事务是作为单个逻辑工作单元执行的一系列操作。 它展示了数据库事务的公共[ACID](https://en.wikipedia.org/wiki/ACID) （*原子*性、*一致性*、*隔离*性和*持久性*）属性：

* **原子性**：事务必须是原子工作单元。 换而言之，要么执行其所有数据修改，要么一个数据修改也不执行。
* **一致性**：完成后，事务必须使所有数据处于一致状态。 事务结束时，所有内部数据结构必须都正确。
* **隔离**：并发事务所做的修改必须与任何其他并发事务所做的修改隔离。 [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet)中用于操作的隔离级别由执行该操作的[IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet)确定。
* **持久性**：事务完成后，其效果永久存在于系统中。 该修改即使出现系统故障也将一直保持。

### <a name="isolation-levels"></a>隔离级别

隔离级别定义必须从其他事务所作修改中隔离事务的程度。
Reliable Collections 支持两种隔离级别：

* **可重复的读取**：指定语句不能读取已由其他事务修改但尚未提交的数据，并且指定，其他任何事务都不能在当前事务完成之前修改由当前事务读取的数据。
* **快照**：指定事务中任何语句读取的数据都是事务开始时便存在的数据的事务上一致的版本。
  事务只能识别在其开始之前提交的数据修改。
  在当前事务中执行的语句将看不到在当前事务开始以后由其他事务所做的数据修改。
  其效果就好像事务中的语句获得了已提交数据的快照，因为该数据在事务开始时就存在。
  快照跨 Reliable Collections 一致。

Reliable Collections 会在事务创建时根据副本的操作和角色，为指定读取操作自动选择要使用的隔离级别。
下表描述了用于 Reliable Dictionary 和 Reliable Queue 操作的默认隔离级别。

| 操作\角色 | 主 | 辅助副本 |
| --- |:--- |:--- |
| 单个实体读取 |可重复的读取 |快照 |
| 枚举、计数 |快照 |快照 |

> [!NOTE]
> 单个实体操作的常见示例为 `IReliableDictionary.TryGetValueAsync`、`IReliableQueue.TryPeekAsync`。
> 

可靠字典和可靠队列都支持*读取写入*。
换而言之，事务中的任何写入都将对属于同一事务的后续读取可见。

## <a name="locks"></a>锁

在可靠集合中，所有事务都严格实施两个阶段的锁定：在以中止或提交操作终止事务之前，该事务不会释放所获取的锁。

可靠字典对所有单个实体操作使用行级锁定。
Reliable Queue 权衡严格事务性 FIFO 属性的并发。
可靠队列使用操作级别锁，允许一次具有 `TryPeekAsync` 和/或 `TryDequeueAsync` 的事务以及一次 `EnqueueAsync` 的一个事务。
请注意，为保留 FIFO，如果 `TryPeekAsync` 或 `TryDequeueAsync` 曾观察到 Reliable Queue 为空，则它们将锁定 `EnqueueAsync`。

写入操作始终采用排他锁。
对于读取操作，锁定取决于几个因素：

- 使用快照隔离完成的任何读取操作都是无锁的。
- 任何可重复读取操作默认情况下均采用共享锁。
- 但是，对于任何支持可重复读取的读取操作，用户可以要求使用更新锁而非共享锁。
更新锁是一种非对称锁，用于防止当多个事务为随后可能进行的更新锁定资源时发生常见的死锁。

锁兼容性矩阵可在下表中找到：

| 请求\授予 | 无 | 共享 | 更新 | 排他 |
| --- |:--- |:--- |:--- |:--- |
| 共享 |无冲突 |无冲突 |冲突 |冲突 |
| 更新 |无冲突 |无冲突 |冲突 |冲突 |
| 排他 |无冲突 |冲突 |冲突 |冲突 |

可靠集合 Api 中的超时参数用于死锁检测。
例如，两个事务（T1 和 T2）正在尝试读取和更新 K1。
它们有可能发生死锁，因为它们最后都拥有共享锁。
在这种情况下，其中一个操作或两个操作都将超时。这种情况下，更新锁可能会阻止这种死锁。

## <a name="next-steps"></a>后续步骤

* [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services 通知](service-fabric-reliable-services-notifications.md)
* [Reliable Services 备份和还原（灾难恢复）](service-fabric-reliable-services-backup-restore.md)
* [可靠状态管理器和配置](service-fabric-reliable-services-configuration.md)
* [Reliable Collections 的开发人员参考](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
