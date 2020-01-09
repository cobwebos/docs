---
title: Azure Service Fabric 应用程序设计最佳实践
description: 使用 Azure Service Fabric 开发应用程序和服务的最佳做法和设计注意事项。
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 755e3c1eb649bc6c8ecc084d18e9904cc90b1282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551839"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 应用程序设计最佳实践

本文提供了有关在 Azure Service Fabric 上构建应用程序和服务的最佳实践指南。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉 Service Fabric
* 阅读，[以了解 Service Fabric？](service-fabric-content-roadmap.md)一文。
* 了解[Service Fabric 应用程序方案](service-fabric-application-scenarios.md)。
* 阅读[Service Fabric 编程模型概述](service-fabric-choose-framework.md)，了解编程模型选项。



## <a name="application-design-guidance"></a>应用程序设计指南
熟悉 Service Fabric 应用程序的[通用体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)及其[设计注意事项](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>选择 API 网关
使用与后端服务通信的 API 网关服务，后者可以横向扩展。最常见的 API 网关服务是：

- [与 Service Fabric 集成](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)的[Azure API 管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)。
- [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或[azure 事件](https://docs.microsoft.com/azure/event-hubs/)中心，使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)从事件中心分区读取。
- [Træfik 反向代理](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)，使用[Azure Service Fabric 提供程序](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/)。
- [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)。

   > [!NOTE] 
   > Azure 应用程序网关不会直接与 Service Fabric 集成。 Azure API 管理通常是首选选项。
- 你自己的自定义构建[ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web 应用程序网关。

### <a name="stateless-services"></a>无状态服务
建议你始终通过使用[Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)并将状态存储在 azure 数据库、Azure Cosmos DB 或 azure 存储中来构建无状态服务。 对于大多数开发人员而言，外部化状态是更为熟悉的方法。 此方法还使您能够利用应用商店上的查询功能。  

### <a name="when-to-use-stateful-services"></a>何时使用有状态服务
当你有一种低延迟的方案并且需要使数据接近计算空间时，请考虑有状态服务。 一些示例方案包括： IoT 数字克隆设备、游戏状态、会话状态、缓存来自数据库的数据和长时间运行的工作流，以跟踪对其他服务的调用。

确定数据保留时间范围：

- **缓存的数据**。 在延迟到外部存储存在问题时使用缓存。 使用有状态服务作为自己的数据缓存，或考虑使用[开源 SoCreate Service Fabric 分布式缓存](https://github.com/SoCreate/service-fabric-distributed-cache)。 在这种情况下，如果丢失缓存中的所有数据，则无需担心。
- **时间绑定数据**。 在这种情况下，你需要在一段时间内使数据接近计算，以实现延迟，但你可以承受*灾难*中丢失数据的情况。 例如，在许多 IoT 解决方案中，数据需要接近计算能力，如计算过去几天的平均温度时，但如果此数据丢失，则记录的特定数据点并不重要。 此外，在这种情况下，通常不会关心如何备份各个数据点。 只备份定期写入外部存储的计算平均值。  
- 长期**数据**。 可靠集合可以永久存储数据。 但在这种情况下，你需要[准备进行灾难恢复](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)，包括为群集[配置定期备份策略](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup)。 实际上，你可以配置在灾难中销毁群集时所发生的情况，需要创建新的群集，以及如何部署新的应用程序实例和从最新备份中恢复。

节省成本并提高可用性：
- 可以通过使用有状态服务降低成本，因为无需使用远程存储中的数据访问和事务成本，也无需使用其他服务（如 Azure Cache for Redis）。
- 使用主要用于存储的有状态服务，而不是计算成本高昂，不建议这样做。 将有状态服务视为具有低价本地存储的计算。
- 通过删除其他服务的依赖项，可以提高服务可用性。 在群集中管理 HA 的状态可将你与其他服务停机时间或延迟问题隔离开来。

## <a name="how-to-work-with-reliable-services"></a>如何使用 Reliable Services
利用 Service Fabric Reliable Services，你可以轻松地创建无状态服务和有状态服务。 有关详细信息，请参阅[Reliable Services 简介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)。
- 对于无状态服务和有状态服务，始终服从 `RunAsync()` 方法中的[取消标记](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps)和有状态服务的 `ChangeRole()` 方法。 否则，Service Fabric 不知道您的服务是否可以关闭。 例如，如果不接受取消标记，则可能会出现应用程序升级时间较长的情况。
-   及时打开并关闭[通信侦听器](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication)，并服从取消标记。
-   不要将同步代码与异步代码混合使用。 例如，不要在异步调用中使用 `.GetAwaiter().GetResult()`。 通过调用堆栈*始终*使用 async。

## <a name="how-to-work-with-reliable-actors"></a>如何使用 Reliable Actors
利用 Service Fabric Reliable Actors，你可以轻松创建有状态的虚拟参与者。 有关详细信息，请参阅[Reliable Actors 简介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)。

- 请认真考虑使用参与者之间的发布/订阅消息来缩放应用程序。 提供此服务的工具包括[开源 SoCreate Service Fabric 发布/订阅](https://service-fabric-pub-sub.socreate.it/)和[Azure 服务总线](https://docs.microsoft.com/azure/service-bus/)。
- 尽可能[细化](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)执行执行组件状态。
- 管理执行组件[的生命周期](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。 如果不打算再次使用，则删除执行组件。 当你使用[可变状态提供程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)时，删除不需要的执行组件尤其重要，因为所有状态都存储在内存中。
- 由于它们的[并发性](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)，使用者最好用作独立的对象。 请勿创建多执行组件、同步方法调用（其中每个操作最有可能成为单独的网络调用）的关系图或创建循环参与者请求。 这将明显影响性能和规模。
- 不要使用 async code 混合同步代码。 始终使用 async 来防止性能问题。
- 不要在执行组件中进行长时间运行的调用。 由于基于转换的并发，长时间运行的调用会阻止对同一执行组件的其他调用。
- 如果要使用[Service Fabric 远程处理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting)与其他服务进行通信，并创建 `ServiceProxyFactory`，请在执行组件[服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using)级别创建工厂，而*不*是在执行组件级别进行。


## <a name="application-diagnostics"></a>应用程序诊断
详细了解如何在服务调用中添加[应用程序日志记录](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)。 它将帮助你诊断服务相互调用的情况。 例如，当调用 B 调用 C 调用 D 时，调用可能会失败。 如果没有足够的日志记录，则故障很难诊断。 如果服务由于呼叫量而导致日志记录过多，请确保至少记录错误和警告。

## <a name="iot-and-messaging-applications"></a>IoT 和消息应用程序
从[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或[azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)读取消息时，请使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)。 ServiceFabricProcessor 与 Service Fabric Reliable Services 集成，以维护从事件中心分区读取的状态，并通过 `IEventProcessor::ProcessEventsAsync()` 方法将新消息推送到你的服务。


## <a name="design-guidance-on-azure"></a>Azure 设计指南
* 有关在[azure 上构建微服务](https://docs.microsoft.com/azure/architecture/microservices/)的设计指南，请访问[azure 体系结构中心](https://docs.microsoft.com/azure/architecture/microservices/)。

* 有关在[游戏服务中使用 Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的设计指南，请访问[Azure 入门](https://docs.microsoft.com/gaming/azure/)。
