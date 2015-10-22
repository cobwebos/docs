<properties
   pageTitle="Service Fabric Reliable Service 体系结构"
   description="Reliable Service 体系结构的综合概述"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# Reliable Service 体系结构

Service Fabric Reliable Service 可能有状态，也可能无状态。每种类型的服务都在本文所述的特定体系结构内运行。有关有状态服务和无状态服务之间的区别的详细信息，请参阅 [Reliable Service 概述](/documentation/articles/service-fabric-reliable-services-introduction.md)。

## 有状态的 Reliable Service

### 有状态的 Reliable Service 体系结构示意图
![体系结构示意图](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### 有状态的 Reliable Service

有状态的 Reliable Service 可以从 StatefulService 或 StatefulServiceBase 类派生。这两个基类均由 Service Fabric 提供，可为有状态服务提供各种支持和抽象层级，以便与 Service Fabric 进行交互，并作为服务加入 Service Fabric 群集。StatefulService 派生自 StatefulServiceBase；StatefulServiceBase 为服务提供更大的灵活性，但需要对 Service Fabric 内部运作有更多了解。有关使用 StatefulService 和 StatefulServiceBase 类编写服务的细节的详细信息，请参阅 [Reliable Service 概述](/documentation/articles/service-fabric-reliable-services-introduction)和 [Reliable Service 高级用法](/documentation/articles/service-fabric-reliable-services-advanced-usage)。

这两个基类可管理服务实现的生存期和角色。如果服务实现需要在服务实现生命周期中的那些阶段执行操作或者想要创建通信侦听器对象，服务实现可能会重写任一基类的虚拟方法。请注意，尽管服务实现可以实现自己的通信侦听器对象来公开 ICommunicationListener，但在上图中，通信侦听器仍由 Service Fabric 实现，因为服务实现使用由 Service Fabric 实现的通信侦听器。

有状态的 Reliable Service 可使用可靠状态管理器来利用 Reliable Collections。Reliable Collections 是对服务高度可用的本地数据结构，即，无论是否进行服务故障转移，一律可以使用。每种类型的 Reliable Collection 都由可靠状态提供程序实现。有关 Reliable Collections 的详细信息，请参阅 [Reliable Collections 概述](/documentation/articles/service-fabric-reliable-services-reliable-collections)

### 可靠状态管理器和提供程序

可靠状态管理器是用于管理可靠状态提供程序的对象，它具有创建、删除、枚举以及确保可靠状态提供程序持续保存且高度可用的功能。可靠状态提供程序实例代表持续保存且高度可用的数据结构的实例，比如字典或队列。每个可靠状态提供程序公开一个接口，由有状态服务用于与可靠状态提供程序交互。例如，IReliableDictionary 用来与 Reliable Dictionary 交互，IReliableQueue 用来与 Reliable Queue 交互。所有可靠状态提供程序均实现 IReliableState 接口。

可靠状态管理器具有一个名为 IReliableStateManager 的接口，该接口允许有状态服务实现访问。面向可靠状态提供程序的接口通过 IReliableStateManager 返回。

可靠状态管理器使用动态插件体系结构构建而成，因此可以动态地插入新的 Reliable Collections 类型。

Reliable Dictionary 和 Reliable Queue 以高性能版本的差异存储实现为基础构建。

### 事务复制器

事务复制器组件负责确保服务的状态（即可靠状态管理器和 Reliable Collections 内的状态）跨运行服务的所有副本保持一致，并且确保该状态也保存在日志中。可靠状态管理器通过私有机制与事务复制器交互。

事务复制器使用网络协议与服务实例的其他副本就状态进行通信，以便所有副本都具有最新的状态信息。

事务复制器使用日志保存状态信息，以便该状态信息在进程或节点崩溃后依然存在。与日志的交互通过私有机制进行。

### 日志

日志组件提供一个高性能的持久性存储，该存储可针对旋转磁盘或固态磁盘的写入进行优化，也可针对磁盘空间的最有效利用进行优化。日志的设计旨在让该持久性存储（即硬盘）成为运行有状态服务的节点的本地存储，与非节点本地存储的持久性存储相比，这种存储可实现低延迟和高吞吐量。

日志组件使用两种类型的日志文件。一种是节点范围内共享的日志文件，应位于只用于该日志文件的磁盘上。此文件位于 Service Fabric 节点的工作目录中。服务的每个副本还有一个专用的日志文件，位于服务的工作目录中。共享日志是状态信息的过渡区域，而专用日志文件则是其最终的保存目标位置。在此设计中，首先将状态信息写入共享日志文件，然后在后台延迟移出到专用日志文件。通过这种方式，共享日志写入将具有最低的延迟和最高的吞吐量，以便让服务加快进度。

但是，当日志组件使用 OptimizeForLocalSSD 设置配置为针对固态磁盘进行优化时，状态信息将直接写入专用日志文件，并绕过共享日志文件。由于固态磁盘不会发生磁头移动争用而导致延迟，因此，直接写入专用日志文件不会产生任何负面影响。

当日志组件使用 OptimizeLogForLowerDiskUsage 优化为最大程度减少磁盘空间的使用时，将创建专用日志文件作为 NTFS 稀疏文件。由于一般情况下，日志文件不会始终被状态信息占满，因此，使用稀疏文件可将可用的磁盘空间调配给更多副本。如果不以这种方式进行配置，则将预分配日志文件空间，日志组件可以最高性能直接写入文件。

除了为日志提供最精简的用户模式接口，还将以内核模式驱动程序的形式编写日志。通过以内核模式驱动程序的形式运行，日志可以为所有使用它的服务提供最高的性能。

有关配置日志的详细信息，请参阅[配置有状态的 Reliable Services](/documentation/articles/service-fabric-reliable-services-configuration)。

## 无状态的 Reliable Service

### 无状态的 Reliable Service 体系结构示意图
![体系结构示意图](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### 无状态的 Reliable Service

无状态服务实现派生自 StatelessService 或 StatelessServiceBase 类，其中，StatelessServiceBase 类比 StatelessService 提供更大的灵活性。这两个基类可管理服务的生存期和角色。如果服务需要在服务生命周期中的那些阶段执行操作或者想要创建通信侦听器对象，服务实现可能会重写任一基类的虚拟方法。请注意，尽管服务可以实现自己的通信侦听器对象来公开 ICommunicationListener，但在上图中，通信侦听器仍由 Service Fabric 实现，因为该服务实现使用由 Service Fabric 实现的通信侦听器。

有关使用 StatelessService 和 StatelessServiceBase 类编写服务的细节的详细信息，请参阅 [Reliable Service 概述](/documentation/articles/service-fabric-reliable-services-introduction)和 [Reliable Service 高级用法](/documentation/articles/service-fabric-reliable-services-advanced-usage)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关 Service Fabric 的详细信息，请参阅：

[Reliable Service 概述](/documentation/articles/service-fabric-reliable-services-introduction)

[快速启动](/documentation/articles/service-fabric-reliable-services-quick-start)

[Reliable Collections 概述](/documentation/articles/service-fabric-reliable-services-reliable-collections)

[Reliable Service 高级用法](/documentation/articles/service-fabric-reliable-services-advanced-usage)

[Reliable Service 配置](/documentation/articles/service-fabric-reliable-services-configuration)
 

<!---HONumber=74-->