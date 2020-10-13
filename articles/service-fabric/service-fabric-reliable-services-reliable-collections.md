---
title: 可靠集合简介
description: Service Fabric 有状态服务提供可靠集合让你编写高度可用、可缩放且低延迟的云应用程序。
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 6e0f39b994087fe44038e62e85326945589c719a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245120"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Azure Service Fabric 有状态服务中的可靠集合简介

可靠集合可让你编写高度可用、可缩放且低延迟的云应用程序，就像编写单计算机应用程序一样。 **Microsoft.ServiceFabric.Data.Collections** 命名空间中的类提供一组自动使状态具备高可用性的集合。 开发人员只需面向可靠集合 API 编程，并让可靠集合管理复制状态和本地状态。

Reliable Collections 与其他高可用性技术（如 Redis、Azure 表服务和 Azure 队列服务）的主要区别在于其状态以本地方式保存在服务实例中，同时仍实现高可用性。 这意味着：

* 所有读取均在本地进行，可保障读取的低延迟和高吞吐量。
* 所有写入都只产生最少量的网络 IO，可保障写入的低延迟和高吞吐量。

![集合演变图。](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

可以将可靠集合视作 **System.Collections** 类的自然演变：它们是一组新的集合，专为云应用程序和多计算机应用程序设计，且不会为开发人员增加复杂性。 因此，可靠集合的特性如下：

* 可复制：复制状态更改以实现高可用性。
* 异步：API 采用异步模式，以确保在产生 IO 时不会阻止线程。
* 事务性：API 利用事务抽象方法，让可以在某个服务内轻松管理多个可靠集合。
* 持久化或易失性：数据会保存至磁盘，可在发生大规模中断（例如，数据中心断电）时保障持续性。 某些可靠集合还支持易失性模式（带[警告](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)），在这种模式下，所有数据都保留在内存中，例如复制的内存中缓存。

Reliable Collections 提供全新的非常一致保证，使应用程序状态推断变得更轻松。
非常一致通过以下方法实现：确保仅对副本的多数仲裁（包括主副本）记录整个事务后，才完成事务提交。
若要实现较弱的一致性，应用程序可以在异步提交返回之前，回头向客户端/请求者进行确认。

可靠集合 API 由并发集合 API（位于 **System.Collections.Concurrent** 命名空间中）演变而来：

* 异步：返回任务；不同于并发集合，其操作会受到复制及保存。
* 没有 out 参数：使用 `ConditionalValue<T>` 返回 `bool` 和值，而不是返回 out 参数。 `ConditionalValue<T>` 与 `Nullable<T>` 类似，但不要求 T 是一个 struct。
* 事务：使用事务对象，让用户可以对事务中多个 Reliable Collections 上的操作分组。

目前，**Microsoft.ServiceFabric.Data.Collections** 包含三个集合：

* [Reliable Dictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliabledictionary_2)：表示可复制、事务性和异步的键/值对集合。 类似于 **ConcurrentDictionary**，键和值都可以是任何类型。
* [可靠队列](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1)：表示可复制、事务性和异步的严格先进先出 (FIFO) 队列。 类似于 **ConcurrentQueue**，值可以是任何类型。
* [可靠的并发队列](service-fabric-reliable-services-reliable-concurrent-queue.md)：表示用于实现高吞吐量的可复制、事务性和异步的“尽力排序”队列。 它与 **ConcurrentQueue** 类似，值可以是任意类型。

## <a name="next-steps"></a>后续步骤

* [Reliable Collection 指导原则和建议](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [使用可靠集合](service-fabric-work-with-reliable-collections.md)
* [事务和锁](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* 管理数据
  * [备份和还原](service-fabric-reliable-services-backup-restore.md)
  * [通知](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection 序列化](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [序列化和升级](service-fabric-application-upgrade-data-serialization.md)
  * [可靠状态管理器和配置](service-fabric-reliable-services-configuration.md)
* 其他
  * [Reliable Services 快速启动](service-fabric-reliable-services-quick-start.md)
  * [Reliable Collections 的开发人员参考](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
