---
title: 用于容器的 Azure Monitor 概述 | Microsoft Docs
description: 本文介绍用于容器的 Azure Monitor，它通过监视 AKS 群集和 Azure 中容器实例的运行状况监视 AKS 容器见解解决方案及其提供的值。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 96a312630c92048f36f79e3bec18f83ed5a445ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414127"
---
# <a name="azure-monitor-for-containers-overview"></a>用于容器的 Azure Monitor 概述

用于容器的 Azure Monitor 功能旨在监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

用于容器的 Azure Monitor 通过 Metrics API 从 Kubernetes 中提供的控制器、节点和容器收集内存和处理器指标，来提供性能可见性。 容器日志也会被收集。  从 Kubernetes 群集启用监视后，将通过适用于 Linux 的 Log Analytics 代理的容器化版本自动收集指标和日志。 指标将写入指标存储区，日志数据将写入与 [Log Analytics](../log-query/log-query-overview.md) 工作区关联的日志存储区。 

![用于容器的 Azure Monitor 的体系结构](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>用于容器的 Azure Monitor 提供了什么？

用于容器的 Azure Monitor 使用 Azure Monitor 的不同功能提供全面的监视体验，使你能够了解 Kubernetes 群集和容器工作负荷的性能和运行状况。 使用用于容器的 Azure Monitor 可以：

* 确定节点上运行的 AKS 容器及其平均处理器和内存利用率。 此信息可帮助标识资源瓶颈。
* 确定 Azure 容器实例中托管的容器组及其容器的处理器和内存利用率。  
* 确定容器在控制器或 Pod 中的驻留位置。 此信息可帮助了解控制器或 Pod 的整体性能。
* 查看在主机上运行的与支持 Pod 的标准过程无关的工作负荷的资源利用率。
* 了解群集在平均负载和最重负载下的行为。 此信息有助于了解容量需求及确定群集可承受的最大负载。 
* 配置警报，以便在节点或容器上的 CPU 和内存使用率超出阈值时主动通知你或将其记录下来。
* 与[Prometheus](https://prometheus.io/docs/introduction/overview/)集成, 以查看它从节点和 Kubernetes 收集的应用程序和工作负荷指标使用[查询](container-insights-log-search.md)创建自定义警报、仪表板, 并详细执行详细分析。

>[!NOTE]
>目前, 对 Prometheus 的支持是公共预览版中的一项功能。
>

请观看以下视频, 提供中级深入探讨, 帮助你了解如何使用容器 Azure Monitor 来监视 AKS 群集。

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>如何访问此功能？

可以通过两种方式访问用于容器的 Azure Monitor：从 Azure Monitor 访问或直接从所选 AKS 群集访问。 在 Azure Monitor 中可以从全局角度查看已部署的所有容器（受到监视的容器和未受监视的容器），从而可以跨订阅和资源组进行搜索和筛选，然后从所选容器钻取到用于容器的 Azure Monitor。  否则，可以直接从 AKS 页上选定的 AKS 容器访问该功能。  

![访问用于容器的 Azure Monitor 的方法概述](./media/container-insights-overview/azmon-containers-experience.png)

如果你有兴趣监视和管理在 AKS 外部运行的 Docker 和 Windows 容器主机以查看配置、审核和资源使用情况, 请参阅[容器监视解决方案](../../azure-monitor/insights/containers.md)。

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何为容器启用 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。  
