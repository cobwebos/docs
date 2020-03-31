---
title: 使用 Azure 监视器配置容器的 GPU 监视 |微软文档
description: 本文介绍如何使用启用 NVIDIA 和 AMD GPU 的节点配置监视库伯奈斯群集，以及用于容器的 Azure 监视器。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373305"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>使用 Azure 监视器配置容器的 GPU 监视

从代理版本*ciprod03022019*开始，用于容器集成代理的 Azure 监视器现在支持监视 GPU 感知 Kubernetes 群集节点上的 GPU（图形处理单元）使用情况，并监视请求和使用 GPU 资源的 pod/容器。

## <a name="supported-gpu-vendors"></a>支持的 GPU 供应商

容器的 Azure 监视器支持监视来自以下 GPU 供应商的 GPU 群集：

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

容器的 Azure 监视器通过每隔 60 秒收集以下指标并将其存储在**InsightMetrics**表中，自动开始监视节点上的 GPU 使用情况，GPU 请求窗格和工作负载：

|指标名称 |指标维度（标签） |描述 |
|------------|------------------------|------------|
|容器Gpu任务循环 |container.azm.ms/clusterId，container.azm.ms/clusterName，容器名称，gpuId，gpuModel，供应商 gpu|GPU 繁忙/主动处理容器的过去采样期（60 秒）的百分比。 占空比是介于 1 和 100 之间的数字。 |
|容器 Gpu 限制 |container.azm.ms/clusterId、container.azm.ms/clusterName、容器名称 |每个容器都可以将限制指定为一个或多个 GPU。 无法请求或限制 GPU 的一小部分。 |
|容器 Gpu 请求 |container.azm.ms/clusterId、container.azm.ms/clusterName、容器名称 |每个容器可以请求一个或多个 GPU。 无法请求或限制 GPU 的一小部分。|
|容器 Gpu 内存总字节 |container.azm.ms/clusterId，container.azm.ms/clusterName，容器名称，gpuId，gpuModel，供应商 gpu |可用于特定容器的 GPU 内存量（以字节为单位）。 |
|容器 Gpu 记忆使用字节 |container.azm.ms/clusterId，container.azm.ms/clusterName，容器名称，gpuId，gpuModel，供应商 gpu |特定容器使用的 GPU 内存量（以字节为单位）。 |
|节点GpuAlloca可 |container.azm.ms/clusterId、container.azm.ms/clusterName、gpu 供应商 |Kubernetes 可以使用的节点中的 GPU 数。 |
|节点 Gpu 容量 |container.azm.ms/clusterId、container.azm.ms/clusterName、gpu 供应商 |节点中的 GPU 总数。 |

## <a name="gpu-performance-charts"></a>GPU 性能图表 

容器的 Azure 监视器包括表前面列出的指标的预配置图表，作为每个群集的 GPU 工作簿。 您可以通过从左侧窗格中选择**工作簿**，以及从"透视"中的 **"查看工作簿"** 下拉列表中直接从 AKS 群集中找到 GPU 工作簿**节点 GPU。**

## <a name="next-steps"></a>后续步骤

- 请参阅在 Azure Kubernetes 服务 （AKS）[上使用 GPU 进行计算密集型工作负荷](../../aks/gpu-cluster.md)，了解如何部署包含启用 GPU 的节点的 AKS 群集。

- 详细了解微软[Azure 中的 GPU 优化 VM SKU](../../virtual-machines/sizes-gpu.md)。

- 查看[Kubernetes 中的 GPU 支持](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)，了解有关 Kubernetes 在群集中一个或多个节点管理 GPU 的实验支持。