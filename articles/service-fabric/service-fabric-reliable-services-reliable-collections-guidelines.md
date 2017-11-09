---
title: "Azure Service Fabric 中 Reliable Collections 的相关指导原则和建议 | Microsoft Docs"
description: "有关使用 Service Fabric Reliable Collections 的指导原则和建议"
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
ms.date: 5/3/2017
ms.author: mcoskun
ms.openlocfilehash: 053a7bca76362035e428fc11806b3e4f83d00946
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Azure Service Fabric 中 Reliable Collections 的相关指导原则和建议
本章节提供有关使用可靠状态管理器和 Reliable Collections 的指导原则。 目的是帮助用户避免常见的问题。

这些指导原则被归纳整理成简单的建议，冠以*务必*、*请考虑*、*避免*和*切勿*等提示语。

* 切勿修改读取操作（例如 `TryPeekAsync` 或 `TryGetValueAsync`）返回的自定义类型的对象。 Reliable Collections 与 Concurrent Collections 一样，将返回对这些对象的引用，而非副本。
* 在修改返回的自定义类型的对象之前，务必对其进行深层复制。 由于结构和内置类型均按值传递，因此无需对其进行深层复制。
* 切勿对超时值使用 `TimeSpan.MaxValue`。 应使用超时值来检测死锁。
* 切勿在已提交、中止或释放一个事务之后使用该事务。
* 切勿在对其创建的事务范围之外使用枚举。
* 切勿在另一个事务的 `using` 语句内创建事务，因为它可能会导致死锁。
* 务必确保 `IComparable<TKey>` 实现正确。 系统依赖 `IComparable<TKey>` 进行检查点和行的合并。
* 意图更新某项而读取该项时，切勿更新锁以防止出现某类死锁。
* 请考虑保留 80 KB 以下的项（例如 Reliable Dictionary 的 TKey + TValue）：越小越好。 这会减少大型对象堆的使用量，并降低磁盘和网络 IO 的要求。 通常情况下，还会减少在只更新一小部分值时复制的重复数据。 在 Reliable Dictionary 中实现此效果的常用方法是将一行划分为多行。
* 请考虑使用备份和还原功能来进行灾难恢复。
* 避免在同一事务中混合使用单个实体操作和多个实体操作（例如 `GetCountAsync`、`CreateEnumerableAsync`），因为它们的隔离级别不同。
* 务必处理 InvalidOperationException。 系统可能出于各种原因中止用户事务。 例如，当可靠状态管理器将其角色从“主要”更改为其他角色时，或者当长时间运行的事务阻止截断事务日志时。 在这类情况下，用户可能会收到 InvalidOperationException，指示其事务已终止。 假设用户未请求终止事务，那么，处理此异常的最佳方式是释放事务，并检查是否发出了取消令牌（或者是否更改了副本的角色），如果没有，则创建新的事务并重试。  

需谨记以下几点：

* 所有 Reliable Collection API 的默认超时值均为 4 秒。 大多数用户应使用默认超时值。
* 所有 Reliable Collections API 中的默认取消标记均为 `CancellationToken.None`。
* 可靠字典的键类型参数 (*TKey*) 必须正确实现 `GetHashCode()` 和 `Equals()`。 键必须不可变。
* 若要实现 Reliable Collections 的高可用性，每个服务应至少有一个目标，并且最小副本集大小必须为 3。
* 针对辅助副本的读取操作可能会读取未提交仲裁的版本。
  这意味着从单个辅助副本读取的数据版本可能被错误处理。
  从主副本读取的数据始终是可靠的，绝不会被错误处理。

### <a name="next-steps"></a>后续步骤
* [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [事务和锁](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* [可靠状态管理器和 Reliable Collection 的内部结构](service-fabric-reliable-services-reliable-collections-internals.md)
* 管理数据
  * [备份和还原](service-fabric-reliable-services-backup-restore.md)
  * [通知](service-fabric-reliable-services-notifications.md)
  * [序列化和升级](service-fabric-application-upgrade-data-serialization.md)
  * [可靠状态管理器和配置](service-fabric-reliable-services-configuration.md)
* 其他
  * [Reliable Services 快速启动](service-fabric-reliable-services-quick-start.md)
  * [Reliable Collections 的开发人员参考](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
