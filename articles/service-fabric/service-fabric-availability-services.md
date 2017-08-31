---
title: "Service Fabric 服务的可用性 | Microsoft 文档"
description: "介绍服务的故障检测、故障转移和恢复"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: b0d4615a9b8ab566f69b27e4879b6e2d597b4990
ms.contentlocale: zh-cn
ms.lasthandoff: 08/19/2017

---

# <a name="availability-of-service-fabric-services"></a>Service Fabric 服务的可用性
本文概述 Service Fabric 服务如何保持服务的可用性。

## <a name="availability-of-service-fabric-stateless-services"></a>Service Fabric 无状态服务的可用性
Azure Service Fabric 服务可以是有状态服务，也可以是无状态服务。 无状态服务是不具备任何需要具有高可用性和可靠性的[本地状态](service-fabric-concepts-state.md)的应用程序服务。

创建无状态服务需要定义 `InstanceCount`。 实例计数定义应在群集中运行的无状态服务的应用程序逻辑的实例数。 建议通过增加实例数来扩大无状态服务。

无状态命名服务的实例发生故障时，都会在群集中某个符合条件的节点上创建一个新实例。 例如，无状态服务实例可能会在 Node1 上发生故障，但可以在 Node5 上进行重建。

## <a name="availability-of-service-fabric-stateful-services"></a>Service Fabric 有状态服务的可用性
有状态服务具备一些与之关联的状态。 在 Service Fabric 中，有状态服务均建模为副本集。 每个副本是服务代码的运行实例，其中还具有该服务的状态副本。 在一个副本（以下称为“主副本”）上执行读取和写入操作。 因写入操作发生的状态更改会复制到副本集的其他副本（以下称为活动次要副本），并进行应用。 

只能有一个主要副本，但可以有多个活动次要副本。 可以配置活动辅助副本的数目，而副本数越多，可容忍的并发软件和硬件故障数越多。

如果主副本发生故障，Service Fabric 将指定一个活动辅助副本成为新的主副本。 此活动辅助副本具有最新的状态（通过*复制*）并且可以继续处理更多的读取和写入操作。

主副本和活动辅助副本的这一概念称为副本角色。

### <a name="replica-roles"></a>副本角色
副本的角色可用于管理受该副本所管理状态的生命周期。 具有主副本角色的副本将为读取请求提供服务。 主副本角色还可以通过更新写入请求的状态并复制更改来处理所有写入请求。 这些更改将应用于副本集中的活动辅助副本。 活动辅助副本的工作是接收主副本已复制的状态更改并更新其状态视图。

> [!NOTE]
> 对开发人员而言，更高级别的编程模型（如 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 和 [Reliable Services](service-fabric-reliable-services-introduction.md)）可以隐藏副本角色的概念。 在执行组件中，角色的概念是不必要的，而在服务中，大多数情况下都得到了很大程度的简化。
>

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

- [缩放 Service Fabric 服务](service-fabric-concepts-scalability.md)
- [Service Fabric 服务分区](service-fabric-concepts-partitioning.md)
- [定义和管理状态](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

