---
title: 监视 Azure 机器学习 | Microsoft Docs
description: 了解如何使用 Azure Monitor 查看、分析和创建有关 Azure 机器学习中指标的警报。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399106"
---
# <a name="monitoring-azure-machine-learning"></a>监视 Azure 机器学习

本文介绍 Azure 机器学习生成的监视数据。 还介绍了如何使用 Azure Monitor 分析数据并定义警报。

> [!TIP]
> 本文档中的信息主要面向管理员，因为它主要介绍针对 Azure 机器学习的监视。 如果你是一名数据科学家或开发人员，并且想要监视与模型训练运行相关的信息，请参阅以下文档：
>
> * [启动、监视和取消训练运行](how-to-manage-runs.md)
> * [记录训练运行的指标](how-to-track-experiments.md)
> * [使用 MLflow 对试验进行跟踪](how-to-use-mlflow.md)
> * [使用 TensorBoard 将运行可视化](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure 机器学习使用 Azure Monitor 来记录监视数据，这是 Azure 中的一个完整堆栈监视服务。 Azure Monitor 提供了一组用于监视 Azure 资源的完整功能。 它还可以监视其他云和本地的资源。

请先阅读文章 [Azure Monitor 概述](/azure/azure-monitor/overview)，对监视功能有一个大致了解。 以下各节通过提供将 Azure 监视器与 Azure 机器学习一起使用的详细信息来构建此信息。

若想了解与 Azure Monitor 相关的成本，请参阅[使用情况和估计成本](/azure/azure-monitor/platform/usage-estimated-costs)。 要了解数据在 Azure 监视器中显示所需的时间，请参阅[日志数据引入时间](/azure/azure-monitor/platform/data-ingestion-time)。

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure 机器学习的监视数据

Azure 机器学习收集的监视数据的类型与其他 Azure 资源相同，[Azure 机器学习的监视数据](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)中介绍了这一点。 请参阅 [Azure 机器学习监视数据参考](monitor-resource-reference.md)，详细了解 Azure 机器学习创建的日志和指标。

## <a name="analyzing-metric-data"></a>分析指标数据

可以通过从“Azure Monitor”**** 菜单中打开“指标”****，来分析 Azure 机器学习的指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](/azure/azure-monitor/platform/metrics-getting-started)。

Azure 机器学习的所有指标都位于命名空间**机器学习服务工作区**中。

![机器学习服务工作区处于选定状态的指标资源管理器](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>筛选和拆分

对于支持维度的指标，应用筛选器时可以使用维度值。 例如，筛选“群集名称”**** 为 `cpu-cluster` 的“活动核心”****。 

还可以按维度来拆分指标，将指标的不同部分进行直观比较。 例如，拆分**管道步骤类型**，查看管道中使用的步骤类型的计数。

有关筛选和拆分的详细信息，请参阅 [Azure Monitor 的高级功能](/azure/azure-monitor/platform/metrics-charts)。

## <a name="alerts"></a>警报

可以通过从“Azure Monitor”**** 菜单中打开“警报”****，来访问 Azure 机器学习的警报。 请参阅[使用 Azure Monitor 创建、查看和管理指标警报](/azure/azure-monitor/platform/alerts-metric)，详细了解如何创建警报。

下表列出了常见和推荐使用的 Azure 机器学习的指标警报规则：

| 警报类型 | 条件 | 描述 |
|:---|:---|:---|
| 失败的模型部署数 | 聚合类型：总计，运算符：大于，阈值：0 | 当一个或多个模型部署失败时 |
| 配额利用率百分比 | 聚合类型：平均值，运算符：大于阈值：90| 当配额使用率百分比大于 90% 时 |
| 不可用的节点数 | 聚合类型：总计，运算符：大于，阈值：0 | 当存在一个或多个不可用的节点时 |

## <a name="configuration"></a>Configuration

> [!IMPORTANT]
> __无需配置 Azure 机器学习的指标__，系统会自动收集指标，并在指标资源管理器中用于监视和警报。

可以添加诊断设置来配置以下功能：

* 将日志和指标信息存档到 Azure 存储帐户。
* 将日志和指标信息流式传输到 Azure 事件中心。
* 将日志和指标信息发送到 Azure Monitor Log Analytics。

启用这些设置需要额外的 Azure 服务（存储帐户、事件中心或 Log Analytics），这可能会增加成本。 若要估算成本，请访问 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。

有关创建诊断设置的详细信息，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](/azure/azure-monitor/platform/diagnostic-settings)。

可以为 Azure 机器学习配置以下日志：

| 类别 | 描述 |
|:---|:---|
| AmlComputeClusterEvent | Azure 机器学习计算群集的事件。 |
| AmlComputeClusterNodeEvent | Azure 机器学习计算群集内节点的事件。 |
| AmlComputeJobEvent | Azure 机器学习计算上运行的作业的事件。 |

> [!NOTE]
> 启用诊断设置中的指标时，当前发送到存储帐户、事件中心或 log analytics 的信息中并不包含维度信息。

## <a name="analyzing-log-data"></a>分析日志数据

使用 Azure Monitor Log Analytics 需要创建诊断配置，并启用__将信息发送到 Log Analytics__。 有关详细信息，请参阅[配置](#configuration)部分。

Azure Monitor 日志中的数据以表形式存储，每个表包含自己独有的属性集。 Azure 机器学习将数据存储在以下表格中：

| 表 | 描述 |
|:---|:---|
| AmlComputeClusterEvent | Azure 机器学习计算群集的事件。 |
| AmlComputeClusterNodeEvent | Azure 机器学习计算群集内节点的事件。 |
| AmlComputeJobEvent | Azure 机器学习计算上运行的作业的事件。 |

> [!IMPORTANT]
> 在 Azure 机器学习菜单中选择“日志”**** 时，Log Analytics 随即打开，其查询范围设置为当前工作区。 这意味着日志查询只包含来自该资源的数据。 如果希望运行的查询包含其他数据库或其他 Azure 服务的数据，请从“Azure Monitor”**** 菜单中选择“日志”****。 请查看 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)了解详细信息。

请参阅 [Azure 机器学习监视数据参考](monitor-resource-reference.md)，详细了解相关日志和指标。

### <a name="sample-queries"></a>示例查询

下面是一些可用于帮助监视 Azure 机器学习资源的查询： 

+ 获取过去五天内失败的作业：

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 获取特定名称的作业的记录：

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 为 VM 大小为 Standard_D1_V2 的群集获取过去五天内的群集事件：

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 获取最近 8 天内分配的节点：

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 机器学习监视数据参考](monitor-resource-reference.md)，了解相关日志和指标。
- 有关如何使用与 Azure 机器学习相关的配额的信息，请参阅[管理和请求 Azure 资源配额](how-to-manage-quotas.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](/azure/azure-monitor/insights/monitor-azure-resource)。
