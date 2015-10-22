<properties
   pageTitle="动态负载报告"
   description="资源平衡器动态负载报告概述"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 动态负载报告概述

在运行时，有状态和无状态的服务对象可以通过 ReportLoad 方法（IStatefulServicePartition 和 IStatelessServicePartition 接口的成员）来报告负载。运行时负载值报告非常重要，因为它可以确保服务精确封装成节点，并协助中央 Service Fabric 资源平衡器精确跟踪资源使用率，让节点上的服务资源体验更加顺畅。

请注意，当副本报告负载时，会先与其他负载报告一起在本地进行批处理，再将单个报告发送至资源平衡器。此过程意味着，如果服务很快就重复报告负载，实际上只会将最后一个报告发送给资源平衡器。

当 Service Fabric 资源平衡器运行时，会检查所有节点聚合的全部负载信息，并执行部分检查。这些检查包含群集清单中所定义的指标平衡阈值和活动阈值。它们将确定群集的失衡程度是否需要运行资源平衡器，以及是否有任何特定节点的容量已超过所定义的任何指标容量。在容量超标的情况下，资源平衡器首先只考虑容量超标的节点上共享容量超标指标的服务。若是群集失衡的情况，资源平衡器会考虑通过指标与报告失衡指标的任何服务相关联的所有服务。如果 Service Fabric 资源平衡器判断有任何情况属实，即会运行模拟，尝试寻找更好的服务排列方式。

服务应在负载变更时报告负载，且不自行对负载报告执行任何聚合或平滑处理操作。

请注意，从服务报告负载之时起，这些负载报告即会取代服务创建时所定义的默认主要和次要负载值，并成为 Service Fabric 资源平衡器在进行平衡或放置决策时所使用的新负载值。



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息，请参阅：[资源平衡器体系结构](/documentation/articles/service-fabric-resource-balancer-architecture)
 

<!---HONumber=74-->