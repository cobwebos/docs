---
title: Service Fabric 群集 Resource Manager 简介 | Microsoft 文档
description: 介绍 Service Fabric 群集 Resource Manager。
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f25a422385abfcdb7020eb7477c0ae2ee55cd8fb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210590"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Service Fabric 群集 Resource Manager 简介
在传统上，管理 IT 系统或联机服务意味着将特定物理机或虚拟机专用于这些特定的服务或系统。 服务构建为层级形式。 这些层级分为“ Web”层和“数据”（或“存储”）层。 应用程序会有消息传送层（请求在其中流入和流出）以及一组专用于缓存的计算机。 每个层级或每种类型的工作负荷都有特定的专用计算机：数据库需要一些专用计算机，Web 服务器也需要一些。 如果特定类型的工作负荷导致运行它的计算机运行温度过高，则可以向该层添加更多具有该相同配置的计算机。 但是，并非所有工作负荷都可以如此轻松地进行横向扩展 - 尤其是在数据层中，通常需要将计算机替换为更大的计算机。 这很容易理解。 如果某台计算机发生故障，则在还原该计算机之前，整个应用程序中的该部件以较低容量来运行。 这仍然很容易理解（但不一定有趣）。

然而，现在的服务和软件体系结构领域已发生改变。 应用程序采用横向扩展设计更为常见。 具有容器和/或微服务的应用程序的构建已较为普遍。 现在，虽然可能仍只具有几台计算机，但它们已不只是运行单个工作负荷实例。 它们甚至可以同时运行多个不同的工作负荷。 现在有多个不同类型的服务（没有一个服务需要占用整个计算机的资源），可能使用了这些服务的数百个不同实例。 每个命名实例都有一个或多个实例或副本用于高可用性 (HA)。 根据这些工作负荷的大小及其繁忙程度，可能需要数百至数千台计算机。 

突然间，管理环境并不像管理一些专用于单一类型工作负荷的计算机一样简单。 服务器是虚拟的且不再具有名称（毕竟现在要管理的是[一大堆](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)而不是几台计算机）。 有关计算机的配置减少了，有关服务本身的配置增多了。 专用于单个工作负荷实例的硬件大体上已过时。 服务本身已经变成小型分布式系统，跨越多个较小的商用硬件。

由于应用不再是一系列分布在多个层级的固化结构，因此现在就有更多的组合需要处理。 哪种因素决定了哪种类型的工作负荷可在特定的硬件上，或者可以运行多少个工作负荷？ 哪些工作负荷可在相同的硬件上运行得更好，哪些会发生冲突？ 计算机出现故障时，如何知道计算机上正在运行哪些程序？ 哪种机制负责确保该工作负荷可再次开始运行？ 是否正在等待（虚拟）计算机恢复正常，或者工作负荷自动故障转移到其他计算机并保持运行？ 是否需要人工干预？ 如何在此环境中升级？

作为在此环境中进行操作的开发人员和操作员，我们希望获取一些帮助来管理此复杂性。 大量招聘以及尝试通过配备人员来掩饰复杂性可能并非正确解答，那么我们应该怎么办？

## <a name="introducing-orchestrators"></a>协调器简介
“协调器”是软件片段中使用的一般术语，可帮助管理员管理这种类型的环境。 协调器是以类似于“我想要在环境中运行此服务的五个副本”的形式发出请求的组件。 无论发生什么情况，它们都尝试使环境匹配所需的状态。

协调器（不是人类）是当计算机故障或工作负荷出于某种意外原因而终止时，要采取措施的组件。 大多数协调器所做的操作不仅是处理故障。 其他功能包括：管理新部署、处理升级和处理资源消耗及治理。 从本质来说，所有协调器就是要维护环境中配置的某些所需状态。 可以将自己的预期告诉协调器，让它帮助你完成繁重的工作。 例如，位于 Mesos、Docker Datacenter/Docker Swarm、Kubernetes 和 Service Fabric 顶层的 Aurora 都是协调器。 开发人员正在积极开发这些协调器，以满足生产环境中的实际工作负荷需求。 

## <a name="orchestration-as-a-service"></a>协调即服务
群集资源管理器是在 Service Fabric 中处理业务流程的系统组件。 群集资源管理器的作业可划分为三个部分：

1. 强制实施规则
2. 优化环境
3. 帮助运行其他进程

若要了解群集 Resource Manager 的工作原理，请观看以下 Microsoft Virtual Academy 视频：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>它不是什么
在传统 N 层应用程序中，始终存在[负载均衡器](https://en.wikipedia.org/wiki/Load_balancing_(computing))。 通常这是网络负载均衡器 (NLB) 或应用程序负载均衡器 (ALB)，具体取决于它在网络堆栈中的位置。 有些负载均衡器基于硬件（例如 F5 的 BigIP 产品），有些则是基于软件（例如 Microsoft 的 NLB）。 在其他环境中，可能会在此角色中看到类似于 HAProxy、nginx、Istio 或 Envoy 的组件。 在这些体系结构中，负载均衡作业的目标是确保无状态工作负荷（大致） 接收相同的工作量。 均衡负载策略各不相同。 某些均衡器会向不同服务器发送每个不同调用。 其他均衡器则提供固定/粘性会话。 更高级的均衡器使用实际负载估计或报告来根据其预期的成本和当前计算机负载路由调用。

网络均衡器或消息路由器尝试确保 Web/辅助角色层大致保持均衡。 用于平衡数据层的策略有所不同，具体取决于数据存储机制。 数据层的平衡依赖于数据分区、缓存、托管视图、存储过程和其他特定于存储的机制。

尽管其中有些策略很有作用，但 Service Fabric 群集 Resource Manager 的功能并不像网络负载均衡器或缓存一样。 网络负载均衡器通过在前端分散流量来确保前端均衡。 Service Fabric 群集资源管理器采用不同的策略。 从根本上说，Service Fabric 会将*服务*移到服务最适用且流量或负载应跟随的位置。 例如，它可能会将服务移至当前冷门的节点，因为那里的服务未在做很多工作。 这些节点可能冷门，因为已存在的服务已删除或移到其他位置。 再举一例，群集 Resource Manager 还可以将服务移出计算机。 可能是计算机将要升级，或由于其上运行的服务的资源消耗量达到峰值而超载。 或者，服务的资源需求可能已增加。 因此，此计算机上没有足够的资源来继续运行服务。 

由于群集资源管理器负责移动服务，因此它提供一个不同于网络负载均衡器的功能集。 这是因为，网络负载均衡器将网络流量传送到服务所在位置，即使这个位置并不适合运行该服务。 Service Fabric 群集资源管理器使用本质上不同的策略来确保可以高效利用群集中的资源。

## <a name="next-steps"></a>后续步骤
- 有关群集 Resource Manager 中的体系结构和信息流的信息，请查看[此文](service-fabric-cluster-resource-manager-architecture.md)
- 群集 Resource Manager 提供许多用于描述群集的选项。 若要详细了解这些指标，请查看这篇[描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章
- 有关服务配置的详细信息，请参阅[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 若要详细了解指标及其配置方式，请查看[本文](service-fabric-cluster-resource-manager-metrics.md)
- 群集 Resource Manager 可与 Service Fabric 的管理功能配合使用。 若要了解有关这种集成的详细信息，请阅读[此文](service-fabric-cluster-resource-manager-management-integration.md)
- 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
