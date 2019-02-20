---
title: 使用用于容器的 Azure Monitor 创建性能警报 | Microsoft Docs
description: 本文介绍如何在用于容器的 Azure Monitor 中基于内存和 CPU 利用率日志查询创建自定义 Azure 警报。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238834"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>如何在用于容器的 Azure Monitor 中针对性能问题设置警报
用于容器的 Azure Monitor 可监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 

本文介绍如何在群集节点上的处理器和内存利用率超过定义的阈值时启用警报。

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>基于群集 CPU 或内存利用率创建警报
若要在群集的 CPU 或内存利用率较高时发出警报，可以基于提供的日志查询创建指标度量警报规则。 查询使用 `now` 运算符将某个日期时间与当前时间进行比较，并将时间推后一个小时。 用于容器的 Azure Monitor 存储的所有日期均采用 UTC 格式。  

在开始之前，如果你不熟悉 Azure Monitor 中的警报，请参阅 [Microsoft Azure 中的警报概述](../platform/alerts-overview.md)。 若要详细了解使用日志查询的警报，请参阅 [Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户的左窗格中选择“监视”。 在“见解”部分，选择“容器”。    
3. 在“监视的群集”选项卡上，单击列表中的某个群集名称以选择该群集。
4. 在“监视”部分下的左窗格中，选择“日志”打开 Azure Monitor 日志页上，用于编写和执行 Azure Log Analytics 查询。
5. 在“日志”页上，单击“+ 新建警报规则”。
6. 在“条件”部分下，单击预定义的自定义日志条件“每当自定义日志搜索为 <logic undefined> 时”。 系统会自动选择“自定义日志搜索”信号类型，因为警报规则的创建是直接从 Azure Monitor 日志页发起的。  
7. 将以下查询之一粘贴到“搜索查询”字段。 以下查询每隔一分钟计算平均 CPU 利用率作为成员节点的平均 CPU 利用率。

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    以下查询每隔一分钟计算平均内存利用率作为成员节点的平均内存利用率。

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. 使用以下信息配置警报：

    a. 从“基于”下拉列表中选择“指标度量”。 指标度量将为查询中其值超出指定阈值的每个对象创建一个警报。  
    b. 对于“条件”，请选择“大于”并输入 **75** 作为初始基线**阈值**，或输入符合条件的值。  
    c. 在“触发警报的条件”部分选择“连续违规”，然后从下拉列表中选择“大于”并输入 **2** 作为值。  
    d. 在“评估依据”部分下，将“时段”值修改为 60 分钟。 此规则将每隔五分钟运行一次，返回从当前时间算起过去一小时内创建的记录。 将时间段设置为更宽的时间窗口可以解决数据延迟的可能性，并确保查询返回数据以避免警报永远不会触发的漏报。 

9. 单击“完成”，完成警报规则。
10. 在“警报规则名称”字段中提供警报的名称。 指定“说明”以详细描述该警报的具体信息，并从提供的选项中选择相应的严重性。
11. 若要在创建后立即激活警报规则，请接受“创建后启用规则”选项的默认值。
12. 在最后一个步骤中，选择现有的或创建新的**操作组**，确保每次触发警报时都执行相同的操作，而且这些操作可用于定义的每个规则。 请根据 IT 或 DevOps 运营部门管理事件的方式进行配置。 
13. 单击“创建警报规则”，完成警报规则。 该警报会立即开始运行。

## <a name="next-steps"></a>后续步骤

* 回顾一些[记录查询示例](container-insights-analyze.md#search-logs-to-analyze-data)，以了解可以评估和使用的，或者可针对其他警报方案自定义的预定义查询或示例。 
* 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)
