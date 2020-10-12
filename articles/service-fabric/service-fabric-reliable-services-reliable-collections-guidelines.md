---
title: 有关可靠集合的指导原则
description: 在 Azure Service Fabric 应用程序中使用 Service Fabric 可靠集合的相关指导原则和建议。
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 63e6de436bdaceed7f1d2a78e8385dd14bfc0ed6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260919"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Azure Service Fabric 中 Reliable Collections 的相关指导原则和建议
本部分提供有关使用可靠状态管理器和 Reliable Collections 的指导原则。 目的是帮助用户避免常见错误。

这些指导原则被归纳整理成简单的建议，冠以*务必*、*请考虑*、*避免*和*切勿*等提示语。

* 切勿修改读取操作（例如 `TryPeekAsync` 或 `TryGetValueAsync`）返回的自定义类型的对象。 Reliable Collections 与 Concurrent Collections 一样，返回对这些对象的引用，而非副本。
* 在修改返回的自定义类型的对象之前，务必对其进行深层复制。 由于结构和内置类型均按值传递，因此无需对其进行深层复制，除非它们包含要修改的引用类型字段或属性。
* 切勿对超时值使用 `TimeSpan.MaxValue` 。 应使用超时值来检测死锁。
* 切勿在已提交、中止或释放一个事务之后使用该事务。
* 切勿在对其创建的事务范围之外使用枚举。
* 切勿在另一个事务的 `using` 语句内创建事务，因为它可能会导致死锁。
* 不要通过 `IReliableStateManager.GetOrAddAsync` 创建可靠状态，请在同一事务中使用可靠状态。 这会导致 InvalidOperationException。
* 务必确保 `IComparable<TKey>` 实现正确。 系统依赖 `IComparable<TKey>` 进行检查点和行的合并。
* 意图更新某项而读取该项时，切勿更新锁以防止出现某类死锁。
* 请考虑将每个分区的可靠集合数保持在 1000 以下。 建议在可靠集合中包含较多项，而不是使用较多可靠集合且在每个集合中包含较少项。
* 请考虑保留 80 KB 以下的项（例如 Reliable Dictionary 的 TKey + TValue）：越小越好。 这会减少大型对象堆的使用量，并降低磁盘和网络 IO 的要求。 通常情况下，还会减少在只更新一小部分值时复制的重复数据。 在 Reliable Dictionary 中实现此效果的常用方法是将一行划分为多行。
* 请考虑使用备份和还原功能进行灾难恢复。
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
* 应用程序在可靠集合中保留的数据的安全性/隐私性是用户决定，并受到存储管理的保护；即 操作系统磁盘加密可用于保护静态数据。
* `ReliableDictionary` 枚举使用按键排序的排序数据结构。 为了使枚举高效，提交将会被添加到临时哈希表中，然后被移动到检查点后的主排序数据结构中。 如果需要验证检查是否存在键，“添加”/“更新”/“删除”操作的最佳运行时为 O(1)，最差运行时为 O(log n)。 Gets 可能是 O(1) 或 O(log n)，具体取决于你是从最近的提交还是从旧的提交中进行读取。

## <a name="volatile-reliable-collections"></a>易失可靠集合
决定使用易失可靠集合时，请考虑以下事项：

* ```ReliableDictionary``` 具有易失性支持
* ```ReliableQueue``` 具有易失性支持
* ```ReliableConcurrentQueue``` 不具有易失性支持
* 持久化服务无法变为易失性服务。 将 ```HasPersistedState``` 标志改为 ```false``` 需要从头开始重新创建整个服务
* 易失性服务无法变为持久化服务。 将 ```HasPersistedState``` 标志改为 ```true``` 需要从头开始重新创建整个服务
* ```HasPersistedState``` 是服务级别的配置。这意味着所有集合都可归为持久化集合和易失性集合中的一种。 不能将易失性集合和持久化集合混合
* 易失性分区的仲裁丢失会导致数据完全丢失
* 备份和还原不可用于易失性服务

## <a name="next-steps"></a>后续步骤
* [使用可靠集合](service-fabric-work-with-reliable-collections.md)
* [事务和锁](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* 管理数据
  * [备份和还原](service-fabric-reliable-services-backup-restore.md)
  * [通知](service-fabric-reliable-services-notifications.md)
  * [序列化和升级](service-fabric-application-upgrade-data-serialization.md)
  * [可靠状态管理器和配置](service-fabric-reliable-services-configuration.md)
* 其他
  * [Reliable Services 快速启动](service-fabric-reliable-services-quick-start.md)
  * [Reliable Collections 的开发人员参考](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
