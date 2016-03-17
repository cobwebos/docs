<properties
   pageTitle="Service Fabric 编程模型 | Microsoft Azure"
   description="Service Fabric 提供了两个框架用于构建服务：Actor 框架和 Services 框架。它们在简单性和控制度方面具有截然不同的利弊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="01/26/2016"
   wacn.date=""/>

# 为服务选择框架

Azure Service Fabric 提供了两个高级框架用于生成服务：Reliable Services API 和 Reliable Actors API。虽然这两个框架都基于同一 Service Fabric 核心，但在并发、分区和通信方面的简洁性与灵活性之间，它们所发挥的权衡作用各不相同。了解这两种模型非常有用，可确保能为你应用程序中的特定服务选择合适的框架。

## 比较 Reliable Actors API 和 Reliable Services API

|**何时选择 Reliable Actors API**|**何时选择 Reliable Services API**|
|-----------------------|--------------------------|
|问题空间涉及许多（1000 个以上）小型的独立状态和逻辑单元。|需要维护多个组件之间的逻辑。|
|你想要处理不需要大量外部交互的单线程对象。|你想要使用可靠集合（如 .NET 可靠字典和可靠队列）来存储和管理状态。|
|你希望平台为你管理通信。|你希望为服务管理通信并控制分区方案。|

记住，对应用内的不同服务使用不同的框架是完全合理的。例如，你可能具有一种有状态服务，它会聚合由大量执行组件生成的数据。

## 后续步骤

- [了解有关 Reliable Actors API 的详细信息](/documentation/articles/service-fabric-reliable-actors-introduction)
- [了解有关 Reliable Services API 的详细信息](/documentation/articles/service-fabric-reliable-services-introduction)

<!---HONumber=Mooncake_0307_2016-->