---
title: 监视 Azure 容器实例中的容器
description: 详细介绍了如何监视 Azure 容器实例中的容器消耗的计算资源，例如 CPU 和内存。
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: nepeters
ms.openlocfilehash: 814346bd8021b996b64cd7f0311f31b13b32a8c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>监视 Azure 容器实例中的容器资源

Azure Monitor 可以洞察容器实例使用的计算资源。 使用 Azure Monitor 跟踪容器组及其容器的 CPU 和内存利用率。 此资源使用情况数据有助于确定容器组的最佳 CPU 和内存设置。

本文档详述了如何使用 Azure 门户和 Azure CLI 收集容器实例的 CPU 和内存使用情况。

> [!IMPORTANT]
> 目前，仅提供了 Linux 容器的资源使用情况指标。
>

## <a name="available-metrics"></a>可用指标

Azure Monitor 提供了有关 Azure 容器实例的 **CPU** 和**内存**使用情况的指标。 对于容器组和单独的容器都提供了这两个指标。

CPU 指标是以**毫核心**为单位表示的。 一个毫核心是一个 CPU 核心的 1/1000，因此 500 毫核心（或 500 毫）表示 CPU 核心的利用率为 50%。

内存指标是以**字节**为单位表示的。

## <a name="get-metrics---azure-portal"></a>获取指标 - Azure 门户

创建容器组时，Azure Monitor 数据在 Azure 门户中可用。 若要查看容器组的指标，请选择资源组，然后选择容器组。 可以看到有关 CPU 和内存使用情况的预创建图表。

![双图表][dual-chart]

如果你有包含多个容器的容器组，请使用[维度][monitor-dimension]来表示每个个体容器的指标。 若要创建包含个体容器指标的图表，请执行以下步骤：

1. 从左侧导航菜单中选择“监视”。
2. 选择一个容器组和一个指标（CPU 或内存）。
3. 选择绿色的维度按钮，并选择“容器名称”。

![维度][dimension]

## <a name="get-metrics---azure-cli"></a>获取指标 - Azure CLI

还可以使用 Azure CLI 收集容器实例 CPU 和内存使用情况。 首先，使用以下命令获取容器组的 ID。 将 `<resource-group>` 替换为你的资源组名称，将 `<container-group>` 替换为你的容器组的名称。


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

使用以下命令获取 **CPU** 使用情况指标。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

使用以下命令获取**内存**使用情况指标。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

对于多容器组，可以添加 `containerName` 维度来返回每个容器的此数据。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>后续步骤

从 [Azure 监视概述][azure-monitoring]详细了解 Azure 监视。

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../monitoring-and-diagnostics/monitoring-metric-charts.md#what-are-multi-dimensional-metrics
