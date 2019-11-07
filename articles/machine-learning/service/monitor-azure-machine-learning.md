---
title: 监视 Azure 机器学习 |Microsoft Docs
description: 了解如何使用 Azure Monitor 从 Azure 机器学习查看、分析和创建有关指标的警报。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/04/2019
ms.openlocfilehash: d9b7a433dd7392e83719f09815f736dbd85f9c7f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582894"
---
# <a name="monitoring-azure-machine-learning"></a>监视 Azure 机器学习

本文介绍 Azure 机器学习生成的监视数据。 还介绍了如何使用 Azure Monitor 分析数据并定义警报。

> [!TIP]
> 本文档中的信息主要面向管理员，因为它描述了对 Azure 机器学习服务的监视。 如果你是一名数据科学家或开发人员，并且想要监视特定于模型定型运行的信息，请参阅以下文档：
>
> * [启动、监视和取消定型运行](how-to-manage-runs.md)
> * [用于定型运行的日志指标](how-to-track-experiments.md)
> * [跟踪 eexperiments 与 MLflow](how-to-use-mlflow.md)
> * [用 TensorBoard 进行可视化运行](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure 机器学习使用 Azure Monitor 来记录监视数据，这是 Azure 中的一个完整 stack 监视服务。 Azure Monitor 提供了一组完整的功能来监视 Azure 资源。 它还可以监视其他云和本地的资源。

从[Azure Monitor 概述](/azure/azure-monitor/overview)一文开始，其中概述了监视功能。 以下各节通过使用 Azure Monitor 与 Azure 机器学习提供具体的 oo，对此信息进行构建。

要了解与 Azure Monitor 相关的成本，请参阅[使用情况和预估成本](/azure/azure-monitor/platform/usage-estimated-costs)。 若要了解在 Azure Monitor 中显示数据所用的时间，请参阅[记录数据引入时间](/azure/azure-monitor/platform/data-ingestion-time)。

## <a name="monitoring-data-from-azure-machine-learning"></a>监视 Azure 机器学习中的数据

Azure 机器学习会收集与其他 Azure 资源相同的监视数据，如[监视 Azure 资源的数据](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources)中所述。 有关 Azure 机器学习创建的日志和指标的详细参考信息，请参阅[Azure 机器学习监视数据参考](monitor-resource-reference.md)。

## <a name="analyzing-metric-data"></a>分析指标数据

可以通过从 " **Azure Monitor** " 菜单中打开**指标**来分析 Azure 机器学习的指标。 有关使用此工具的详细信息，请参阅[Azure 指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)入门。

Azure 机器学习的所有指标位于命名空间**机器学习服务工作区**中。

![已选择机器学习服务工作区指标资源管理器](media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>筛选和拆分

对于支持维度的度量值，您可以使用维度值应用筛选器。 例如，筛选**群集名称**为 `cpu-cluster`的**活动内核**。 

您还可以按维度拆分度量值，以可视化不同的度量段之间的比较情况。 例如，拆分**管道步骤类型**以查看管道中使用的步骤类型的计数。

有关筛选和拆分的详细信息，请参阅[Azure Monitor 的高级功能](/azure/azure-monitor/platform/metrics-charts)。

## <a name="alerts"></a>警报

您可以通过从 " **Azure Monitor** " 菜单中打开**警报**来访问 Azure 机器学习警报。 有关创建警报的详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](/azure/azure-monitor/platform/alerts-metric)。

下表列出了 Azure 机器学习的常见和建议的指标警报规则：

| 警报类型 | 条件 | 说明 |
|:---|:---|:---|
| 模型部署失败 | 聚合类型： Total、Operator：大于，阈值：0 | 当一个或多个模型部署失败时 |
| 配额使用率百分比 | 聚合类型： Average、Operator：大于、阈值：90| 当配额使用率百分比大于90% 时 |
| 不可用节点 | 聚合类型： Total、Operator：大于，阈值：0 | 存在一个或多个不可用的节点时 |

## <a name="configuration"></a>配置

> [!IMPORTANT]
> __不需要配置 Azure 机器学习的指标__，它们会自动收集，并且在指标资源管理器中可用于监视和警报。

可以添加诊断设置来配置以下功能：

* 将日志和指标信息存档到 Azure 存储帐户。
* 将日志和指标信息流式传输到 Azure 事件中心。
* 将日志和指标信息发送到 Azure Monitor Log Analytics。

启用这些设置需要额外的 Azure 服务（存储帐户、事件中心或 Log Analytics），这可能会增加成本。 若要计算估算成本，请访问[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。

有关创建诊断设置的详细信息，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](/azure/azure-monitor/platform/diagnostic-settings)。

你可以配置以下 Azure 机器学习的日志：

| Category | 说明 |
|:---|:---|
| AmlComputeClusterEvent | Azure 机器学习计算群集中的事件。 |
| AmlComputeClusterNodeEvent | Azure 机器学习计算群集中的节点的事件。 |
| AmlComputeJobEvent | Azure 机器学习计算上运行的作业的事件。 |

> [!NOTE]
> 当你在诊断设置中启用指标时，维度信息当前不包含为发送到存储帐户、事件中心或 log analytics 的信息的一部分。

## <a name="analyzing-log-data"></a>分析日志数据

使用 Azure Monitor Log Analytics 需要你创建诊断配置并启用__将信息发送到 Log Analytics__。 有关详细信息，请参阅[配置](#configuration)部分。

Azure Monitor 日志中的数据存储在表中，每个表都具有自己的唯一属性集。 Azure 机器学习将数据存储在下表中：

| 表 | 说明 |
|:---|:---|
| AmlComputeClusterEvent | Azure 机器学习计算群集中的事件。 |
| AmlComputeClusterNodeEvent | Azure 机器学习计算群集中的节点的事件。 |
| AmlComputeJobEvent | Azure 机器学习计算上运行的作业的事件。 |

> [!IMPORTANT]
> 从 "Azure 机器学习" 菜单中选择 "**日志**" 时，会打开 Log Analytics 并将查询范围设置为当前工作区。 这意味着日志查询只包含来自该资源的数据。 如果要运行的查询包含来自其他数据库或其他 Azure 服务的数据，请从 " **Azure Monitor** " 菜单中选择 "**日志**"。 有关详细信息，请参阅[Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

有关日志和指标的详细参考信息，请参阅[Azure 机器学习监视数据参考](monitor-resource-reference.md)。

### <a name="sample-queries"></a>示例查询

下面是一些可用于帮助监视 Azure 机器学习资源的查询： 

+ 在过去五天内获取失败的作业：

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 获取特定作业名称的记录：

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 对于 VM 大小为 Standard_D1_V2 的群集，在过去五天内获取群集事件：

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 获取最近8天内分配的节点：

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>后续步骤

- 有关日志和指标的参考，请参阅[Azure 机器学习监视数据参考](monitor-resource-reference.md)。
- 有关使用与 Azure 机器学习相关的配额的信息，请参阅[管理和请求 Azure 资源的配额](how-to-manage-quotas.md)。
- 有关监视 Azure 资源的详细信息，请参阅[监视具有 Azure Monitor 的 azure 资源](/azure/azure-monitor/insights/monitor-azure-resource)。
