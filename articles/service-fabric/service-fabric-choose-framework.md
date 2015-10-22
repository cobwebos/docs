<properties
   pageTitle="选择框架"
   description="Service Fabric 提供了两个高级框架用于生成服务：Actor 框架和 Services 框架。了解每种框架的价值有助于为你的应用程序做出正确的体系结构决策。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/09/2015"
   wacn.date=""/>

# 为服务选择框架

Service Fabric 提供了两个高级框架用于生成服务：Reliable Actors API 和 Reliable Services API。虽然这两个框架都基于同一 Service Fabric 核心，但在并发、分区和通信方面的简洁性与灵活性之间，它们所发挥的权衡作用各不相同。了解这两种模型非常有用，可确保能为你应用程序中的特定服务选择合适的框架。

## 比较 Reliable Actors API 和 Reliable Services API

|**Reliable Actors API**|**Reliable Services API**|
|-----------------------|--------------------------|
|问题空间涉及许多小型的独立状态和逻辑单元|需要维护多个组件之间的逻辑|
|希望处理单线程对象，同时仍能够进行缩放并保持一致性|希望使用可靠的集合（如 .NET 字典和队列）来存储和管理你的状态|
|希望使用框架来管理状态的并发性和粒度|希望控制状态的粒度和并发性|
|希望使用平台来管理你的通信|希望为服务管理通信并控制分区方案|

记住，对应用内的不同服务使用不同的框架是完全合理的。例如，你可能具有一种有状态服务，它会聚合由大量 Actor 生成的数据。

## 后续步骤

- [了解有关 Reliable Actors API 的详细信息](documentation/articles/service-fabric-reliable-actors-introduction)
- [了解有关 Reliable Services API 的详细信息](documentation/articles/service-fabric-reliable-services-introduction)

<!---HONumber=74-->