---
title: 如何从 Azure Monitor 为容器查询日志 |Microsoft Docs
description: 容器 Azure Monitor 收集指标和日志数据，本文介绍了这些记录，并包括示例查询。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: c3a034776b32db57f70ddee960c1cd5fc96b170b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555407"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>如何从容器 Azure Monitor 查询日志

容器 Azure Monitor 从容器主机和容器中收集性能指标、清单数据和健康状况信息，并将其转发到 Azure Monitor 中的 Log Analytics 工作区。 每隔三分钟收集数据。 此数据可用于在 Azure Monitor 中进行[查询](../../azure-monitor/log-query/log-query-overview.md)。 此数据可应用于包括迁移计划、容量分析、发现和按需性能故障排除在内的方案。

## <a name="container-records"></a>容器记录

下表显示适用于容器的 Azure Monitor 收集的记录以及日志搜索结果中显示的数据类型的示例：

| 数据类型 | 日志搜索中的数据类型 | 字段 |
| --- | --- | --- |
| 主机和容器的性能 | `Perf` | 计算机、ObjectName、CounterName（处理器时间百分比、磁盘读取 MB、磁盘写入 MB、内存使用 MB、网络接收字节数、网络发送字节数、处理器使用秒数、网络）、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器库存 | `ContainerInventory` | TimeGenerated、计算机、容器名称、ContainerHostname、映像、ImageTag、ContainerState、ExitCode、EnvironmentVar、命令、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器日志 | `ContainerLog` | TimeGenerated、计算机、映像 ID、容器名称、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器节点清单 | `ContainerNodeInventory`| TimeGenerated、计算机、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes 群集中的 Pod 清单 | `KubePodInventory` | TimeGenerated、Computer、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、容器状态、 ContainerStatusReason、ContainerID、、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| Kubernetes 群集节点部分清单 | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes 事件 | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes 群集中的服务 | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes 群集节点部分的性能指标 | Perf &#124; where ObjectName == “K8SNode” | Computer、ObjectName、CounterName &#40;CpuAllocatableBytes、MemoryAllocatableBytes、CpuCapacityNanoCores、MemoryCapacityBytes、MemoryRssBytes、CpuUsageNanoCores、MemoryWorkingsetBytes、restartTimeEpoch&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes 群集容器部分的性能指标 | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; CpuRequestNanoCores、MemoryRequestBytes、CpuLimitNanoCores、MemoryWorkingSetBytes、RestartTimeEpoch、CpuUsageNanoCores、memoryRssBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| 自定义指标 |`InsightsMetrics` | 计算机、名称、命名空间、源、SourceSystem、标记<sup>1</sup>、TimeGenerated、类型、Va、_ResourceId | 

<sup>1</sup> "*标记*" 属性表示对应指标的[多个维度](../platform/data-platform-metrics.md#multi-dimensional-metrics)。 有关在 `InsightsMetrics` 表中收集和存储的指标以及记录属性的说明的其他信息，请参阅[InsightsMetrics 概述](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)。

>[!NOTE]
>目前，对 Prometheus 的支持是公共预览版中的一项功能。
>

## <a name="search-logs-to-analyze-data"></a>搜索日志以分析数据

Azure Monitor 日志可帮助你查找趋势、诊断瓶颈、预测或关联数据，这些数据可帮助你确定当前群集配置是否以最佳状态执行。 提供预定义日志搜索，可直接使用，也可通过自定义来按自己想要的方式返回信息。

通过在预览窗格中选择“查看 Kubernetes 事件日志”或“查看容器日志”选项，对工作区中的数据执行交互式分析。 “日志搜索”页面在用户所处的 Azure 门户页面的右侧显示。

![在 Log Analytics 中分析数据](./media/container-insights-analyze/container-health-log-search-example.png)   

容器将转发到你的工作区的输出记录为 STDOUT 和 STDERR。 由于 Azure Monitor 正在监视 Azure 托管的 Kubernetes (AKS)，目前因生成了大量数据而不收集 Kube-system。 

### <a name="example-log-search-queries"></a>日志搜索查询示例

从一两个示例开始生成查询，然后修改它们以适应需求的做法通常很有用。 可使用以下示例查询进行试验，帮助生成更高级的查询：

| Query | 描述 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出容器的所有生命周期信息| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清单 | 
| 选择“折线图”显示选项：<br> 性能<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| 选择“折线图”显示选项：<br> 性能<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器内存 |
| InsightsMetrics<br> &#124;where Name = = "requests_count"<br> &#124;汇总 Val = any （Val） by TimeGenerated = bin （TimeGenerated，1m）<br> &#124;按 TimeGenerated asc 排序<br> &#124;项目 RequestsPerMinute = Val-前（Val），TimeGenerated <br> &#124;呈现 barchart  | 每分钟个请求，含自定义指标 |

下面的示例是一个 Prometheus 指标查询。 收集的度量值是计数，若要确定在特定时间段内发生了多少错误，必须从计数中减去。 数据集按*partitionKey*分区，这意味着，对于每个唯一的*名称*、*主机名*和*OperationType*集，我们在该集上运行一个子查询，该查询通过*TimeGenerated*对日志进行排序。查找先前的*TimeGenerated*和记录时间的计数，以确定速率。

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

输出将显示类似于以下内容的结果：

![数据引入卷的日志查询结果](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>后续步骤

容器 Azure Monitor 不包含一组预定义的警报。 查看[使用容器 Azure Monitor 创建性能警报](container-insights-alerts.md)，了解如何为高 CPU 和内存使用率创建建议警报，以支持 DevOps 或操作过程和过程。 
