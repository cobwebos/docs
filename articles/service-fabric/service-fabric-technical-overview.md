<properties
   pageTitle="Service Fabric 技术概述 | Microsoft Azure"
   description="Service Fabric 技术概述。介绍重要概念和体系结构。"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.date="02/09/2016"
   wacn.date=""/>

# Service Fabric 技术概述

Azure Service Fabric 是一个分布式系统平台，可以轻松地构建面向云的可缩放、低延迟、易于管理的可靠应用程序。这意味着你可以专注于自己的业务需要，并让 Service Fabric 负责确保你的应用程序始终可用并且可以扩展。

## 关键概念

**群集** - 一组通过网络连接在一起的虚拟机或物理计算机，应用程序实例部署在其中。群集可以扩展到成千上万台计算机。

**节点** - 群集中的一个可寻址单位。节点具有诸如位置属性和唯一 ID 等特征。节点可以通过运行 Fabric.exe 加入一个群集并与一个操作系统实例相关联。

**应用程序/应用程序类型** -（微）服务的集合。将应用程序类型看作是一个或多个服务类型的容器。请参阅[应用程序模型](/documentation/articles/service-fabric-application-model)一文以了解一个群集（本身由多个节点组成）如何能由多个应用程序类型组成。

**服务/服务类型** - 执行某种独立功能（它可以独立启动和运行）的代码和配置，例如，队列服务或数据库服务。一个应用程序类型可以包含一个或多个服务类型。有两种服务类型：

- 无状态服务：状态保留到外部存储空间，例如 Azure 数据库或 Azure 表存储的服务。如果此服务的一个实例在处于活动状态时其所在节点出现故障，则另一个节点上的另一个实例自动启动。

- 有状态服务：一种具有状态的服务，通过在群集中其他节点上的副本之间的复制实现可靠性。有状态服务具有一个主副本和多个辅助副本。如果此服务的一个副本在处于活动状态时其所在节点关闭，则另一个节点上的新副本将会启动。如果关闭的节点是主副本，则辅助副本将自动提升为新的主副本。

**应用程序实例**：在群集中可以创建一个应用程序类型的很多应用程序实例，每个实例有一个特定名称。可以独立于具有相同类型或不同类型的应用程序的其他实例，管理每个应用程序实例及实例的版本。此外，它们定义了资源和安全隔离。

**服务实例**：针对某个服务类型进行了实例化的代码。每个服务实例都具有以 `fabric:/` 开头的唯一名称，并且与某个特别命名的应用程序实例相关联。

**应用程序包**：为特定应用程序组合在一起的服务代码包和配置文件的集合。这些是部署的物理文件，并且采用简单的文件和文件夹格式布局。例如，某个电子邮件应用程序的应用程序包会包含一个队列服务包、一个前端服务包和一个数据库服务包。

**编程模型**：Service Fabric 提供两种编程模型来构建应用程序：

- Reliable Services：一个 API，它基于 `StatelessService` 和 `StatefulService` .NET 类构建无状态和有状态服务，并在 .NET 可靠集合（字典和队列）中存储状态。它们还可以插入各种通信堆栈，如 Web API 和 Windows Communication Foundation。此编程模型适合需要跨多个状态单元执行计算的应用程序。

- Reliable Actors：一个通过虚拟执行组件编程模型构建无状态和有状态对象的 API，适合含有多个独立状态单元和计算的应用程序。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤
若干了解有关 Service Fabric 的更多信息，请参阅：

- [应用程序模型](/documentation/articles/service-fabric-application-model)
- [应用程序方案](/documentation/articles/service-fabric-application-scenarios)
 

<!---HONumber=Mooncake_0321_2016-->