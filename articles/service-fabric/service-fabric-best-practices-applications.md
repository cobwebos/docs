---
title: 有关 Azure Service Fabric 应用程序设计的最佳做法
description: 使用 Azure Service Fabric 开发应用程序和服务的最佳做法和设计注意事项。
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538492"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>有关 Azure Service Fabric 应用程序设计的最佳做法

本文提供有关在 Azure Service Fabric 上构建应用程序和服务的最佳做法指导。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉 Service Fabric
* 阅读[想要了解 Service Fabric？](service-fabric-content-roadmap.md)一文。
* 阅读 [Service Fabric 应用程序方案](service-fabric-application-scenarios.md)。
* 在 [Service Fabric 编程模型概述](service-fabric-choose-framework.md)中了解编程模型选项。



## <a name="application-design-guidance"></a>应用程序设计指导
熟悉 Service Fabric 应用程序的[一般体系结构](/azure/architecture/reference-architectures/microservices/service-fabric)及其[设计注意事项](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>选择 API 网关
使用一个 API 网关服务，该服务能够与以后可横向扩展的后端服务通信。最常用的 API 网关服务包括：

- [与 Service Fabric 集成](./service-fabric-tutorial-deploy-api-management.md)的 [Azure API 管理](./service-fabric-api-management-overview.md)。
- 使用 [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) 从事件中心分区读取数据的 [Azure IoT 中心](../iot-hub/index.yml)或 [Azure 事件中心](../event-hubs/index.yml)。
- 使用 [Azure Service Fabric 提供程序](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/)的 [Træfik 反向代理](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)。
- [Azure 应用程序网关](../application-gateway/index.yml)。

   > [!NOTE] 
   > Azure 应用程序网关不直接与 Service Fabric 集成。 通常首选 Azure API 管理。
- 自定义构建的 [ASP.NET Core](./service-fabric-reliable-services-communication-aspnetcore.md) Web 应用程序网关。

### <a name="stateless-services"></a>无状态服务
我们建议，一开始始终使用可在 Azure 数据库、Azure Cosmos DB 或 Azure 存储中存储状态的 [Reliable Services](./service-fabric-reliable-services-introduction.md) 构建无状态服务。 对于大多数开发人员而言，使状态外部化是他们更熟悉的方法。 此方法还可以利用存储查询功能。  

### <a name="when-to-use-stateful-services"></a>何时使用有状态服务
如果你的方案要求低延迟，并需要使数据靠近计算资源，请考虑使用有状态服务。 部分示例方案包括 IoT 数字孪生设备、游戏状态、会话状态、缓存数据库中的数据，以及长时间运行的用于跟踪对其他服务的调用的工作流。

确定数据保留期限：

- **缓存的数据**。 如果与外部存储之间的延迟是一个问题，请使用缓存。 将有状态服务用作自己的数据缓存，或考虑使用[开源的 SoCreate Service Fabric 分布式缓存](https://github.com/SoCreate/service-fabric-distributed-cache)。 在此方案中，无需担心是否会丢失缓存中的所有数据。
- **时间受限的数据**。 在此方案中，需要使数据靠近计算资源以减小某个时间段的延迟，但在发生灾难时，你能够承受得起丢失该数据所造成的影响。  例如，在许多 IoT 解决方案中，数据需要靠近计算资源（例如，计算过去几天的平均温度），但如果此数据丢失，则记录的特定数据点就不是那么重要。 此外，在此方案中，你通常不会关心单个数据点的备份。 你只需备份定期写入到外部存储的平均计算值。  
- **长期数据**。 可靠集合可以永久存储数据。 但是，在这种情况下，需要[为灾难恢复做好准备](./service-fabric-disaster-recovery.md)，包括为群集[配置定期备份策略](./service-fabric-backuprestoreservice-configure-periodic-backup.md)。 实际上，配置的设置涉及到群集在灾难中受损时要怎样做、在哪种情况下需要创建新的群集、如何部署新应用程序实例以及从最新的备份恢复。

节省成本并提高可用性：
- 可以使用有状态服务降低成本，因为从远程存储执行数据访问和事务不会产生成本，并且无需使用另一个服务（例如 Azure Redis 缓存）。
- 将有状态服务主要用于存储而不是计算的做法会造成很大的成本，我们不建议这样做。 请考虑将有状态服务用作本地存储成本低廉的计算资源。
- 消除与其他服务之间的依赖关系可以提高服务可用性。 使用群集中的 HA 管理状态可以免受其他服务停机或延迟问题造成的影响。

## <a name="how-to-work-with-reliable-services"></a>如何使用 Reliable Services
使用 Service Fabric Reliable Services 可以轻松创建无状态和有状态服务。 有关详细信息，请参阅 [Reliable Services 简介](./service-fabric-reliable-services-introduction.md)。
- 始终遵循 `RunAsync()` 方法（对于无状态和有状态服务）和 `ChangeRole()` 方法（对于有状态服务）中的[取消标记](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)。 否则，Service Fabric 不知道是否可以关闭你的服务。 例如，如果你不遵循取消标记，可能会导致应用程序升级时间大幅延长。
-    及时打开和关闭[通信侦听器](./service-fabric-reliable-services-communication.md)并遵循取消标记。
-    切勿将同步代码和异步代码混合使用。 例如，不要在异步调用中使用 `.GetAwaiter().GetResult()`。 在整个调用堆栈中始终使用异步调用。 

## <a name="how-to-work-with-reliable-actors"></a>如何使用 Reliable Actors
使用 Service Fabric Reliable Actors 可以轻松创建有状态的虚拟执行组件。 有关详细信息，请参阅 [Reliable Actors 简介](./service-fabric-reliable-actors-introduction.md)。

- 强烈建议考虑在执行组件之间使用 pub/sub 消息传送来缩放应用程序。 提供此服务的工具包括[开源的 SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) 和 [Azure 服务总线](/azure/service-bus/)。
- 使执行组件状态[尽量保持粒度](./service-fabric-reliable-actors-state-management.md#best-practices)。
- 管理[执行组件的生命周期](./service-fabric-reliable-actors-state-management.md#best-practices)。 如果你不再使用执行组件，请将其删除。 使用[易失性状态提供程序](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)时，删除不需要的执行组件尤为重要，因为所有状态存储在内存中。
- 最好是将执行组件用作独立的对象，因为它们采用[基于轮次的并发](./service-fabric-reliable-actors-introduction.md#concurrency)。 不要创建多执行组件同步方法调用（每个调用很有可能会成为独立的网络调用）的图或创建循环执行组件请求， 这会对性能和规模造成显著影响。
- 不要将同步代码和异步代码混合使用。 始终使用异步代码，以防止出现性能问题。
- 不在在执行组件中发出长时间运行的调用。 长时间运行的调用会阻止对同一执行组件发出其他调用，因为执行组件采用基于轮次的并发。
- 如果使用 [Service Fabric 远程处理](./service-fabric-reliable-services-communication-remoting.md)来与其他服务通信，并且你正在创建 `ServiceProxyFactory`，请在[执行组件服务](./service-fabric-reliable-actors-using.md)级别而不是执行组件级别创建工厂。 


## <a name="application-diagnostics"></a>应用程序诊断
在服务调用中添加[应用程序日志记录](./service-fabric-diagnostics-event-generation-app.md)时应该面面俱到。 日志记录有助于诊断服务相互调用的方案。 例如，如果 A 调用 B，B 调用 C，C 调用 D，则调用可能会在任何一个位置失败。 如果没有足够的日志，将难以诊断。 如果服务记录的日志过多（因为调用量很大），请确保至少记录错误和警告。

## <a name="iot-and-messaging-applications"></a>IoT 和消息传送应用程序
从 [Azure IoT 中心](../iot-hub/index.yml)或 [Azure 事件中心](../event-hubs/index.yml)读取消息时，请使用 [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor)。 ServiceFabricProcessor 与 Service Fabric Reliable Services 集成，可保留从事件中心分区读取消息的状态，并通过 `IEventProcessor::ProcessEventsAsync()` 方法将新消息推送到服务。


## <a name="design-guidance-on-azure"></a>Azure 设计指南
* 有关在[azure 上构建微服务](/azure/architecture/microservices/)的设计指南，请访问[azure 体系结构中心](/azure/architecture/microservices/)。

* 有关在[游戏服务中使用 Service Fabric](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)的设计指南，请访问[Azure 入门](/gaming/azure/)。
