---
title: Service Fabric 中的 Reliable Actors | Microsoft 文档
description: 介绍 Reliable Actors 如何在 Reliable Services 上进行分层以及如何使用 Service Fabric 平台的功能。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 088f56f33c85d3c590acf4a2eaa660a9d586f7ec
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Reliable Actors 如何使用 Service Fabric 平台
本文介绍了 Reliable Actors 如何使用 Azure Service Fabric 平台。 Reliable Actors 在有状态的可靠服务（称为*执行组件服务*）的实现托管的框架中运行。 执行组件服务包含管理执行组件的生命周期和消息发送所需的所有组件：

* 执行组件运行时管理生命周期、垃圾回收，并强制执行单线程访问。
* 执行组件服务远程处理侦听器接受对执行组件的远程访问调用，并将它们发送到调度程序，以便路由到合适的执行组件实例。
* 执行组件状态提供程序包装状态提供程序（例如 Reliable Collections 状态提供程序），并为执行组件状态管理提供一个适配器。

这些组件共同构成了 Reliable Actor 框架。

## <a name="service-layering"></a>服务分层
因为执行组件服务本身是一种可靠服务，Reliable Services 的所有[应用程序模型](service-fabric-application-model.md)、生命周期、[打包](service-fabric-package-apps.md)、[部署](service-fabric-deploy-remove-applications.md)、升级和缩放概念同样适用于执行组件服务。

![执行组件服务分层][1]

上图显示了 Service Fabric 应用程序框架和用户代码之间的关系。 蓝色元素代表 Reliable Services 应用程序框架，橙色代表 Reliable Actor 框架，绿色则代表用户代码。

在 Reliable Services 中，服务会继承 `StatefulService` 类。 该类本身派生自 `StatefulServiceBase`（或无状态服务的 `StatelessService`）。 在 Reliable Actors 中，使用执行组件服务。 执行组件服务是一种不同的 `StatefulServiceBase` 类的实现，该类可实现运行执行组件的执行组件模式。 由于执行组件服务只是 `StatefulServiceBase` 的一个实现，因此可以编写自己的派生自 `ActorService` 的服务，并且以与继承 `StatefulService` 时一样的方式实现服务级别的功能，例如：

* 服务备份和还原。
* 共享给所有执行组件的功能，例如断路器。
* 对执行组件服务自身和每个执行组件的远程过程调用。

> [!NOTE]
> Java/Linux 目前不支持有状态服务。

有关详细信息，请参阅[在执行组件服务中实现服务级功能](service-fabric-reliable-actors-using.md)。

## <a name="application-model"></a>应用程序模型
执行组件服务都是 Reliable Services，因此服务的应用程序模型是相同的。 但是，执行组件框架生成工具生成某些应用程序模型文件。

### <a name="service-manifest"></a>服务清单
执行组件框架生成工具自动生成执行组件服务的 ServiceManifest.xml 文件的内容。 此文件包括：

* 执行组件服务类型。 根据执行组件项目名称生成此类型名称。 根据执行组件的持久性属性，还将相应设置 HasPersistedState 标志。
* 代码包。
* 配置包。
* 资源和终结点。

### <a name="application-manifest"></a>应用程序清单
执行组件框架生成工具将为执行组件服务自动创建默认的服务定义。 生成工具填充默认的服务属性：

* 副本集计数由执行组件的持久性属性决定。 每次更改执行组件的持久性属性时，将相应地重置默认服务定义中的副本集计数。
* 分区方案和范围设置为具有完整的 Int64 键范围的统一 Int64。

## <a name="service-fabric-partition-concepts-for-actors"></a>针对执行组件的 Service Fabric 分区概念
执行组件服务是已分区的有状态服务。 执行组件服务的每个分区包含一组执行组件。 服务分区在 Service Fabric 的多个节点中自动分布。 因此，执行组件实例分布到各个节点中。

![执行组件分区和分布][5]

可使用不同的分区方案和分区键范围创建 Reliable Services。 执行组件服务使用具有完整的 Int64 键范围的 Int64 分区方案将执行组件映射到分区。

### <a name="actor-id"></a>执行组件 ID
服务中创建的每个执行组件具有与之关联的唯一 ID，并使用 `ActorId` 类表示。 `ActorId` 是一个不透明的 ID 值，通过生成随机 ID，可将此值用于在各个服务分区中统一分布执行组件：

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


每个 `ActorId` 都经过哈希算法转换为 Int64 类型值。 因此，执行组件服务必须使用具有完整 Int64 键范围的 Int64 分区方案。 不过，`ActorID` 也可以使用自定义 ID 值，包括 GUID/UUID、字符串和 Int64。

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

使用 GUID/UUID 和字符串时，这些值将经过哈希算法转换为 Int64。 但是，如果向 `ActorId` 显式提供 Int64，此 Int64 将直接映射到分区，而无需进行哈希转换。 可以使用此方法来控制将执行组件置于哪个分区。


## <a name="next-steps"></a>后续步骤
* [执行组件状态管理](service-fabric-reliable-actors-state-management.md)
* [执行组件生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
* [执行组件 API 参考文档](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET 代码示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 代码示例](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
