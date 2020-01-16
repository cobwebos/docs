---
title: 应用程序方案和设计
description: Service Fabric 中云应用程序的类别概述。 介绍使用有状态服务和无状态服务的应用程序设计。
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024739"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric 应用程序方案

Azure Service Fabric 提供了一个可靠而灵活的平台，可在其中编写和运行多种类型的业务应用程序和服务。 这些应用程序和微服务可以是无状态或有状态，并且它们在虚拟机之间进行了资源平衡，以最大程度地提高效率。

Service Fabric 的独特体系结构使你可以在应用程序中执行近实时数据分析、内存中计算、并行事务和事件处理。 你可以根据不断变化的资源要求轻松地放大或缩小应用程序。

有关生成应用程序的设计指南，请参阅[Azure Service Fabric 上的微服务体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)和[使用 Service Fabric 进行应用程序设计的最佳实践](service-fabric-best-practices-applications.md)。

请考虑对以下类型的应用程序使用 Service Fabric 平台：

* **数据收集、处理和 IoT**： Service Fabric 处理大规模，并通过其有状态服务实现低延迟。 它可以帮助处理数百万台设备上的数据，以及设备的数据和计算的位置。

    使用 Service Fabric 构建 IoT 服务的客户包括[Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)、 [PCL 构造](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)、 [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)、[宝马](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)、 [Schneider 电气](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)和[网格系统](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)。

* **游戏和基于会话的交互式应用程序**：如果你的应用程序需要低延迟读取和写入（例如在线游戏或即时消息），则 Service Fabric 很有用。 Service Fabric 使你能够生成这些交互式的有状态应用程序，而无需创建单独的存储或缓存。 请访问[Azure 游戏解决方案](https://azure.microsoft.com/solutions/gaming/)，了解有关在[游戏服务中使用 Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的设计指南。

    已构建游戏服务的客户包括[下一场游戏](https://customers.microsoft.com/story/next-games-media-telecommunications-azure)和[Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)。 已构建交互式会话的客户包括[使用 Hololens 的 Honeywell](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)。

* **数据分析和工作流处理**：必须可靠地处理事件或数据流的应用程序，这些应用程序在 Service Fabric 中经过优化的读取和写入。 Service Fabric 还支持应用程序处理管道，其中的结果必须可靠，并将其传递到下一个处理阶段而不会丢失任何损失。 这些管道包括事务性和财务系统，其中的数据一致性和计算保证至关重要。

    已生成业务工作流服务的客户包括[Zeiss 组](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)、[仲裁业务解决方案](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)和[Société一般](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)。

* **数据的计算**： Service Fabric 使你能够生成具有大量数据计算功能的有状态应用程序。 Service Fabric 允许在应用程序中进行归置处理（计算）和数据。 

   通常情况下，当应用程序需要访问数据时，与外部数据缓存或存储层关联的网络延迟会限制计算时间。 有状态 Service Fabric 服务可消除这种延迟，从而实现更优化的读取和写入。

   例如，假设某个应用程序为客户执行近乎实时的建议选择，且往返时间要求小于100毫秒。 Service Fabric services 的延迟和性能特征为用户提供了响应式体验，与必须从远程存储提取必需数据的标准实现模型相比。 系统的响应速度更快，因为建议选择的计算与数据和规则有关。

    已生成计算服务的客户包括[Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals)和[Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)。

* **高度可用的服务**： Service Fabric 通过创建多个辅助服务副本提供快速的故障转移。 节点、进程或单独的服务因硬件或其他故障而不可用时，其中一个辅助副本会提升为主副本，将对服务的损失降到最低。

* **可缩放的服务**：可对单独的服务进行分区，以允许在群集范围内扩大状态。 还可以动态创建和删除单个服务。 你可以从几个节点上的几个实例向外扩展到多个节点上的数千个实例，然后根据需要重新进行扩展。 您可以使用 Service Fabric 来生成这些服务并管理其整个生命周期。

## <a name="application-design-case-studies"></a>应用程序设计案例研究

演示如何使用 Service Fabric 来设计应用程序的案例研究在 Azure 站点的[客户案例](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc)和[微服务](https://azure.microsoft.com/solutions/microservice-applications/)上发布。

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>设计由无状态和有状态微服务组成的应用程序

使用 Azure 云服务辅助角色生成应用程序是无状态服务的一个示例。 相之之下，除请求及其响应以外，有状态微服务还维护其权威状态。 此功能通过简单的 Api 提供状态的高可用性和一致性，这些 Api 提供由复制支持的事务保证。

Service Fabric 中有状态服务可以为所有类型的应用程序提供高可用性，而不仅仅是数据库和其他数据存储。 这是顺其自然的进步。 针对高可用性，应用程序已从使用单纯的关系数据库发展到 NoSQL 数据库的境界。 现在，应用程序可在自身内部管理其“热”状态和数据，以便进一步提高性能，而无需损失可靠性、一致性或可用性。

构建由微服务组成的应用程序时，通常会将无状态 web 应用（例如 ASP.NET 和 node.js）组合到无状态和有状态业务中间层服务。 所有应用和服务都部署在同一 Service Fabric 群集中，通过 Service Fabric 部署命令。 其中每个服务都独立于规模、可靠性和资源使用情况。 此独立性提高了开发和生命周期管理的灵活性和灵活性。

有状态微服务将简化应用程序设计，因为它们不再需要传统上需要用来处理纯无状态应用程序的可用性和延迟需求的附加队列和缓存。 由于有状态服务具有高可用性和低延迟，因此在应用程序中管理的详细信息更少。

以下关系图说明了设计无状态应用程序与有状态的应用程序之间的差异。 通过利用 [Reliable Services](service-fabric-reliable-services-introduction.md) 和 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 编程模型，有状态服务降低了应用程序的复杂性，同时实现了高吞吐量和低延迟。

下面是使用无状态服务的示例应用程序：使用无状态服务的 ![应用程序][Image1]

下面是使用有状态服务的示例应用程序：使用有状态服务的 ![应用程序][Image2]

## <a name="next-steps"></a>后续步骤

* 开始生成无状态和有状态服务 Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md)和[Reliable Actors](service-fabric-reliable-actors-get-started.md)编程模型。
* 请访问 Azure 体系结构中心，获取有关在[Azure 上构建微服务](https://docs.microsoft.com/azure/architecture/microservices/)的指导。
* 有关应用程序设计指南，请参阅[Azure Service Fabric 应用程序和群集最佳实践](service-fabric-best-practices-overview.md)。

* 另请参阅：
  * [了解微服务](service-fabric-overview-microservices.md)
  * [定义和管理服务状态](service-fabric-concepts-state.md)
  * [服务的可用性](service-fabric-availability-services.md)
  * [缩放服务](service-fabric-concepts-scalability.md)
  * [分区服务](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
