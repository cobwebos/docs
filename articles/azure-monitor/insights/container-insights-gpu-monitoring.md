---
title: 配置针对容器 Azure Monitor 的 GPU 监视 |Microsoft Docs
description: 本文介绍如何配置 Kubernetes 群集，并使其具有适用于容器 Azure Monitor 的 NVIDIA 和 AMD GPU enabled 节点。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80373305"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>配置针对容器 Azure Monitor 的 GPU 监视

从代理版本*ciprod03022019*开始，适用于容器的 Azure monitor 集成代理现在支持在识别 Gpu 的 Kubernetes 群集节点上监视 gpu （图形处理单元）使用情况，并监视请求和使用 GPU 资源的箱/容器。

## <a name="supported-gpu-vendors"></a>支持的 GPU 供应商

容器 Azure Monitor 支持监视以下 GPU 供应商提供的 GPU 群集：

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [采用](https://github.com/RadeonOpenCompute/k8s-device-plugin)

容器 Azure Monitor 自动开始监视节点上的 GPU 使用情况，并通过以60sec 时间间隔收集以下度量值并将其存储在**InsightMetrics**表中来请求 pod 和工作负荷：

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId、container.azm.ms/clusterName、容器、gpuId、gpuModel、gpuVendor|在过去的示例期间（60秒）内 GPU 忙于/主动处理容器的时间百分比。 "责任周期" 是1到100之间的一个数字。 |
|containerGpuLimits |container.azm.ms/clusterId、container.azm.ms/clusterName、容器 |每个容器可以将限制指定为一个或多个 Gpu。 不能请求或限制 GPU 的小数部分。 |
|containerGpuRequests |container.azm.ms/clusterId、container.azm.ms/clusterName、容器 |每个容器可以请求一个或多个 Gpu。 不能请求或限制 GPU 的小数部分。|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、容器、gpuId、gpuModel、gpuVendor |可用于特定容器的 GPU 内存量（以字节为单位）。 |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、容器、gpuId、gpuModel、gpuVendor |特定容器使用的 GPU 内存量（以字节为单位）。 |
|nodeGpuAllocatable |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |节点中可供 Kubernetes 使用的 Gpu 数。 |
|nodeGpuCapacity |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |节点中的 Gpu 总数。 |

## <a name="gpu-performance-charts"></a>GPU 性能图表 

容器 Azure Monitor 包含为表中前面列出的指标预配置的图表，作为每个群集的 GPU 工作簿。 通过从左侧窗格中选择工作簿，并从见解中的 "**查看工作簿**" 下拉列表中选择**工作**簿，可以直接从 AKS 群集中找到 GPU 工作簿**节点 gpu** 。

## <a name="next-steps"></a>后续步骤

- 请参阅[在 Azure Kubernetes Service （AKS）上将 gpu 用于计算密集型工作负荷](../../aks/gpu-cluster.md)，了解如何部署包含启用了 GPU 的节点的 AKS 群集。

- 详细了解[Microsoft Azure 中的 GPU 优化 VM sku](../../virtual-machines/sizes-gpu.md)。

- 查看[Kubernetes 中的 GPU 支持](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)，了解有关 Kubernetes 实验性支持的详细信息，以便在群集中的一个或多个节点上管理 gpu。