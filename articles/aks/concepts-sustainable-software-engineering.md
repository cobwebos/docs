---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的可持续软件工程
description: 了解 Azure Kubernetes 服务 (AKS) 中的可持续软件工程。
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 2457de7bdaa94a6e2269515fafe6689d44960625
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984972"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 中的可持续软件工程原则 (AKS) 

可持续的软件工程原则是一组可帮助您定义、构建和运行可持续应用程序的能力。 总体目标是降低应用程序每个方面的碳足迹。 " [原则][principles-green] " 这一项目概述了可持续软件工程的原则。

关于可持续软件工程，需要了解的一个重要理念是：它是优先事项和重点的转移。 在许多情况下，软件的设计和运行方式侧重于快速性能和低延迟。 可持续软件工程侧重于尽可能多地降低碳排放量。 在某些情况下，应用可持续的软件工程原则可以提高性能或降低延迟，例如降低总网络行程。 在其他情况下，减少碳排放可能会导致性能降低或延迟增加，如延迟低优先级工作负荷。 考虑将可持续的软件工程原则应用于应用程序之前，请查看应用程序的优先级、需求和利弊。

## <a name="measure-and-optimize"></a>度量和优化

若要降低 AKS 群集的碳足迹，你需要了解群集资源的使用方式。 [Azure Monitor][azure-monitor] 提供有关群集资源使用情况的详细信息，例如内存和 CPU 使用率。 这些数据可以帮助你做出决定，以减少群集的碳足迹并观察所做更改的效果。 你还可以安装 [Microsoft 可持续性计算器][sustainability-calculator]，以查看所有 Azure 资源的碳足迹。

## <a name="increase-resource-utilization"></a>提高资源利用率

减少碳足迹的一种方法是缩短计算资源的空闲时间。 缩短空闲时间涉及提高计算资源的利用率。 例如，如果群集中有四个节点，每个节点以 50% 的容量运行，则所有四个节点都有 50% 的未使用容量保持空闲状态。 如果将群集减为三个节点，则相同的工作负荷会导致三个节点以 67% 的容量运行，将每个节点上的未使用容量减少到 33%，从而提高利用率。

> [!IMPORTANT]
> 考虑更改群集中的资源时，请验证你的[系统池][system-pools]是否有足够的资源来维持群集的核心系统组件的稳定性。 永远不要将群集的资源减少到群集可能会变得不稳定的程度。

查看群集利用率之后，请考虑使用[多节点池][multiple-node-pools]提供的功能。 可以使用[节点大小调整][node-sizing]通过特定的 CPU 和内存配置文件定义节点池，以便根据工作负荷需求定制节点。 根据工作负荷需求调整节点大小可以让你在运行较少节点的情况下提高利用率。 你还可以配置群集的[缩放][scale]方式，并使用[横向 pod 自动缩放程序][scale-horizontal]和[群集自动缩放程序][scale-auto]基于配置自动缩放群集。 控制群集的缩放方式可以让你的所有节点保持以高利用率运行，同时跟上对群集工作负荷的更改。 在工作负荷可承受突然中断或终止的情况下，可以使用 [点池][spot-pools] 来利用 Azure 中的空闲容量。 例如，对于批处理作业或开发环境，点池可能会很好地运行。

提高利用率还可以减少过多的节点，这可以减少[每个节点上的资源预留][resource-reservations]所消耗的能量。

另外，请在你的应用程序的 Kubernetes 清单中查看 CPU 和内存请求与限制。 由于你降低了内存和 CPU 的这些值，因此有更多的内存和 CPU 可供群集用来运行其他工作负荷。 由于你以较少的 CPU 和内存运行较多的工作负荷，因此可以更加密集地分配你的群集，从而提高利用率。 降低应用程序的 CPU 和内存时，如果将这些值设置得过低，应用程序的行为可能会降级或变得不稳定。 在更改 CPU 和内存请求与限制之前，请考虑运行一些基准测试，以了解这些值的设置是否合适。 此外，永远不要将这些值减少到你的应用程序会变得不稳定的程度。

## <a name="reduce-network-travel"></a>减少网络行程

减少往返群集的请求和响应的距离通常可以减少联网设备的电力消耗并减少碳排放。 查看网络流量后，请考虑在更靠近你的网络流量来源的[区域中][regions]创建群集。 你还可以使用 [Azure 流量管理器][azure-traffic-manager] 帮助将流量路由到最近的群集和 [邻近位置组][proiximity-placement-groups] ，以帮助减少 Azure 资源之间的距离。

> [!IMPORTANT]
> 考虑对群集的网络进行更改时，切勿以必须满足的工作负荷要求为代价来减少网络行程。 例如，使用 [可用性区域][availability-zones] 会导致群集上的网络旅行越多，但可能需要使用该功能来处理工作负荷要求。

## <a name="demand-shaping"></a>需求调整

如果可能，请考虑将对群集资源的需求转移到可以使用过量容量的时间或区域。 例如，请考虑更改要运行的批处理作业的时间或区域，或使用 [点池][spot-pools]。 还应考虑重构你的应用程序，以使用队列来延迟运行不需要立即处理的工作负荷。

## <a name="next-steps"></a>后续步骤

详细了解本文中提到的 AKS 的功能：

* [多个节点池][multiple-node-pools]
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
[principles-green]: https://principles.green/