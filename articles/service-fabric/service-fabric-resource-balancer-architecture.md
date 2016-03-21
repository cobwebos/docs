<properties
   pageTitle="资源平衡器体系结构 | Microsoft Azure"
   description="Service Fabric 资源平衡器体系结构概述。"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 资源平衡器体系结构概述

![资源平衡器体系结构][Image1]

Azure Service Fabric 中的资源平衡器由单个集中式资源平衡服务以及一个位于每个节点上的组件构成。从概念上讲，这两个构件分别与 Service Fabric 故障转移管理器和本地 Service Fabric 节点共置在一起。

本地代理负责收集和缓冲处理本地节点上运行的服务的负载报告，将报告发送至资源平衡器服务，并将故障和其他事件报告给故障转移管理器和资源平衡器（上面的 1 和 2）。资源平衡器和故障转移管理器相互协作以响应系统中的负载事件和其他事件。这些事件可能包括副本或节点故障、负载报告，以及创建和删除的服务与应用程序。

例如，当副本发生故障时，故障转移管理器会请求资源平衡器根据不同节点的负载数据来建议取代位置。同样地，当收到新的服务请求时，故障转移管理器也会请求资源平衡器提供放置该服务的建议位置。在所有这些情况下，资源平衡器将会响应针对各种服务配置的更改 (3)。然后，故障转移管理器将发布这些更改。在上述示例中，故障转移管理器会在节点上创建新的副本，以形成最佳的整体平衡 (4)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

资源平衡功能：

- [描述群集](/documentation/articles/service-fabric-resource-balancer-cluster-description)
- [描述服务](/documentation/articles/service-fabric-resource-balancer-service-description)
- [报告动态负载](/documentation/articles/service-fabric-resource-balancer-dynamic-load-reporting)
- [主动指标打包](/documentation/articles/service-fabric-resource-balancer-proactive-metric-packing)
- [节点缓冲区百分比](/documentation/articles/service-fabric-resource-balancer-node-buffer-percentage)

[Image1]: ./media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=Mooncake_0314_2016-->