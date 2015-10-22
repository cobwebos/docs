<properties
   pageTitle="开发 Service Fabric 服务"
   description="帮助你了解如何使用 Reliable Actors 或 Reliable Services 编程模型开发 Service Fabric 服务的概念性信息和教程。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/20/2015"
   wacn.date=""/>

# 开发 Service Fabric 服务
此页具有指向概述和概念文章以及教程的链接，可帮助你了解如何开发 Service Fabric 服务。Service Fabric 提供了两个高级编程模型用于生成服务：Reliable Actors API 和 Reliable Services API。虽然这两个模型都基于同一 Service Fabric 核心，但在并发、分区和通信方面的简洁性与灵活性之间，它们所发挥的权衡作用各不相同。了解这两种模型非常有用，可确保能为你应用程序中的特定服务选择合适的框架。

- [选择编程模型](/documentation/articles/service-fabric-choose-framework)
- [Service Fabric Actor 模型简介](/documentation/articles/service-fabric-reliable-actors-introduction)
- [Reliable Service 编程模型简介](/documentation/articles/service-fabric-reliable-services-introduction)

## Reliable Actor 编程模型
 Reliable Actors 提供了单线程的异步 Actor 模型。Actor 代表分布在整个群集上以实现高可缩放性的状态和计算单元。Reliable Actor 模型利用基础 Service Fabric 平台提供的分布式存储区为应用程序开发人员提供高度可用且一致的状态管理。若要了解更多信息，请阅读以下文章：

- [Reliable Actors 入门 ](/documentation/articles/service-fabric-reliable-actors-get-started)
- [Actor 生命周期和垃圾回收](/documentation/articles/service-fabric-reliable-actors-lifecycle)
- [Fabric Actors 如何使用 Service Fabric 平台](/documentation/articles/service-fabric-reliable-actors-platform)
- [有关 Azure Service Fabric Actors 类型序列化的说明](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)

与 Actors 进行通信在以下文章中介绍：

- [ Service Fabric Actor 模型简介](/documentation/articles/service-fabric-reliable-actors-introduction.md#actor-communication)。
- [与服务通信](/documentation/articles/service-fabric-connect-and-communicate-with-services)

这些文章讨论了有用的设计模式和方案：

- [Actor 模型设计模式](/documentation/articles/service-fabric-reliable-actors-patterns-introduction)  
- [模式：智能缓存](/documentation/articles/service-fabric-reliable-actors-pattern-smart-cache)
- [模式：分布式网络和图形](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs)
- [模式：资源调控](/documentation/articles/service-fabric-reliable-actors-pattern-resource-governance)
- [模式：有状态服务组合](/documentation/articles/service-fabric-reliable-actors-pattern-stateful-service-composition)
- [模式：物联网](/documentation/articles/service-fabric-reliable-actors-pattern-internet-of-things)
- [模式：分布式计算](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)
- [若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns)

为 Reliable Actor 方法提供了基于轮次的简单并发。并发、计时器和提醒以及重新进入在以下文章中介绍：

- [并发](/documentation/articles/service-fabric-reliable-actors-introduction.md#concurrency)
- [与并发相关的事件和性能计数器](/documentation/articles/service-fabric-reliable-actors-diagnostics.md)
- [Actor 重新进入](/documentation/articles/service-fabric-reliable-actors-reentrancy)
- [Actor 计时器](/documentation/articles/service-fabric-reliable-actors-timers-reminders)

有关配置 Reliable Actors 的信息，可在以下文章中找到：

- [KVSActorStateProvider 配置](/documentation/articles/service-fabric-reliable-actors-KVSActorstateprovider-configuration)  
- [配置 Reliable Actors - ReliableDictionaryActorStateProvider](/documentation/articles/service-fabric-reliable-actors-reliabledictionarystateprovider-configuration)

Reliable Actors 发出事件和性能计数器，可用于诊断和监视你的服务：

- [Actor 诊断](/documentation/articles/service-fabric-reliable-actors-diagnostics)
- [Actor 事件](/documentation/articles/service-fabric-reliable-actors-events)


## Reliable Service 编程模型
Reliable Services 为你提供简单且功能强大的顶级编程模型，以帮助你表达对你的应用程序至关重要的内容。若要了解更多信息，请阅读以下文章：

- [Reliable Services 入门](/documentation/articles/service-fabric-reliable-services-quick-start)
- [编程模型概述](/documentation/articles/service-fabric-reliable-services-service-overview)  
- [体系结构](/documentation/articles/service-fabric-reliable-services-platform-architecture)
- [Reliable Collections](/documentation/articles/service-fabric-reliable-services-reliable-collections)
- [配置有状态的 Reliable Services](/documentation/articles/Service-Fabric/service-fabric-reliable-services-configuration)
- [Reliable Services 编程模型的高级用法](/documentation/articles/Service-Fabric/service-fabric-reliable-services-advanced-usage)

与 Reliable Services 的通信以及客户端可用于发现服务终结点并与之通信的抽象在以下文章中介绍：

- [与服务通信](/documentation/articles/service-fabric-connect-and-communicate-with-services)
- [服务通信模型](/documentation/articles/service-fabric-reliable-services-communication)
- [Reliable Services 框架提供的默认通信堆栈](/documentation/articles/service-fabric-reliable-services-communication-default)
- [Reliable Services 基于 WCF 的通信堆栈](/documentation/articles/service-fabric-reliable-services-communication-wcf)
- [带 OWIN 自托管的 Microsoft Azure Service Fabric Web API 服务入门 (VS 2015 RC)](/documentation/articles/service-fabric-reliable-services-communication-webapi)

Reliable Services 发出事件和性能计数器，可用于诊断和监视你的服务：

- [有状态的 Reliable Service 诊断](/documentation/articles/service-fabric-reliable-services-diagnostics)

<!---HONumber=74-->