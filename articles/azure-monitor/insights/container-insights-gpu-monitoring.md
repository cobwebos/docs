---
title: 使用用于容器的 Azure Monitor 配置 GPU 监视 | Microsoft Docs
description: 本文介绍如何使用用于容器的 Azure Monitor 配置具有 NVIDIA 和 AMD GPU 支持的节点的 Kubernetes 群集。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373305"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>使用用于容器的 Azure Monitor 配置 GPU 监视

从代理版本 ciprod03022019 开始，用于容器集成代理的 Azure Monitor 现已支持监视 GPU（图形处理单元）在 GPU 感知的 Kubernetes 集群节点上的使用情况，并监视请求和使用 GPU 资源的 Pod/容器。

## <a name="supported-gpu-vendors"></a>支持的 GPU 供应商

用于容器的 Azure Monitor 支持监视以下 GPU 供应商提供的 GPU 群集：

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

通过以 60 秒的间隔收集以下指标并将其存储在 InsightMetric 表中，用于容器的 Azure Monitor 会自动开始监视节点上的 GPU 使用情况以及 GPU 请求 Pod 和工作负载的情况：

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor|在刚过去的采样周期（60 秒）中，GPU 处于繁忙/积极处理容器的状态的时间百分比。 占空比是 1 到 100 之间的数字。 |
|containerGpuLimits |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName |每个容器可以将限值指定为一个或多个 GPU。 不能请求或限制为 GPU 的一部分。 |
|containerGpuRequests |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName |每个容器可以请求一个或多个 GPU。 不能请求或限制为 GPU 的一部分。|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor |可用于特定容器的 GPU 内存量（以字节为单位）。 |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor |特定容器使用的 GPU 内存量（以字节为单位）。 |
|nodeGpuAllocatable |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |节点中可供 Kubernetes 使用的 GPU 数。 |
|nodeGpuCapacity |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |节点中的 GPU 总数。 |

## <a name="gpu-performance-charts"></a>GPU 性能图表 

用于容器的 Azure Monitor 包含表中先前列出的指标的预配置图表，作为每个集群的 GPU 工作簿。 可以通过在左侧窗格中选择“工作簿”直接从 AKS 群集中找到 GPU 工作簿“节点 GPU”，也可以通过 Insight 中的“查看工作簿”下拉列表找到  。

## <a name="next-steps"></a>后续步骤

- 请参阅[在 Azure Kubernetes 服务 (AKS) 上将 GPU 用于计算密集型工作负荷](../../aks/gpu-cluster.md)，以了解如何部署包含支持 GPU 的节点的 AKS 群集。

- 详细了解[Microsoft Azure 中的 GPU 优化 VM sku](../../virtual-machines/sizes-gpu.md)。

- 请查看 [Kubernetes 中的 GPU 支持](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)，了解有关在群集中的一个或多个节点上管理 GPU 的 Kubernetes 试验性支持。