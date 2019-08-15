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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67052597"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric 应用程序方案
Azure Service Fabric 提供了一个可靠而灵活的平台，可用于编写和运行多种类型的商业应用程序和服务。 这些应用程序和微服务可以无状态也可以有状态，它们在各虚拟机间资源平衡，可最大限度提高工作效率。 

Service Fabric 的独特体系结构使你可以在应用程序中执行近实时数据分析、内存中计算、并行事务和事件处理。 可以根据不断变化的资源要求轻松向上或向下缩放应用程序（其实是扩展或缩减）。

有关生成应用程序的设计指南, 请参阅[Azure Service Fabric 上的微服务体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)和[使用 Service Fabric 进行应用程序设计的最佳实践](service-fabric-best-practices-applications.md)。

对于以下类型的应用程序，请考虑使用 Service Fabric 平台：

* **数据收集、处理和 IoT**：Service Fabric 通过其有状态服务实现大规模处理和低延迟。 它可以帮助你处理数百万台设备上的数据（其中的设备数据和计算数据共置在一起）。

    已使用 Service Fabric 构建 IoT 服务的客户包括 [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)、[PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)、[Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)、[BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)、[Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric) 和 [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)。

* **游戏和基于会话的交互式应用程序**：如果应用程序需要低延迟读取和写入（例如在线游戏或即时消息），Service Fabric 非常有用。 通过 Service Fabric，可生成这些交互式有状态应用程序，而无需创建单独的存储或缓存。 有关[在游戏服务中使用 Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf) 的设计指导，请访问 [Azure 游戏解决方案](https://azure.microsoft.com/solutions/gaming/)。

    已构建游戏服务的客户包括 [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) 和 [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)。 已构建交互式会话的客户包括 [Honeywell with Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)。

* **数据分析和工作流处理**：必须可靠处理事件或数据流的应用程序可受益于 Service Fabric 中的优化读取和写入。 Service Fabric 还支持应用程序处理管道，其中的结果必须能够可靠地传递到下一个处理阶段而不会丢失。 这些管道包括交易和财务系统，其中的数据一致性和计算保证至关重要。

    已构建业务工作流服务的客户包括 [Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)、[Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric) 和 [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)。

* **数据计算**：使用 Service Fabric 可以构建有状态应用程序来执行密集的数据计算。 Service Fabric 允许在应用程序中共置处理（计算）和数据。 

   一般情况下，当应用程序需要访问数据时，与外部数据缓存或存储层相关的网络延迟会限制计算时间。 使用 Service Fabric 有状态服务可消除这种延迟，使读取和写入得到进一步的优化。 
   
   例如，假设某个应用程序要为客户执行准实时的建议选择，且要求往返时间小于 100 毫秒。 与必须从远程存储中提取所需数据的标准实现模型相比，Service Fabric 服务的延迟和性能特征向用户提供一种响应体验。 系统的响应能力更强，因为建议选择计算与数据和规则共置。

    已构建计算服务的客户包括 [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) 和 [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)。

* **高度可用的服务**：Service Fabric 通过创建多个辅助服务副本提供快速的故障转移。 节点、进程或单独的服务因硬件或其他故障而不可用时，其中一个辅助副本会提升为主副本，将对服务的损失降到最低。

* **可缩放的服务**：可对单独的服务进行分区，以允许在群集范围内扩大状态。 此外，还可动态创建和删除各项服务。 可将少量节点上的少量实例中的服务横向扩展为许多节点上的数千个实例，以后可根据需要再次缩减。 可以使用 Service Fabric 生成这些服务并管理其整个生命周期。

## <a name="application-design-case-studies"></a>应用程序设计案例研究
[客户案例](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/)和 [Azure 中的微服务](https://azure.microsoft.com/solutions/microservice-applications/)站点上发布了介绍如何使用 Service Fabric 设计应用程序的案例研究。

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>设计由无状态和有状态微服务组成的应用程序
构建使用 Azure 云服务辅助角色的应用程序是无状态服务的一个示例。 相之之下，除请求及其响应以外，有状态微服务还维护其权威状态。 此功能在复制支持下提供交易保证的简单 API，从而提供状态的高可用性和一致性。 

Service Fabric 中的有状态服务可让所有类型的应用程序（而不仅仅是数据库和其他数据存储）实现高可用性。 这是顺其自然的进步。 针对高可用性，应用程序已从使用单纯的关系数据库发展到 NoSQL 数据库的境界。 现在，应用程序可在自身内部管理其“热”状态和数据，以便进一步提高性能，而无需损失可靠性、一致性或可用性。

构建包含微服务的应用程序时，通常有一个无状态 Web 应用（例如 ASP.NET 和 Node.js）组合调用应用于无状态和有状态业务中间层服务。 这些应用和服务全部通过 Service Fabric 部署命令部署到同一个 Service Fabric 群集。 在规模、可靠性和资源使用方面，其中的每个服务都是独立的。 这种独立性大大提高了开发和生命周期管理的敏捷性与灵活性。

有状态微服务将简化应用程序设计，因为它们不再需要传统上需要用来处理纯无状态应用程序的可用性和延迟需求的附加队列和缓存。 由于有状态服务具有高度可用和低延迟的特点，要在应用程序中管理的详细信息更少。 

下图演示了设计有状态应用程序与无状态应用程序之间的差异。 通过利用 [Reliable Services](service-fabric-reliable-services-introduction.md) 和 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 编程模型，有状态服务降低了应用程序的复杂性，同时实现了高吞吐量和低延迟。

下面是使用无状态服务的示例应用程序:![使用无状态服务的应用程序][Image1]

下面是使用有状态服务的示例应用程序:![使用无状态服务的应用程序][Image2]

## <a name="next-steps"></a>后续步骤

* 详细了解[模式和方案](service-fabric-patterns-and-scenarios.md)。

* 使用 Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) 和 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 编程模型开始构建无状态和有状态服务。
* 请访问 Azure 体系结构中心, 获取有关在[Azure 上构建微服务](https://docs.microsoft.com/azure/architecture/microservices/)的指导。
* 有关应用程序设计指导，请参阅 [Azure Service Fabric 应用程序和群集最佳做法](service-fabric-best-practices-overview.md)。

* 此外，请参阅以下主题：
  * [介绍微服务](service-fabric-overview-microservices.md)
  * [定义和管理服务状态](service-fabric-concepts-state.md)
  * [Service Fabric 服务的可用性](service-fabric-availability-services.md)
  * [缩放 Service Fabric 服务](service-fabric-concepts-scalability.md)
  * [对 Service Fabric 服务进行分区](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
