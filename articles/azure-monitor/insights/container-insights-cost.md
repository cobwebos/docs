---
title: 容器 Azure Monitor 的监视开销 |Microsoft Docs
description: 本文介绍 Azure Monitor 为容器收集的指标 & 清单数据的监视开销，以帮助客户管理其使用情况和相关成本。
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: a03e94fa7650c56a4d3b3beda3c27283329aebbe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84204644"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>了解容器 Azure Monitor 的监视成本

本文提供了有关容器 Azure Monitor 的定价指导，帮助你了解以下各项：

* 如何在启用此见解之前提前估算成本

* 如何在为一个或多个容器启用容器 Azure Monitor 之后测量成本

* 如何控制数据收集并降低成本

Azure Monitor 日志收集、索引和存储 Kubernetes 群集生成的数据。 

Azure Monitor 定价模型主要基于 Log Analytics 工作区中引入的数据量（以 gb 为单位）。 Log Analytics 工作区的成本不仅基于收集的数据量，它也依赖于所选的计划，以及你选择用于存储从群集生成的数据的时间。

>[!NOTE]
>所有规模和定价仅适用于示例估算。 请参阅 Azure Monitor [定价](https://azure.microsoft.com/pricing/details/monitor/) 页，了解最新定价的 Azure Monitor Log Analytics 定价模型和 Azure 区域。

下面汇总了从 Kubernetes 群集收集的数据的类型，并提供了 Azure Monitor 用于影响成本的容器，并可根据使用情况进行自定义：

- Stdout，来自群集中每个 Kubernetes 命名空间中每个受监视容器的 stderr 容器日志

- 群集中的每个受监视容器中的容器环境变量

- 群集中的 Kubernetes 作业/pod，无需监视

- Active 抓取 of Prometheus 指标

- AKS 群集中的 Kubernetes 主节点日志的[诊断日志集合](../../aks/view-master-logs.md)，用于分析由主组件（例如*kube-apiserver*和*kube-manager*）生成的日志数据。

## <a name="what-is-collected-from-kubernetes-clusters"></a>从 Kubernetes 群集收集的内容

容器 Azure Monitor 包含一组预定义的度量值和收集的清单项，这些项是作为 Log Analytics 工作区中的日志数据写入的。 默认情况下，每隔一分钟收集下面列出的所有指标。

### <a name="node-metrics-collected"></a>已收集节点度量值

下表列出了每个节点的每个节点：

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- 使用 (磁盘) 
- 空闲 (磁盘) 
-  (磁盘 used_percent) 
- io_time (diskio) 
- 写入 (diskio) 
- 读取 (diskio) 
- write_bytes (diskio) 
- write_time (diskio) 
- iops_in_progress (diskio) 
- read_bytes (diskio) 
- read_time (diskio) 
- err_in (net) 
- err_out (net) 
- bytes_recv (net) 
- bytes_sent (net) 
- Kubelet_docker_operations (Kubelet) 

### <a name="container-metrics"></a>容器指标

下表列出了每个容器收集的8个指标：

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>群集清单

下面列出了默认收集的群集清单数据：

- 每个容器 KubePodInventory –每分钟1个
- KubeNodeInventory –每个节点每分钟1个
- KubeServices –每个服务每分钟1个
- ContainerInventory-每个容器每分钟1个

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>估计监视 AKS 群集的成本

下面的评估基于 Azure Kubernetes Service (AKS) 群集，其中包含以下大小示例。 而且，估计仅适用于收集的指标和清单数据。 对于容器日志 (stdout、stderr 和环境变量) ，它根据工作负荷生成的日志大小而变化，并从我们的估计中排除。

如果启用了对配置为如下所述的 AKS 群集的监视，

- 三个节点
- 每个节点两个磁盘
- 每个节点一个网络接口
- 20个 pod (每个容器中的一个容器 = 总共) 20 个容器
- 两个 Kubernetes 命名空间
- 五个 Kubernetes 服务 (包括 kube 系统箱、服务和命名空间) 
- 收集频率 = 60 秒 (默认值) 

可以在 "分配的 Log Analytics" 工作区中查看每小时生成的表和数据量。 有关其中每个表的详细信息，请参阅 [容器记录](container-insights-log-search.md#container-records)。

|表 | 大小估算 (MB/小时)  |
|------|---------------|
|性能 | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0.75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

Total = 31 MB/小时 = 23.1 GB/月 (一个月 = 31 天) 

使用 Log Analytics 的默认 [定价](https://azure.microsoft.com/pricing/details/monitor/) ，即即用即付模型，可以估算每月的 Azure Monitor 成本。 包含产能预留后，根据所选的预订，每月的价格会更高。

## <a name="controlling-ingestion-to-reduce-cost"></a>控制引入以降低成本

假设你的组织的不同业务部门共享 Kubernetes 基础结构和 Log Analytics 工作区的情况。 每个业务单位由 Kubernetes 命名空间分隔。 可以使用最近发布的工作簿直观显示每个工作区中引入的数据量。 使用[工作簿库](../platform/workbooks-overview.md#getting-started)中的**Container Insights 使用情况**工作簿，可帮助你直观显示数据源，而无需构建你自己的文档中共享的查询库。 在此工作簿中，有一些可供查看的图表，如以下所示：

- 按解决方案引入的计费数据总计（GB）

- 按容器 (应用程序日志的计费数据引入) 

- 按 Kubernetes 命名空间的计费容器日志数据引入

- 按群集名称隔离的可计费容器日志数据引入

- 按 logsource 条目的可计费容器日志数据引入

- 可计费诊断数据引入按诊断主节点日志

若要了解如何管理工作簿的权限和权限，请查看 [访问控制](../platform/workbooks-access-control.md)。

完成分析后，确定哪些源或源生成的数据或数据超出了要求，你可以重新配置数据收集。 [配置代理数据收集设置](container-insights-agent-config.md)一文中介绍了有关配置 stdout、stderr 和环境变量的集合的详细信息。

下面是可通过修改 ConfigMap 文件来帮助控制成本的更改的示例。

1. 通过在 ConfigMap 文件中修改以下内容，对群集中的所有命名空间禁用 stdout 日志：

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. 禁止从开发命名空间收集 stderr 日志 (例如， **开发测试**) ，并继续从其他命名空间收集 stderr 日志 (例如，通过在 ConfigMap 文件中修改以下内容来实现 **生产** 和 **默认**) ：

    >[!NOTE]
    >默认情况下，kube 日志收集处于禁用状态。 保留默认设置，将 **开发测试** 命名空间添加到排除命名空间列表应用于 stderr 日志收集。

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. 通过在 ConfigMap 文件中修改以下项来禁用整个群集中的环境变量集合。 这适用于每个 Kubernetes 命名空间中的所有容器。 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. 若要清理已完成的作业，请通过在 ConfigMap 文件中修改以下内容，在作业定义中指定清理策略：

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

将一个或多个这些更改应用到 ConfigMaps 后，请参阅 [应用更新的 ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap) ，将其应用到群集。

### <a name="prometheus-metrics-scraping"></a>Prometheus 指标抓取

如果使用 [Prometheus 指标抓取](container-insights-prometheus-integration.md)，请确保考虑以下因素，以限制从群集收集的指标数：

- 确保抓取 frequency 设置为最佳 (默认值为60秒) 。 虽然你可以将频率提高到15秒，但需确保抓取的度量值以该频率发布。 否则，会有许多重复的指标擦除 Log Analytics，并按间隔添加到数据引入和保留成本，但价值更小。 

- 容器 Azure Monitor 支持按指标名称排除 & 包含列表。 例如，如果你在群集中抓取 **kubedns** 指标，则默认情况下可能会获得数百个擦除，但你可能只对一个子集感兴趣。 确认指定了擦除的指标列表，或排除其他值，保存在数据引入卷上。 可以轻松启用抓取，而不使用其中的许多指标，这只会增加 Log Analytics 帐单收取的费用。

- 当抓取到 pod 注释时，请确保按命名空间进行筛选，以便从不 (使用的命名空间中排除 pod 指标的抓取（例如， **开发测试** 命名空间) ）。

## <a name="next-steps"></a>后续步骤

若要详细了解如何根据使用容器 Azure Monitor 收集的数据收集的最新用量模式，请参阅 [管理使用情况和估计成本](../platform/manage-cost-storage.md)。