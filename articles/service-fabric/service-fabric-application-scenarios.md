<properties 
   pageTitle="使用 Service Fabric 的应用程序方案和设计" 
   description="应用程序的类别。使用有状态和无状态服务的应用程序设计" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/21/2015"
   wacn.date=""/>

# Service Fabric 应用程序方案

Service Fabric 和 Azure 提供了一个可靠而灵活的云基础结构平台，使你能够运行多个类型的业务应用程序和服务。这些应用程序和微服务可以为无状态或有状态，并且它们在各虚拟机间的资源平衡，可最大限度提高工作效率。Service Fabric 的独特体系结构使你可以在应用程序中执行近实时数据分析、内存中计算、并行事务和事件处理。你可以根据不断变化的资源要求轻松地增减应用程序。

Azure 中的 Service Fabric 平台非常适合以下类别的应用程序和服务：

- **高度可用的服务**：Service Fabric 服务提供极快速的故障转移。Service Fabric 允许你创建多个辅助服务副本。节点、进程或单独的服务因硬件故障或其他故障而不可用时，其中一个辅助副本会立即提升为主副本，几乎不会对客户服务造成任何损失。 

- **可缩放的服务**：可对单独的服务进行分区，以允许在群集范围内扩大状态。此外，还可以动态创建并删除单独的服务。服务可以快速简单地从几个节点上的几个实例向扩大到多个节点上的数千个实例，然后立即再次减少，具体取决于你的资源需求。可以使用 Service Fabric 来生层这些服务并管理其整个生命周期。
 
- **非静态数据计算**：Service Fabric 使你能够生成数据、I/O 并计算密集型有状态应用程序。Service Fabric 允许在应用程序中共置处理（计算）和数据。如今，当你的应用程序需要访问数据时，通常会存在与外部数据缓存或存储层关联的网络延迟。使用有状态 Service Fabric 服务可消除该延迟，从而实现更高性能的读取和写入。例如，假设你有一个应用程序为要求往返时间小于 100 毫秒的客户执行近实时建议选择。与必须从远程存储中提取所需数据的标准实现模型相比，Service Fabric 服务（其中的建议选择计算与数据和规则共置）的延迟和性能特征向用户提供一种响应体验。
 
- **基于会话的交互式应用程序**：在你的应用程序（例如在线游戏或即时消息）需要低延迟读取和写入时，Service Fabric 非常有用。Service Fabric 使你能够生成这些交互式的有状态应用程序，而无需创建一个无状态应用所需的单独存储或缓存（增加延迟时间并可能产生一致性问题）。
 
- **分布式图形处理**：社交网络的增长大大增加了并行分析大型图形的需求。快速缩放和并行加载处理使 Service Fabric 成为用于处理大型图形的适宜平台。Service Fabric 使你可以针对各个组（如社交网络、商业智能和科学研究）生成高度可缩放的服务。
 
- **数据分析和工作流**：Service Fabric 的快速读/写使必须可靠处理事件或数据流的应用程序成为可能。Service Fabric 还可实现描述处理管道的应用程序，该应用程序中的结果必须可靠，然后无损传递到下一处理阶段（包括事务和财务系统），在此过程中，数据一致性和计算保证至关重要。

## 设计由无状态和有状态微服务组成的应用程序 ##
具有 Azure 云服务辅助角色的生成应用程序是无状态服务的一个示例。与此相反，有状态微服务将保持请求外的权威状态以及其响应，通过提供由复制支持的事务保证的简单 API 提供该状态的高可用性和一致性。Service Fabric 的有状态服务使高可用性 (HA) 变得大众化，将其引入所有类型的应用程序，而不仅仅是数据库和其他数据存储。这是一个自然发展的过程：应用程序已从使用纯关系数据库来实现 HA 转为使用 NoSQL 数据库；现在，应用程序本身即可管理其内部的热”状态和数据以获得额外的性能提升，而不会牺牲可靠性、一致性和可用性。

生成包含微服务的应用程序时，通常有一个无状态 Web 应用（ASP.NET 和 node.js 等）组合调用应用于无状态和有状态业务中间层服务，这些服务使用 Service Fabric 部署命令全部部署到了同一 Service Fabric 群集中。在规模、可靠性和资源使用情况方面，每种这些服务都是独立的，从而大大提高了开发和生命周期管理的灵活性。
  
有状态微服务将简化应用程序设计，因为它们不再需要传统上需要用来处理纯无状态应用程序的可用性和延迟需求的附加队列和缓存。由于有状态服务天然就高度可用且具有低延迟，这意味着作为一个整体，应用程序中要管理的移动部件更少。下图说明了设计有状态应用程序与无状态应用程序之间的差异。通过利用 [Reliable Services](service-fabric-reliable-services-introduction) 和 [Reliable Actors](service-fabric-reliable-actors-introduction) 编程模型，有状态服务降低了应用程序的复杂性，同时实现了高吞吐量和低延迟。

## 使用无状态服务生成的应用程序##
![使用无状态服务的应用程序][Image1]

## 使用有状态服务生成的应用程序##
![使用无状态服务的应用程序][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤


使用 Service Fabric [Reliable Services](documentation/articles/service-fabric-reliable-services-quick-start) 和 [Reliable Actors](documentation/articles/service-fabric-reliable-actors-get-started) 编程模型，开始生成无状态和有状态服务。

此外，请参阅以下主题：

[定义和管理服务状态](documentation/articles/service-fabric-concepts-state)

[服务的可用性](documentation/articles/service-fabric-concepts-availability-services)

[Service Fabric 服务的可伸缩性](documentation/articles/service-fabric-concepts-scalability)

[Service Fabric 服务分区](documentation/articles/service-fabric-concepts-partitioning)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=74-->