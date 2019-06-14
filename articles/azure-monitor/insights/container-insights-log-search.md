---
title: 如何查询日志从 Azure Monitor 的容器 |Microsoft Docs
description: 用于容器的 azure Monitor 收集指标和日志数据和这篇文章介绍记录并包含的示例查询。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60494758"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>如何查询 Azure 监视器中的容器的日志
用于容器的 azure Monitor 从容器主机和容器，收集性能指标、 清单数据和运行状况状态信息并将其转发到 Azure Monitor 中的 Log Analytics 工作区。 每隔三分钟收集数据。 此数据是可用于[查询](../../azure-monitor/log-query/log-query-overview.md)Azure 监视器中。 此数据可应用于包括迁移计划、容量分析、发现和按需性能故障排除在内的方案。

## <a name="container-records"></a>容器记录

下表显示适用于容器的 Azure Monitor 收集的记录以及日志搜索结果中显示的数据类型的示例：

| 数据类型 | 日志搜索中的数据类型 | 字段 |
| --- | --- | --- |
| 主机和容器的性能 | `Perf` | 计算机、ObjectName、CounterName（处理器时间百分比、磁盘读取 MB、磁盘写入 MB、内存使用 MB、网络接收字节数、网络发送字节数、处理器使用秒数、网络）、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器库存 | `ContainerInventory` | TimeGenerated、计算机、容器名称、ContainerHostname、映像、ImageTag、ContainerState、ExitCode、EnvironmentVar、命令、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器日志 | `ContainerLog` | TimeGenerated、计算机、映像 ID、容器名称、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器节点清单 | `ContainerNodeInventory`| TimeGenerated、计算机、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes 群集中的 Pod 清单 | `KubePodInventory` | TimeGenerated、 计算机、 ClusterId、 ContainerCreationTimeStamp、 PodUid、 PodCreationTimeStamp、 ContainerRestartCount、 PodRestartCount、 PodStartTime、 ContainerStartTime、 ServiceName、 ControllerKind、 ControllerName、 名称， ContainerStatusReason、 ContainerID、 容器名称、 名称、 PodLabel、 Namespace、 PodStatus、 ClusterName、 PodIp、 SourceSystem |
| Kubernetes 群集节点部分清单 | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes 事件 | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes 群集中的服务 | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes 群集节点部分的性能指标 | Perf &#124; where ObjectName == “K8SNode” | 计算机、 ObjectName、 CounterName &#40;cpuAllocatableBytes，memoryAllocatableBytes，cpuCapacityNanoCores，memoryCapacityBytes，memoryRssBytes，cpuUsageNanoCores，memoryWorkingsetBytes，restartTimeEpoch&#41;，CounterValue、TimeGenerated、 CounterPath、 SourceSystem | 
| Kubernetes 群集容器部分的性能指标 | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; cpuRequestNanoCores，memoryRequestBytes，cpuLimitNanoCores，memoryWorkingSetBytes，restartTimeEpoch，cpuUsageNanoCores，memoryRssBytes&#41;，CounterValue、 TimeGenerated、 CounterPath、 SourceSystem | 
| InsightsMetrics | 计算机名称、 Namespace、 源、 SourceSystem、 标记、 TimeGenerated，键入，值 |

## <a name="search-logs-to-analyze-data"></a>搜索日志以分析数据
Azure Monitor 日志可以帮助您查找所的趋势，诊断瓶颈，预测，或相关联的数据可帮助您确定是否以最佳方式执行的当前群集配置。 提供预定义日志搜索，可直接使用，也可通过自定义来按自己想要的方式返回信息。 

通过在预览窗格中选择“查看 Kubernetes 事件日志”或“查看容器日志”选项，对工作区中的数据执行交互式分析   。 “日志搜索”页面在用户所处的 Azure 门户页面的右侧显示  。

![在 Log Analytics 中分析数据](./media/container-insights-analyze/container-health-log-search-example.png)   

转发到你的工作区的容器日志输出为 STDOUT 和 STDERR。 由于 Azure Monitor 正在监视 Azure 托管的 Kubernetes (AKS)，目前因生成了大量数据而不收集 Kube-system。 

### <a name="example-log-search-queries"></a>日志搜索查询示例
从一两个示例开始生成查询，然后修改它们以适应需求的做法通常很有用。 可使用以下示例查询进行试验，帮助生成更高级的查询：

| 查询 | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出容器的所有生命周期信息| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清单 | 
| 选择“折线图”显示选项  ：<br> 性能<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| 选择“折线图”显示选项  ：<br> 性能<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器内存 |

## <a name="next-steps"></a>后续步骤
用于容器的 azure 监视器不包括一组预定义的警报。 审阅[适用于容器的 Azure Monitor 创建性能警报](container-insights-alerts.md)若要了解如何创建高的 CPU 和内存使用率，以支持 DevOps 或操作流程和过程建议的警报。 