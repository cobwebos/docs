<properties
   pageTitle="资源平衡器体系结构"
   description="Service Fabric 资源平衡器体系结构概述"
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

Service Fabric 资源平衡器由单个集中式资源平衡服务和存在于每个节点上的组件构成，它们在概念上分别与 Service Fabric 故障转移管理器和本地 Service Fabric 节点共存。

本地代理负责收集和缓冲处理本地节点上运行的服务的负载报告，将报告发送至资源平衡器服务，并将故障和其他事件报告给故障转移管理器和资源平衡器（上面的 1 和 2）。资源平衡器服务与故障转移管理器共同合作，响应系统中的负载事件和其他事件，比如副本或节点故障、负载报告以及已创建和删除的服务和应用程序。例如，当副本发生故障时，故障转移管理器会请求 Service Fabric 资源平衡器根据不同节点的负载数据来建议取代位置。同样地，当收到新的服务请求时，故障转移管理器也会请求资源平衡器提供放置该服务的建议位置。在这些情况下，资源平衡器会响应各种服务配置更改 (3)，然后由故障转移管理器来执行。在上述示例中，故障转移管理器会在节点上创建新的副本，以形成最佳的整体平衡 (4)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

资源平衡功能：

- [描述群集](/documentation/articles/service-fabric-resource-balancer-cluster-description)
- [描述服务](/documentation/articles/service-fabric-resource-balancer-service-description)
- [报告动态负载](/documentation/articles/service-fabric-resource-balancer-dynamic-load-reporting)
- [主动指标封装](/documentation/articles/service-fabric-resource-balancer-proactive-metric-packing)
- [节点缓冲区百分比](/documentation/articles/service-fabric-resource-balancer-node-buffer-percentage)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=74-->