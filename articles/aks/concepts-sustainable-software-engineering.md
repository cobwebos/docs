---
title: '概念-Azure Kubernetes Services 中的可持久软件工程 (AKS) '
description: 了解 Azure Kubernetes Service (AKS) 中的可持续软件工程。
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: bc2240b3cb8508109f6dd25c4c80ff31ba197cfd
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083583"
---
# <a name="sustainable-software-engineering-principals-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 中的可持续软件工程主体 (AKS) 

可持续的软件工程主体是一组可帮助您定义、构建和运行可持续应用程序的能力。 总体目标是降低应用程序每个方面的碳足迹。 " [原则][principals-green] " 这一项目概述了持久的软件工程。

了解可持续软件工程的一个重要主意是，这是优先考虑因素。 在许多情况下，软件的设计和运行方式侧重于快速性能和低延迟。 可持续的软件工程侧重于降低尽可能多的碳排放量。 在某些情况下，应用可持续的软件工程主体可以提高性能或降低延迟，例如降低总网络旅行。 在其他情况下，减少碳排放可能会导致性能降低或延迟增加，如延迟低优先级工作负荷。 考虑将可持续的软件工程主体应用到应用程序之前，请查看应用程序的优先级、需求和利弊。

## <a name="measure-and-optimize"></a>度量和优化

若要降低 AKS 群集的碳足迹，需要了解群集资源的使用方式。 [Azure Monitor][azure-monitor] 提供有关群集资源使用情况的详细信息，例如内存和 CPU 使用率。 此数据可帮助通知你决定降低群集的碳足迹，并观察更改的效果。 还可以安装 [Microsoft 可持续性计算器][sustainability-calculator] 来查看所有 Azure 资源的碳足迹。

## <a name="increase-resource-utilization"></a>提高资源利用率

降低碳足迹的一种方法是缩短计算资源的空闲时间量。 缩短空闲时间涉及增加计算资源的利用率。 例如，如果群集中有四个节点，每个节点的容量为50%，则所有四个节点都有50% 的未使用容量空闲空间。 如果将群集减为三个节点，则相同的工作负载会导致三个节点以67% 的容量运行，将每个节点上的未使用容量减少到33%，从而提高利用率。

> [!IMPORTANT]
> 考虑更改群集中的资源时，验证 [系统池][system-pools] 是否有足够的资源来维持群集核心系统组件的稳定性。 不要将群集的资源降到群集可能会变得不稳定的点。

查看群集利用率之后，请考虑使用 [多个节点池][multiple-node-pools]提供的功能。 你可以使用 [节点大小调整][node-sizing] 来定义具有特定 CPU 和内存配置文件的节点池，使你能够根据工作负荷需求定制节点。 根据工作负荷需求调整节点大小可以帮助你在更高的利用率内运行一些节点。 你还可以配置群集的 [缩放][scale] 方式，并使用 [水平 pod 自动缩放程序][scale-horizontal] 和 [群集自动缩放程序][scale-auto] 基于配置自动扩展群集。 控制群集的缩放方式可帮助您保持所有节点在高利用率下运行，同时保持对群集工作负荷的更改。 在工作负荷可承受突然中断或终止的情况下，可以使用 [点池][spot-pools] 来利用 Azure 中的空闲容量。 例如，对于批处理作业或开发环境，点池可能会很好地运行。

增加利用率还可以减少过量节点，从而减少 [每个节点上的资源保留][resource-reservations]所消耗的能量。

另外，请查看应用程序的 Kubernetes 清单中的 CPU 和内存 *请求* 和 *限制* 。 降低了内存和 CPU 的值时，群集可以使用更多的内存和 CPU 来运行其他工作负荷。 如果运行的工作负荷的 CPU 和内存较低，则群集就会变得更密集，从而提高利用率。 降低应用程序的 CPU 和内存时，如果将这些值设置得过低，应用程序的行为可能会降级或不稳定。 更改 CPU 和内存 *请求* 和 *限制*之前，请考虑运行一些基准测试，以了解这些值是否设置正确。 此外，在应用程序变得不稳定时，不要将这些值减少到点。

## <a name="reduce-network-travel"></a>减少网络旅行

减少与群集之间的距离请求和对流量的响应通常会降低网络设备的电能消耗，并减少碳排放量。 查看网络流量后，请考虑在离网络流量源更近的 [区域中][regions] 创建群集。 你还可以使用 [Azure 流量管理器][azure-traffic-manager] 帮助将流量路由到最近的群集和 [邻近位置组][proiximity-placement-groups] ，以帮助减少 Azure 资源之间的距离。

> [!IMPORTANT]
> 考虑对群集的网络进行更改时，切勿以满足工作负荷要求的成本减少网络旅行。 例如，使用 [可用性区域][availability-zones] 会导致群集上的网络旅行越多，但可能需要使用该功能来处理工作负荷要求。

## <a name="demand-shaping"></a>需求调整

如果可能，请考虑将群集资源的需求改变到可使用超出容量的时间或区域。 例如，请考虑更改要运行的批处理作业的时间或区域，或使用 [点池][spot-pools]。 还应考虑重构应用程序，以使用队列来延迟不需要立即处理的运行工作负荷。

## <a name="next-steps"></a>后续步骤

详细了解本文中所述的 AKS 功能：

* [多节点池][multiple-node-pools]
* [节点大小调整][node-sizing]
* [缩放群集][scale]
* [水平 Pod 自动缩放程序][scale-horizontal]
* [群集自动缩放程序][scale-auto]
* [污点池][spot-pools]
* [系统池][system-pools]
* [资源预留][resource-reservations]
* [邻近位置组][proiximity-placement-groups]
* [可用性区域][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principals-green]: https://principles.green/