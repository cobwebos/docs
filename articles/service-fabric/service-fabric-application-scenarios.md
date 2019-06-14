---
title: 应用程序方案和设计 | Microsoft 文档
description: Service Fabric 中云应用程序的类别概述。 介绍使用有状态服务和无状态服务的应用程序设计。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052597"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric 应用程序方案
Azure Service Fabric 提供了可靠而灵活的平台，可以编写和运行多种类型的业务应用程序和服务。 这些应用程序和微服务可以是无状态或有状态的并且它们资源平衡跨虚拟机，以最大限度地提高效率。 

Service Fabric 的独特体系结构使你可以在应用程序中执行近实时数据分析、内存中计算、并行事务和事件处理。 可以根据不断变化的资源要求轻松向上或向下缩放应用程序（其实是扩展或缩减）。

有关构建应用程序设计指南，请阅读[上 Azure Service Fabric 的微服务体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)并[使用 Service Fabric 应用程序设计的最佳做法](service-fabric-best-practices-applications.md)。

请考虑对以下类型的应用程序使用 Service Fabric 平台：

* **数据收集、 处理和 IoT**:Service Fabric 处理大规模和具有通过其有状态服务的低延迟。 它可以帮助数以百万计的设备和计算的数据归置的设备上处理数据。

    通过使用 Service Fabric 构建 IoT 服务的客户包括[Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)， [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)， [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)， [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)， [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)，并[网格系统](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)。

* **游戏和基于会话的交互式应用程序**:Service Fabric 非常有用，如果应用程序需要低延迟读取和写入操作，例如在线游戏或即时消息中。 Service Fabric 使你能够生成这些交互式的有状态应用程序而无需创建单独的存储或缓存。 请访问[Azure 游戏解决方案](https://azure.microsoft.com/solutions/gaming/)有关设计指南[游戏服务中使用 Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)。

    构建游戏服务的客户包括[Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure)并[Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)。 已生成的交互式会话的客户包括[Honeywell 与 Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)。

* **数据分析和工作流处理**:必须可靠地处理事件或数据流的数据受益于优化的读取和写入在 Service Fabric 中的应用程序。 Service Fabric 还支持应用程序处理管道，其中结果必须可靠地传递到下一处理阶段不会丢失任何。 这些管道包含交易和财务系统，数据一致性和计算保证至关重要。

    构建业务工作流服务的客户包括[Zeiss 组](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)，[仲裁商业解决方案](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)，并[Société 常规](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)。

* **数据计算**:Service Fabric 使你能够构建有状态应用程序执行密集型数据计算。 Service Fabric 允许在应用程序中共置处理 （计算） 和数据。 

   通常情况下，当你的应用程序需要访问数据，与外部数据缓存或存储层相关联的网络延迟将限制计算时间。 有状态 Service Fabric 服务消除该延迟，启用进一步优化读取和写入。 
   
   例如，请考虑执行近实时建议选择小于 100 毫秒的往返时间要求的客户，应用程序。 Service Fabric 服务的延迟和性能特征向用户，无需从远程存储中提取所需的数据的标准实现模型相比提供一种响应体验。 系统速度更快，因为的建议选择计算与数据和规则共置。

    已生成计算服务的客户包括[Solidsoft 答复](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals)并[Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)。

* **高度可用的服务**：Service Fabric 通过创建多个辅助服务副本提供快速的故障转移。 节点、进程或单独的服务因硬件或其他故障而不可用时，其中一个辅助副本会提升为主副本，将对服务的损失降到最低。

* **可缩放的服务**：可对单独的服务进行分区，以允许在群集范围内扩大状态。 单个服务还可以创建和删除动态。 可以横向扩展到数千个实例在多个节点上从几个节点上的几个实例的服务，并根据需要缩放逻辑再次缩放中对其。 Service Fabric 可用于构建这些服务和管理其完整的生命周期。

## <a name="application-design-case-studies"></a>应用程序设计案例研究
说明如何使用 Service Fabric 设计应用程序的案例研究已发布在[客户案例](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/)并[在 Azure 中的微服务](https://azure.microsoft.com/solutions/microservice-applications/)站点。

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>无状态和有状态微服务设计的应用程序组成
使用 Azure 云服务辅助角色构建应用程序是无状态服务的一个示例。 相之之下，除请求及其响应以外，有状态微服务还维护其权威状态。 此功能可提供高可用性和一致性状态的简单 Api 提供支持的复制的事务保证。 

在 Service Fabric 有状态服务将对所有类型的应用程序，而不仅仅是数据库和其他数据存储的高可用性。 这是顺其自然的进步。 针对高可用性，应用程序已从使用单纯的关系数据库发展到 NoSQL 数据库的境界。 现在，应用程序可在自身内部管理其“热”状态和数据，以便进一步提高性能，而无需损失可靠性、一致性或可用性。

当您要构建包含微服务的应用程序时，通常具有的无状态 web 应用 （如 ASP.NET 和 Node.js） 组合调用无状态和有状态业务中间层服务。 应用程序部署和服务的所有 Service Fabric 部署命令通过在同一 Service Fabric 群集中。 每个服务是独立缩放、 可靠性和资源使用情况方面。 这种独立性提高了灵活性和灵活地开发和生命周期管理。

有状态微服务将简化应用程序设计，因为它们不再需要传统上需要用来处理纯无状态应用程序的可用性和延迟需求的附加队列和缓存。 由于有状态服务具有高可用性和低延迟，有更少详细信息来管理应用程序中。 

下图显示了设计的应用程序是无状态和有状态之间的差异。 通过利用 [Reliable Services](service-fabric-reliable-services-introduction.md) 和 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 编程模型，有状态服务降低了应用程序的复杂性，同时实现了高吞吐量和低延迟。

下面是使用无状态服务的示例应用程序：![使用无状态服务应用程序][Image1]

下面是使用有状态服务的示例应用程序：![使用无状态服务应用程序][Image2]

## <a name="next-steps"></a>后续步骤

* 详细了解如何[模式和方案](service-fabric-patterns-and-scenarios.md)。

* 开始构建无状态和有状态服务与 Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md)并[Reliable Actors](service-fabric-reliable-actors-get-started.md)编程模型。
* 访问有关在 Azure 体系结构中心指南[在 Azure 上构建微服务](https://docs.microsoft.com/azure/architecture/microservices/)。
* 转到[Azure Service Fabric 应用程序和群集的最佳做法](service-fabric-best-practices-overview.md)有关应用程序设计指南。

* 此外，请参阅以下主题：
  * [介绍微服务](service-fabric-overview-microservices.md)
  * [定义和管理服务状态](service-fabric-concepts-state.md)
  * [Service Fabric 服务的可用性](service-fabric-availability-services.md)
  * [缩放 Service Fabric 服务](service-fabric-concepts-scalability.md)
  * [对 Service Fabric 服务进行分区](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
