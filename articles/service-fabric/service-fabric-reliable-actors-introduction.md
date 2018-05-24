---
title: Service Fabric Reliable Actors 概述 | Microsoft 文档
description: Service Fabric Reliable Actors 编程模型简介。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: d0de7e7596fb7fabbc4061709816111a7a4ad827
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors 简介
Reliable Actors 是基于[虚拟执行组件](http://research.microsoft.com/en-us/projects/orleans/)模式的 Service Fabric 应用程序框架。 Reliable Actors API 提供单一线程编程模型，该模型是基于 Service Fabric 所提供的可扩展性和可靠性保证构建的。

## <a name="what-are-actors"></a>什么是执行组件？
执行组件是一个使用单线程执行的计算和状态的独立单元。 [执行组件模式](https://en.wikipedia.org/wiki/Actor_model)是并发或分布式系统的计算模型。在此类系统中，大量执行组件可同时相互独立地运行。 执行组件可相互进行通信，并且它们可以创建更多执行组件。

### <a name="when-to-use-reliable-actors"></a>何时使用 Reliable Actors
Service Fabric Reliable Actors 是执行组件设计模式的实现。 与任何软件设计模式一样，决定是否使用特定的模式取决于遇到的软件设计问题是否适合用该模式解决。

虽然执行组件设计模式可以很好的适应很多分布式系统问题和场景的需求，但是仍有必要仔细考虑该模式以及实现该模式的框架的限制。 通常对于以下情况，请考虑使用执行组件模式来对问题或场景进行建模：

* 问题空间包含大量（几千或更多）小型、独立的状态和逻辑单元。
* 想要使用单线程对象，这些对象无需外部组件的明显交互，包括查询一组执行组件的状态。
* 执行组件实例不会通过发出 I/O 操作来使用不可预测的延迟阻止调用方。

## <a name="actors-in-service-fabric"></a>Service Fabric 中的执行组件
在 Service Fabric 中，执行组件在 Reliable Actors 框架中实现：在 [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md) 基础上构建的基于执行组件模式的应用程序框架。 编写的每个 Reliable Actor 服务实际上都是一个已分区、有状态的 Reliable Service。

每个执行组件都定义为执行组件类型的一个实例，类似于 .NET 对象是 .NET 类型的一个实例。 例如，可能有用于实现计算器功能的执行组件类型，并可能有很多在群集中各个节点分布的该类型的执行组件。 每个此类执行组件都通过执行组件 ID 进行唯一标识。

## <a name="actor-lifetime"></a>执行组件生存期
Service Fabric 执行组件是虚拟的，这表示其生存期不依赖于其内存中表示形式。 因此，它们不需要显式创建或销毁。 Reliable Actors 运行时会在它第一次接收到执行组件 ID 的请求时自动激活此执行组件。 如果一段时间未使用某个执行组件，则 Reliable Actors 运行时会回收此内存对象。 它还将掌握此执行组件的存在信息，以便将来重新激活。 如需了解更多详情，请参阅[执行组件生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)。

虚拟执行组件生命周期抽象因虚拟执行组件模型而产生一些注意事项，实际上 Reliable Actors 实现有时会偏离此模型。

* 当第一次将消息发送到执行组件的执行组件 ID 时会自动激活此执行组件，并由此构造执行组件对象。 在一段时间之后，将回收此执行组件对象。 将来可以再次使用此执行组件 ID 来构造新的执行组件对象。 在状态管理器中存储时，执行组件的状态的生命周期比此对象的生命周期长。
* 针对某个执行组件 ID 调用任何执行组件方法可激活此执行组件。 出于此原因，执行组件类型允许运行时隐式调用其构造函数。 因此，虽然可通过服务将参数传递给执行组件的构造函数，但是客户端代码无法将参数传递给执行组件类型的构造函数。 结果是如果执行组件需要客户端的初始化参数，则在对其调用其他方法时在部分初始化状态下构造执行组件。 从客户端激活执行组件不存在单一的入口点。
* 虽然 Reliable Actors 隐式创建执行组件对象，仍然能够显示删除执行组件及其状态。

## <a name="distribution-and-failover"></a>分布和故障转移
要提供伸缩性和可靠性，Service Fabric 在整个群集中分布执行组件，并根据需要自动将其从故障节点迁移到正常节点中。 这是对[已分区的有状态 Reliable Service](service-fabric-concepts-partitioning.md) 进行抽象。 由于执行组件在名为*执行组件服务*的有状态 Reliable Service 内部运行，因此分布、可伸缩性、可靠性和自动故障转移全都可提供。

执行组件在执行组件服务的各个分区中分布，而这些分区在一个 Service Fabric 群集的各个节点中分布。 每个服务分区包含一组执行组件。 Service Fabric 管理服务分区的分布和故障转移。

例如，将按以下方式分布具有九个分区的执行组件服务，这些分区使用默认的执行组件分区放置方案部署到三个节点：

![Reliable Actors 分布][2]

执行组件框架管理分区方案和键范围设置。 这可以简化一些选择，但同时也要注意以下情况：

* Reliable Services 允许选择分区方案、键范围（当使用范围分区方案时）和分区计数。 Reliable Actors 仅限于使用范围分区方案（统一 Int64 方案），要求使用完整的 Int64 键范围。
* 默认情况下，执行组件被随机放到分区中，因此而形成统一分布。
* 因为执行组件是随机分布的，所以预计执行组件的操作将始终需要网络通信，包括对方法调用数据的序列化和反序列化，这会产生延迟和开销。
* 在高级方案中，可使用映射到特定分区的 Int64 执行组件 ID 控制执行组件分区放置。 但是，这样做会导致分区间的执行组件的分布不平衡。

若要详细了解如何对执行组件服务进行分区，请参阅[执行组件的分区概念](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)。

## <a name="actor-communication"></a>执行组件通信
执行组件交互在接口中定义，该接口由实现接口的执行组件和通过相同接口获取执行组件代理的客户端共享。 因为该接口用于异步调用执行组件方法，所以接口的每个方法都必须是返回任务的方法。

由于方法的调用及其响应最终导致跨群集的网络请求，因此必须通过平台对这些请求的参数以及所返回的任务的结果类型进行序列化。 特别是，它们必须可进行[数据协定序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。

### <a name="the-actor-proxy"></a>执行组件代理
Reliable Actors 客户端 API 提供一个执行组件实例和一个执行组件客户端之间的通信。 若要与执行组件进行通信，客户端需创建实现执行组件接口的执行组件代理对象。 客户端通过调用代理对象上的方法与执行组件进行交互。 执行组件代理可以用于从客户端到执行组件以及从执行组件到执行组件的通信。

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


请注意用于创建执行组件代理对象的两条信息为执行组件 ID 和应用程序名称。 执行组件 ID 用于唯一标识执行组件，而应用程序名称用于标识其中部署执行组件的 [Service Fabric 应用程序](service-fabric-reliable-actors-platform.md#application-model)。

客户端上的 `ActorProxy`(C#) / `ActorProxyBase`(Java) 类执行必要的解析工作，以便按 ID 查找执行组件，并使用该执行组件打开信道。 如果出现通信故障和故障转移，它还会重新尝试查找执行组件。 因此，消息传送具有以下特征：

* 消息传送将尽力而为。
* 执行组件可能会收到来自同一客户端的重复消息。

## <a name="concurrency"></a>并发
Reliable Actors 运行时提供简单的基于轮次的访问模型用于访问执行组件方法。 这意味着任何时候执行组件对象的代码内仅有一个活动线程。 基于轮次的访问极大简化了并发系统，因为无需使用数据访问的同步机制。 它还意味着系统的设计必须特别考虑每个执行组件实例的单线程访问性质。

* 单个执行组件实例一次只能处理一个请求。 如果需要处理并发请求，那么一个执行组件实例会导致出现吞吐量瓶颈。
* 如果两个执行组件之间存在一个循环请求，并且同时向其中一个执行组件发送外部请求，那么这两个执行组件就会发生互相死锁的情况。 执行组件运行时在执行组件调用上会自动超时，并向调用方抛出异常，以便中断可能出现的死锁情况。

![Reliable Actors 通信][3]

### <a name="turn-based-access"></a>基于轮次的访问
一个轮次包含完全执行用于响应其他执行组件或客户端的请求的执行组件方法，或完全执行[计时器/提醒](service-fabric-reliable-actors-timers-reminders.md)回叫。 即使这些方法和回调是异步的，执行组件运行时也不会交替执行它们。 必须彻底完成一个轮次，才允许启动新的轮次。 换而言之，在允许新的方法调用或回调之前必须彻底完成当前正在执行的执行组件方法或计时器/提醒回调。 如果执行已从方法或回调中返回，且由方法或回调返回的任务已完成，则将此方法或回调视为已完成。 值得强调的是，即使在不同的方法、计时器和回调中，也遵从基于轮次的并发执行。

通过在轮次的开始获取按执行组件锁并在轮次的结束释放锁，执行组件运行时强制执行基于轮次的并发。 因此，基于按执行组件基础而非所有执行组件强制执行基于轮次的并发。 执行组件方法和计时器/提醒回调可以代表不同执行组件同时执行。

以下示例对上述概念进行了说明。 假设一种类型的执行组件实现两个异步方法（即 *Method1* 和 *Method2*）、一个计时器和一个提醒。 下面的示例图展示了代表属于这种执行组件类型的两个执行组件（*ActorId1* 和 *ActorId2*）执行这些方法和回叫的时间线。

![Reliable Actors 运行时基于轮次的并发执行和访问][1]

该图遵循以下约定：

* 每条垂直线显示代表特定执行组件执行方法或回调的逻辑流。
* 每条垂直线上标记的事件按时间顺序发生，较新事件发生在较旧事件之后。
* 时间线上使用不同的颜色对应不同的执行组件。
* 突出显示用于指示代表方法或回调保持按执行组件锁的持续时间。

要重点考虑的几点：

* 当代表 *ActorId2* 执行 *Method1* 以响应客户端请求 *xyz789* 时，收到另一个客户端请求 (*abc123*)，也要求由 *ActorId2* 执行 *Method1*。 不过，在上一次执行完成之前，不会开始第二次执行 *Method1*。 同样，在执行 *Method1* 以响应客户端请求 *xyz789* 时，触发了 *ActorId2* 注册的提醒。 只有当这两次 *Method1* 执行都完成之后，才会执行提醒回叫。 一切都是因为，为 *ActorId2* 强制执行基于轮次的并发。
* 同样，也为 *ActorId1* 强制执行了基于轮次的并发，代表 *ActorId1* 以串行方式执行 *Method1*、*Method2* 和计时器回叫就表明了这一点。
* 代表 *ActorId1* 执行 *Method1* 与代表 *ActorId2* 执行此方法相互重叠。 这是因为仅在同一个执行组件内，而不是在不同执行组件之间强制执行基于轮次的并发执行。
* 在一些方法/回叫执行中，方法/回叫返回的 `Task`(C#) / `CompletableFuture`(Java) 在方法返回之后完成。 在另一些方法/回叫执行中，异步操作在方法/回叫返回时就已完成。 在这两种情况下，只有在方法/回叫返回且异步操作已完成后，才会解除每个执行组件锁定。

### <a name="reentrancy"></a>重新进入
执行组件运行时默认情况下允许重新进入。 也就是说，如果 *Actor A* 的执行组件方法调用 *Actor B* 上的方法，后者反过来又调用 *Actor A* 上的另一个方法，则允许运行另一个方法。 这是因为它是同一逻辑调用链上下文的一部分。 所有计时器和提醒调用都使用新的逻辑上下文开始。 如需了解更多详情，请参阅 [Reliable Actors 可重入性](service-fabric-reliable-actors-reentrancy.md)。

### <a name="scope-of-concurrency-guarantees"></a>并发保证的范围
执行组件运行时在它控制调用这些方法的情况下提供这些并发保证。 例如，它为响应客户端请求而执行的方法调用和计时器与提醒回调提供这些保证。 但是，如果执行组件代码在执行组件运行时提供的机制之外直接调用这些方法，则运行时不能提供任何并发保证。 例如，如果在与执行组件方法返回的任务不相关联的某项任务的上下文中调用方法，则运行时不能提供并发性保证。 如果通过执行组件依据其自身创建的线程调用方法，那么运行时也不能提供并发性保证。 因此，若要执行后台操作，执行组件应使用遵从基于轮次的并发的[执行组件计时器和执行组件提醒](service-fabric-reliable-actors-timers-reminders.md)。

## <a name="next-steps"></a>后续步骤
从生成第一个 Reliable Actors 服务开始：
   * [.NET 上的 Reliable Actors 入门](service-fabric-reliable-actors-get-started.md)
   * [Java 上的 Reliable Actors 入门](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
