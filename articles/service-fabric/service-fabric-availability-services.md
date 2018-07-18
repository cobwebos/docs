---
title: Service Fabric 服务的可用性 | Microsoft 文档
description: 介绍服务的故障检测、故障转移和恢复
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0794c0e190ecbc4cce808e94f98bb0ac63d1075a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206243"
---
# <a name="availability-of-service-fabric-services"></a>Service Fabric 服务的可用性
本文概述 Azure Service Fabric 服务如何保持服务的可用性。

## <a name="availability-of-service-fabric-stateless-services"></a>Service Fabric 无状态服务的可用性
Service Fabric 服务可以是有状态服务，也可以是无状态服务。 无状态服务是不具备需要具有高可用性和可靠性的[本地状态](service-fabric-concepts-state.md)的应用程序服务。

创建无状态服务需要定义 `InstanceCount`。 实例计数定义应在群集中运行的无状态服务的应用程序逻辑的实例数。 建议通过增加实例数来扩大无状态服务。

无状态命名服务的实例发生故障时，都会在群集中某个符合条件的节点上创建一个新实例。 例如，无状态服务实例可能会在 Node1 上发生故障，但可以在 Node5 上进行重建。

## <a name="availability-of-service-fabric-stateful-services"></a>Service Fabric 有状态服务的可用性
有状态服务具备与之关联的状态。 在 Service Fabric 中，有状态服务均建模为副本集。 每个副本是服务的代码的运行实例。 此副本还拥有该服务的状态的副本。 在一个副本（以下称为“主副本”）上执行读取和写入操作。 因写入操作发生的状态更改会复制到副本集的其他副本（以下称为活动次要副本），并进行应用。 

只能有一个主要副本，但可以有多个活动次要副本。 可以配置活动辅助副本的数目，而副本数越多，可容忍的并发软件和硬件故障数越多。

如果主副本发生故障，Service Fabric 将指定一个活动辅助副本成为新的主副本。 此活动辅助副本具有最新的状态（通过复制）并且可以继续处理更多的读取/写入操作。 此过程称为“重新配置”，并在[重新配置](service-fabric-concepts-reconfiguration.md)一文中作了进一步介绍。

主副本和活动辅助副本的这一概念称为副本角色。 这些副本在[副本和实例](service-fabric-concepts-replica-lifecycle.md)一文中作了进一步介绍。 

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

- [缩放 Service Fabric 服务](service-fabric-concepts-scalability.md)
- [Service Fabric 服务分区](service-fabric-concepts-partitioning.md)
- [定义和管理状态](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

