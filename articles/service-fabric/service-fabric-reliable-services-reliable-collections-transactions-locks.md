---
title: "事务和在 Azure 中的锁定模式服务 Fabric Reliable Collections |Microsoft 文档"
description: "Azure Service Fabric 可靠状态管理器和 Reliable Collections 事务和锁定。"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: 3452473f5b2f86d29e46339c997193bc6403736a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>事务和 Azure Service Fabric Reliable Collections 中的锁模式

## <a name="transaction"></a>事务
事务是工作的作为单个逻辑单元执行的操作的序列。
事务必须展示以下 ACID 属性。 (请参阅： https://technet.microsoft.com/en-us/library/ms190612)
* **原子性**： 事务必须是原子工作单元。 换而言之，执行其所有的数据修改，或者其中任何一个执行。
* **一致性**： 事务完成后，必须使处于一致状态的所有数据。 所有内部数据结构必须是事务的正确的末尾。
* **隔离**： 必须独立于所做的任何其他并发事务修改由并发事务的修改。 由执行该操作 IReliableState 确定用于 itransaction:: 中的某个操作的隔离级别。
* **持续性**: 事务已完成后，其效果是永久性系统中。 即使发生系统故障时保留了修改。

### <a name="isolation-levels"></a>隔离级别
隔离级别定义的事务必须是与受其他事务所做的修改隔离的程度。
有两个 Reliable Collections 支持的隔离级别：

* **Repeatable Read**： 指定语句不能读取已修改但是尚未由其他事务提交的数据和任何其他事务都可以修改当前事务完成之前已读取的当前事务的数据。 有关更多详细信息，请参阅[https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)。
* **快照**： 指定在事务中任何语句读取的数据是在事务开始时存在的数据的事务处理方式一致版本。
  事务只能识别在事务开始之前提交的数据修改。
  受其他事务所做的当前事务开始后的数据修改不在当前事务中执行的语句对可见。
  其效果就好像在事务中的语句获得提交数据的快照，在事务开始时存在。
  在 Reliable Collections，快照是一致的。
  有关更多详细信息，请参阅[https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)。

Reliable Collections 自动选择要用于给定的读取操作具体取决于该操作和副本的角色时的事务的创建的隔离级别。
以下是描述了 Reliable Dictionary 和队列操作的隔离级别默认值的表。

| 操作 \ 角色 | 主 | 辅助 |
| --- |:--- |:--- |
| 单个实体读取 |Repeatable Read |快照 |
| 枚举计数 |快照 |快照 |

> [!NOTE]
> 有关单个实体操作的常见示例包括`IReliableDictionary.TryGetValueAsync`， `IReliableQueue.TryPeekAsync`。
> 

Reliable Dictionary 和 Reliable Queue 都支持读取你的写入。
换而言之，在事务中的任何写入将对属于同一个事务的后续读取可见。

## <a name="locks"></a>锁
在 Reliable Collections，严格的所有事务实现两都阶段锁定： 事务不会释放直到因中止或提交的事务终止获取的锁。

Reliable Dictionary 使用行级锁定单个实体的所有操作。
Reliable Queue 权衡并发事务的严格先进先出属性。
Reliable Queue 使用允许一个事务的操作级锁`TryPeekAsync`和/或`TryDequeueAsync`和一个事务`EnqueueAsync`一次。
请注意，若要保留 FIFO，如果`TryPeekAsync`或`TryDequeueAsync`不断观察到可靠的队列为空，它们将会锁定`EnqueueAsync`。

写入操作始终采用排他锁。
对于读取操作，锁定取决于几个因素。
使用快照隔离完成任何读取的操作是无锁定的。
默认情况下的任何可重复的读取操作均采用共享锁。
但是，对于任何支持可重复读取的读取操作，用户可以要求使用更新锁而不是共享锁。
更新锁是用于防止当多个事务锁定在以后的潜在更新资源时发生的死锁的常见形式的非对称锁。

下表中找不到锁兼容性矩阵：

| 请求 \ 授予 | 无 | 共享 | 更新 | 独占 |
| --- |:--- |:--- |:--- |:--- |
| 共享 |不会发生冲突 |不会发生冲突 |冲突 |冲突 |
| 更新 |不会发生冲突 |不会发生冲突 |冲突 |冲突 |
| 独占 |不会发生冲突 |冲突 |冲突 |冲突 |

Reliable Collections Api 中的超时参数用于死锁检测。
例如，两个事务 （T1 和 T2） 正在尝试读取和更新 K1。
它它们仍可能发生死锁，因为它们最后都拥有共享锁。
在这种情况下，一个或两个操作将超时。

此死锁方案很极好的示例的更新锁可如何防止死锁。

## <a name="next-steps"></a>后续步骤
* [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [可靠服务通知](service-fabric-reliable-services-notifications.md)
* [Reliable Services 备份和还原 （灾难恢复）](service-fabric-reliable-services-backup-restore.md)
* [可靠状态管理器配置](service-fabric-reliable-services-configuration.md)
* [Reliable Collections 的开发人员参考](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

