---
title: 监视容器实例
description: 如何监视 Azure 容器实例中的容器消耗的计算资源，例如 CPU 和内存。
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: bd86161bc7840be599eb5ee9a20f6dbf143f5f22
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533645"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>监视 Azure 容器实例中的容器资源

[Azure Monitor][azure-monitoring] 可以洞察容器实例使用的计算资源。 此资源使用情况数据有助于确定容器组的最佳资源设置。 Azure Monitor 还提供用于在容器实例中跟踪网络活动的指标。

本文档详述了如何使用 Azure 门户和 Azure CLI 收集容器实例的 Azure Monitor 指标。

> [!IMPORTANT]
> Azure 容器实例中的 Azure Monitor 指标目前为预览版，并且存在某些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

目前，仅提供了 Linux 容器的 Azure Monitor 指标。

## <a name="available-metrics"></a>可用指标

Azure Monitor 提供下述 [Azure 容器实例指标][supported-metrics]。 这些指标适用于容器组和单独的容器。

* **CPU 使用率** - 以**毫核心**为单位进行度量。 一个毫核心是一个 CPU 核心的 1/1000，因此 500 毫核心（或 500 毫）表示 CPU 核心的使用率为 50%。 聚合为所有核心的**平均使用率**。

* **内存使用率** - 聚合为**平均字节数**。

* **每秒接收的网络字节数**和**每秒传输的网络字节数** - 聚合为**每秒平均字节数**。 

## <a name="get-metrics---azure-portal"></a>获取指标 - Azure 门户

创建容器组时，Azure Monitor 数据在 Azure 门户中可用。 若要查看容器组的指标，请转到容器组的“概览”页。 在这里可以看到为每个可用指标预先创建的图表。

![双图表][dual-chart]

在包含多个容器的容器组中，使用[维度][monitor-dimension]按容器显示指标。 若要创建包含个体容器指标的图表，请执行以下步骤：

1. 在“概览”页中选择一个指标图表，例如“CPU”。 
1. 选择“应用拆分”按钮，然后选择“容器名称”。

![维度][dimension]

## <a name="get-metrics---azure-cli"></a>获取指标 - Azure CLI

还可以使用 Azure CLI 收集容器实例的指标。 首先，使用以下命令获取容器组的 ID。 将 `<resource-group>` 替换为你的资源组名称，将 `<container-group>` 替换为你的容器组的名称。


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

使用以下命令获取 **CPU** 使用情况指标。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

更改命令中 `--metric` 参数的值即可获取其他[支持的指标][supported-metrics]。 例如，使用以下命令获取**内存**使用率指标。 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

对于多容器组，添加 `containerName` 维度即可返回每个容器的指标。

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>后续步骤

从 [Azure 监视概述][azure-monitoring]详细了解 Azure 监视。

了解如何创建[指标警报][metric-alert]，以便在 Azure 容器实例的指标超出阈值时获得通知。

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
