---
title: "Service Fabric 群集资源管理器简介 | Microsoft 文档"
description: "介绍 Service Fabric 群集资源管理器。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: ec470466f006265af5c4ccfddeeba975e6e648b5
ms.lasthandoff: 01/07/2017


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Service Fabric 群集资源管理器简介
在传统上，管理 IT 系统或一组服务意味着要使一些物理机或虚拟机专用于这些特定的服务或系统。 许多主要服务划分为“Web”层和“数据”或“存储”层，此外可能还有其他一些专用组件，例如缓存。 其他类型的应用程序会有消息传递层，请求将在其中流入和流出。 在消息传送过程中，此层将连接到工作层进行所需的任何分析或转换。 每种类型的工作负荷都有特定的专用计算机：数据库需要一些专用计算机，Web 服务器也需要一些。 如果特定类型的工作负荷导致运行它的计算机运行温度过高，则可以添加更多具有该相同配置的计算机。 不过，在大多数情况下，会将几台计算机替换为更大的计算机。 这很容易理解。 如果某台计算机发生故障，则在还原该计算机之前，整个应用程序中的该部件将以较低容量来运行。 这仍然很容易理解（但不一定有趣）。

但是，我们现在假设你已发现有扩展的需要并采用了容器和/或微服务。 突然你会发现自己有数十、数百或数千台计算机。 你使用了多个不同类型的服务（没有一个服务使用要占用完整计算机的资源），可能使用了这些服务的数百个不同实例。 每个命名实例都有一个或多个实例或副本用于高可用性 (HA)。

突然间，管理环境并不像管理一些专用于单一类型工作负荷的计算机一样简单。 服务器是虚拟的且不再具有名称（毕竟*现在*要管理的是[一大堆](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)而不是几台计算机）。 有关计算机的配置减少了，有关服务本身的配置增多了。 专用硬件已是过去的产物，服务本身已经变成小型分布式系统，跨越多个较小的商用硬件。

由于这会解构以前的单体式分层应用并分解到在商用硬件上运行的独立服务，因此，现在有许多更高程度的组合需要处理。 哪种因素决定了哪种类型的工作负荷可在特定的硬件上，或者可以运行多少个工作负荷？ 哪些工作负荷可在相同的硬件上运行得更好，哪些会发生冲突？ 当一台计算机出现故障... 哪些程序仍在运行？ 哪种机制负责确保该工作负荷可再次开始运行？ 是否正在等待（虚拟）计算机恢复正常，或者工作负荷自动故障转移到其他计算机并保持运行？ 是否需要人工干预？ 如何在此环境中升级？

作为处理此问题的开发人员和操作员，我们将需要一些帮助以管理此复杂性。 你有一种感觉：大量招聘以及尝试使用人员来掩饰复杂性并非正确解答。

怎么办？

## <a name="introducing-orchestrators"></a>协调器简介
“协调器”是软件片段中使用的一般术语，可帮助管理员管理这种类型的环境。 协调器是以类似于“我想要在环境中运行此服务的五个副本”的形式发出请求的组件。 无论发生什么情况，它们都尝试使环境匹配所需的状态。

协调器（不是人类）是当计算机故障或工作负荷出于某种意外原因而终止时，要快速采取措施的组件。 大多数协调器所做的操作不仅是处理故障。 它们的其他功能包括：管理新部署、处理升级和处理资源消耗。 从本质来说，所有协调器就是要维护环境中配置的某些所需状态。 你可以将自己的预期告诉协调器，让它帮助你完成繁重的工作。 例如，位于 Mesos、Fleet、Docker Datacenter/Docker Swarm、Kubernetes 和 Service Fabric 顶层的 Chronos 或 Marathon 都是协调器（或者有内置的协调器）。 由于在不同类型的环境中管理现实部署的复杂性和条件不断加剧和变化，持续创建的协调器越来越多。

## <a name="orchestration-as-a-service"></a>协调即服务
在 Service Fabric 群集中，协调器的作业主要由群集资源管理器来处理。 Service Fabric 群集资源管理器是 Service Fabric 中的系统服务之一，将在每个群集中自动启动。 一般而言，群集资源管理器的作业可划分为三个部分：

1. 强制实施规则
2. 优化你的环境
3. 帮助运行其他进程

若要了解群集资源管理器的工作原理，请观看以下 Microsoft Virtual Academy 视频：<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>它不是什么
在传统 N 层应用程序中，始终存在“负载均衡器”的某个概念。 通常这是网络负载均衡器 (NLB) 或应用程序负载均衡器 (ALB)，具体取决于它在网络堆栈中的位置。 有些负载均衡器基于硬件（例如 F5 的 BigIP 产品），有些则是基于软件（例如 Microsoft 的 NLB）。 在其他环境中，可能会在此角色中看到类似于 HAProxy 或 nginx 的组件。 在这些体系结构中，负载均衡作业的目标是确保无状态工作负荷（大致） 接收相同的工作量。 策略平衡负载已变。 某些均衡器会向不同服务器发送每个不同调用。 其他均衡器则提供固定/粘性会话。 更高级的均衡器使用实际估计或报告根据其预期的成本和当前计算机负载路由调用。

网络均衡器或消息路由器尝试确保 Web/辅助角色层大致保持均衡。 平衡数据层的策略则不同并取决于数据存储机制，通常集中在数据分片、缓存、托管视图、存储过程和其他特定于存储的机制。

尽管其中有些策略很有作用，但 Service Fabric 群集资源管理器的功能并不像网络负载均衡器或缓存一样。 网络负载均衡器可通过将流量移到运行服务的位置来确保前端均衡。 Service Fabric 群集资源管理器采用不同的策略。 从根本上说，Service Fabric 会将*服务*移到服务最适用且流量或负载应跟随的位置。 例如，它可能会将服务移至当前冷门的节点，因为那里的服务未在做很多工作。 这些节点可能冷门，因为已存在的服务已删除或移到其他位置。 再举一例，群集资源管理器还可以将服务移出计算机。 可能是计算机将要升级或由于其上运行的服务的资源消耗量达到峰值而超载。

由于群集资源管理器负责移动服务（不提供到服务所在位置的网络流量），因此它提供一个不同于网络负载均衡器的功能集。 当我们更详细地查看时，我们会发现它使用根本不同的策略来确保可以高效利用群集中的硬件资源。

## <a name="next-steps"></a>后续步骤
* 有关群集资源管理器中的体系结构和信息流的信息，请查看[此文](service-fabric-cluster-resource-manager-architecture.md)
* 群集资源管理器提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇有关[描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章
* 有关可用于配置服务的其他选项的详细信息，请查看[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他群集资源管理器配置的相关主题
* 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 若要详细了解指标及其配置方式，请查看[此文](service-fabric-cluster-resource-manager-metrics.md)
* 群集资源管理器可与 Service Fabric 的管理功能配合使用。 若要了解有关这种集成的详细信息，请阅读[此文](service-fabric-cluster-resource-manager-management-integration.md)
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章

