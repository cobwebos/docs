---
title: 用于容器的 Azure Monitor 概述 | Microsoft Docs
description: 本文介绍用于容器的 Azure Monitor，它通过监视 AKS 群集和 Azure 中容器实例的运行状况监视 AKS 容器见解解决方案及其提供的值。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385539"
---
# <a name="azure-monitor-for-containers-overview"></a>用于容器的 Azure Monitor 概述

容器 Azure Monitor 是一项功能，用于监视部署到的容器工作负荷的性能：

- 托管在[Azure Kubernetes 服务（AKS）](../../aks/intro-kubernetes.md)上的托管 Kubernetes 群集
- 使用[AKS 引擎](https://github.com/Azure/aks-engine)托管在 Azure 上的自托管 Kubernetes 群集
- [Azure 容器实例](../../container-instances/container-instances-overview.md)
- 托管在[Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910)或本地的自行管理的 Kubernetes 群集
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

容器 Azure Monitor 支持运行 Linux 和 Windows Server 2019 操作系统的群集。 

监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

用于容器的 Azure Monitor 通过 Metrics API 从 Kubernetes 中提供的控制器、节点和容器收集内存和处理器指标，来提供性能可见性。 容器日志也会被收集。  从 Kubernetes 群集启用监视后，将自动为你收集指标和日志，并通过容器化版本的适用于 Linux 的 Log Analytics 代理。 度量值将写入度量值存储，并将日志数据写入与[Log Analytics](../log-query/log-query-overview.md)工作区关联的日志存储中。 

![容器体系结构的 Azure Monitor](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>用于容器的 Azure Monitor 提供了什么？

容器 Azure Monitor 提供使用 Azure Monitor 的不同功能的综合监视体验。 利用这些功能，你可以了解运行 Linux 和 Windows Server 2019 操作系统的 Kubernetes 群集的性能和运行状况，以及容器工作负荷。 对于容器 Azure Monitor，你可以：

* 确定节点上运行的 AKS 容器及其平均处理器和内存利用率。 此信息可帮助标识资源瓶颈。
* 确定 Azure 容器实例中托管的容器组及其容器的处理器和内存利用率。  
* 确定容器在控制器或 Pod 中的驻留位置。 此信息可帮助了解控制器或 Pod 的整体性能。 
* 查看在主机上运行的与支持 Pod 的标准过程无关的工作负荷的资源利用率。
* 了解群集在平均负载和最重负载下的行为。 此信息有助于了解容量需求及确定群集可承受的最大负载。 
* 配置警报，以便在节点或容器上的 CPU 和内存使用率超过阈值时，或者当群集中的基础结构或节点运行状况汇总发生状态更改时，主动通知你或记录此警报。
* 与[Prometheus](https://prometheus.io/docs/introduction/overview/)集成，以查看它从节点和 Kubernetes 收集的应用程序和工作负荷指标使用[查询](container-insights-log-search.md)创建自定义警报、仪表板，并详细执行详细分析。
* 监视部署到 Azure Stack 上的[AKS engine](https://github.com/Azure/aks-engine)和[AKS engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)的容器工作负荷。
* 监视[部署到 Azure Red Hat OpenShift](../../openshift/intro-openshift.md)的容器工作负荷。

    >[!NOTE]
    >目前，对 Azure Red Hat OpenShift 的支持是公共预览版中的一项功能。
    >

与 Linux 群集相比，监视 Windows Server 群集的主要区别如下：

- 内存 RSS 指标不适用于 Windows 节点和容器。
- 磁盘存储容量信息不适用于 Windows 节点。
- 容器日志不可用于 Windows 节点中运行的容器。
- Windows 容器日志除外，提供实时数据（预览版）功能支持。
- 仅监视 pod 环境，而不监视 Docker 环境。
- 对于预览版本，最多支持30个 Windows Server 容器。 此限制不适用于 Linux 容器。 

请观看以下视频，提供中级深入探讨，帮助你了解如何使用容器 Azure Monitor 来监视 AKS 群集。

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>如何访问此功能？

可以通过两种方式访问用于容器的 Azure Monitor：从 Azure Monitor 访问或直接从所选 AKS 群集访问。 在 Azure Monitor 中，你将对部署的所有容器进行全局透视，这些容器受到监视且不是，允许跨订阅和资源组搜索和筛选，然后深入 Azure Monitor 到所选容器。  否则，你可以从 AKS 页面直接从所选的 AKS 容器访问该功能。  

![访问用于容器的 Azure Monitor 的方法概述](./media/container-insights-overview/azmon-containers-experience.png)

如果你有兴趣监视和管理在 AKS 外部运行的 Docker 和 Windows 容器主机以查看配置、审核和资源使用情况，请参阅[容器监视解决方案](../../azure-monitor/insights/containers.md)。

## <a name="next-steps"></a>后续步骤

若要开始监视 Kubernetes 群集，请查看[如何启用容器 Azure Monitor](container-insights-onboard.md)以了解启用监视的要求和可用方法。 
