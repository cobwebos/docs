---
title: Azure Service Fabric 应用程序设计最佳做法 |Microsoft Docs
description: 有关开发 Service Fabric 应用程序的最佳实践。
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203460"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 应用程序设计最佳做法

本文提供了用于在 Azure Service Fabric 上构建应用程序和服务的最佳实践指南。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉如何使用 Service Fabric
* 读取[因此，你想要了解 Service Fabric？](service-fabric-content-roadmap.md)一文。
* 阅读有关[Service Fabric 应用程序方案](service-fabric-application-scenarios.md)。
* 通过阅读了解编程模型选择[Service Fabric 编程模型概述](service-fabric-choose-framework.md)。



## <a name="application-design-guidance"></a>应用程序设计指南
熟悉[一般体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)的 Service Fabric 应用程序和其[设计注意事项](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>选择 API 网关
使用 API 网关服务，则可以扩大的后端服务进行通信。使用的最常见的 API 网关服务包括：

- [Azure API 管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)，即[与 Service Fabric 集成](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)。
- [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)，并使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)从事件中心分区读取。
- [Træfik 反向代理](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)，并使用[Azure Service Fabric 提供程序](https://docs.traefik.io/configuration/backends/servicefabric/)。
- [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)。

   > [!NOTE] 
   > Azure 应用程序网关没有直接集成到 Service Fabric。 Azure API 管理通常是理想之选。
- 您自己自定义构建[ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web 应用程序网关。

### <a name="stateless-services"></a>无状态服务
我们建议您始终首先通过使用构建无状态服务[Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)和 Azure 数据库、 Azure Cosmos DB 或 Azure 存储中存储状态。 外部化的状态是大多数开发人员更熟悉的方法。 此方法还可以利用在存储区上的查询功能。  

### <a name="when-to-use-stateful-services"></a>何时使用有状态服务
如果你有实现低延迟方案并需要保留的数据靠近计算，请考虑有状态服务。 一些示例方案包括 IoT 数字孪生设备、 游戏状态、 会话状态中，缓存数据从一个数据库和长时间运行的工作流来跟踪对其他服务的调用。

确定数据保留时间范围：

- **缓存的数据**。 使用缓存延迟到外部存储时出现问题。 使用有状态服务作为您自己的数据缓存，或考虑使用[开放源代码 SoCreate Service Fabric 分布式缓存](https://github.com/SoCreate/service-fabric-distributed-cache)。 在此方案中，您不需要担心如果丢失缓存中的所有数据。
- **时间限制数据**。 在此方案中，您需要保持关闭数据来计算一段时间延迟，但您可以承受丢失中的数据*灾难*。 例如，在许多 IoT 解决方案中，数据必须是关闭来计算，例如当正在计算温度平均值在过去的几天内，但如果此数据丢失，则记录的特定数据点不重要。 此外，在此方案中您通常不在乎备份单个数据点。 你只能备份定期写入到外部存储的计算平均值。  
- **长期数据**。 Reliable collections 可以永久存储数据。 但在这种情况下需要[做好灾难恢复](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)，其中包括[配置定期备份策略](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup)群集。 实际上，您配置会发生什么情况在发生灾难销毁群集，需要创建新的群集，以及如何部署新应用程序实例和从最新备份中恢复。

节省成本并提高可用性：
- 可以从远程存储，降低成本的使用有状态服务，因为不会产生数据访问和事务成本，并因为无需使用另一个服务，如 Azure 缓存的 Redis。
- 使用有状态服务，主要用于存储而不是针对计算成本很高，并且我们不建议这样做。 有状态服务看作具有本地存储比较便宜的计算。
- 通过删除其他服务上的依赖项，可以提高服务可用性。 管理与 HA 群集中的状态隔离来自其他服务停机或延迟问题。

## <a name="how-to-work-with-reliable-services"></a>如何使用 Reliable Services
Service Fabric Reliable Services 可以轻松地创建无状态和有状态服务。 有关详细信息，请参阅[Reliable Services 简介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)。
- 始终遵循[取消标记](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps)中`RunAsync()`无状态和有状态服务的方法和`ChangeRole()`对于有状态服务的方法。 如果不这样做，Service Fabric 并不知道如果可以关闭你的服务。 例如，如果不遵循取消令牌，可能会发生更长的时间的应用程序升级时间。
-   打开和关闭[通信侦听器](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication)及时的方式，并遵守取消标记。
-   永远不会混合使用同步代码和异步代码。 例如，不要使用`.GetAwaiter().GetResult()`中异步调用。 使用异步*一直*通过调用堆栈。

## <a name="how-to-work-with-reliable-actors"></a>如何使用 Reliable Actors
Service Fabric Reliable Actors，可轻松地创建有状态、 虚拟执行组件。 有关详细信息，请参阅[Reliable Actors 简介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)。

- 认真地考虑使用 pub/sub 消息之间缩放你的应用程序的执行组件。 提供此服务的工具包括[开放源代码 SoCreate 服务 Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/)并[Azure 服务总线](https://docs.microsoft.com/azure/service-bus/)。
- 使参与者状态作为[尽可能细粒度](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。
- 管理[执行组件的生命周期](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。 如果您不打算再次使用它们，请删除执行组件。 在使用时，删除不需要执行组件则尤其[易失性状态提供程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)，因为所有的状态存储在内存中。
- 由于其[基于轮次的并发](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)，actors 最好用作独立的对象。 不要创建多参与者、 同步方法调用 （每个最有可能成为单独的网络调用） 的关系图或创建循环的执行组件请求。 这些将明显影响性能和规模。
- 不要混合同步与异步代码的代码。 一致地使用异步来防止出现性能问题。
- 不要在执行组件进行长时间运行的调用。 长时间运行的调用将阻止其他对相同的参与者，由于基于轮次的并发调用。
- 如果正在使用与其他服务通信[Service Fabric 远程处理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting)，并且正在创建`ServiceProxyFactory`，创建在工厂[执行组件服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using)级别和*不*在执行组件级别。


## <a name="application-diagnostics"></a>应用程序诊断
有关添加彻底[应用程序日志记录](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)服务调用。 它将帮助您诊断服务调用每个其他的方案。 例如，当调用 B 调用 C 调用 D 时，调用任何位置可能失败。 如果你没有足够的日志记录，很难诊断故障。 如果服务登录由于调用卷太多，请确保至少记录错误和警告。

## <a name="iot-and-messaging-applications"></a>IoT 和消息传递应用程序
当您在阅读来自邮件[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)，使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)。 与 Service Fabric Reliable Services 中保留从事件中心读取的状态进行分区，以及将新消息推送到你的服务通过集成 ServiceFabricProcessor`IEventProcessor::ProcessEventsAsync()`方法。


## <a name="design-guidance-on-azure"></a>在 Azure 上设计指南
* 请访问[Azure 体系结构中心](https://docs.microsoft.com/azure/architecture/microservices/)有关设计指南[在 Azure 上构建微服务](https://docs.microsoft.com/azure/architecture/microservices/)。

* 请访问[开始使用 Azure 游戏](https://docs.microsoft.com/gaming/azure/)有关设计指南[游戏服务中使用 Service Fabric](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)。
