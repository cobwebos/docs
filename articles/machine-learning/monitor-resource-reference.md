---
title: 监视数据引用 |Microsoft Docs
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习收集的数据和资源，以及 Azure Monitor 中可用的资源。 Azure Monitor 收集和显示有关 Azure 机器学习工作区的数据，并允许您查看指标、设置警报和分析记录的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/06/2019
ms.openlocfilehash: bbc62aa153e6ecb5d9ae65c37392c2697b9beaa2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979710"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure 机器学习监视数据引用

了解 Azure Monitor 从 Azure 机器学习工作区收集的数据和资源。 有关收集和分析监视数据的详细信息，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="resource-logs"></a>资源日志

下表列出了在 Azure Monitor 日志或 Azure 存储中收集 Azure 机器学习资源日志时这些资源的属性。

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents 表

| 属性 | Description |
|:--- |:---|
| TimeGenerated | 生成日志条目的时间 |
| OperationName | 与日志事件关联的操作的名称 |
| 类别 | 日志事件的名称，AmlComputeClusterNodeEvent |
| JobId | 已提交作业的 ID |
| ExperimentId | 试验的 ID |
| ExperimentName | 试验的名称 |
| CustomerSubscriptionId | 已提交的试验和作业的 SubscriptionId |
| WorkspaceName | 机器学习工作区的名称 |
| ClusterName | 群集的名称 |
| ProvisioningState | 作业提交状态 |
| ResourceGroupName | 资源组的名称 |
| JobName | 作业的名称 |
| ClusterId | 群集的 ID |
| EventType | 作业事件的类型，例如 JobSubmitted、JobRunning、JobFailed、JobSucceeded，等等。 |
| ExecutionState | 作业（运行）的状态，例如已排队、正在运行、成功、失败 |
| ErrorDetails | 作业错误详细信息 |
| CreationApiVersion | 用于创建作业的 Api 版本 |
| ClusterResourceGroupName | 群集的资源组名称 |
| TFWorkerCount | TF 辅助角色计数 |
| TFParameterServerCount | TF 参数服务器的计数 |
| ToolType | 使用的工具类型 |
| RunInContainer | 描述作业是否应在容器中运行的标志 |
| JobErrorMessage | 作业错误详细消息 |
| NodeId | 作业运行所在的节点的 ID |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents 表

| 属性 | Description |
|:--- |:--- |
| TimeGenerated | 生成日志条目的时间 |
| OperationName | 与日志事件关联的操作的名称 |
| 类别 | 日志事件的名称，AmlComputeClusterNodeEvent |
| ProvisioningState | 群集的预配状态 |
| ClusterName | 群集的名称 |
| ClusterType | 群集的类型 |
| CreatedBy | 创建群集的用户 |
| CoreCount | 群集中的内核计数 |
| VmSize | 群集的 Vm 大小 |
| VmPriority | 在群集专用/LowPriority 内创建的节点的优先级 |
| ScalingType | 群集缩放手动/自动 |
| InitialNodeCount | 群集的初始节点计数 |
| MinimumNodeCount | 群集的最小节点计数 |
| MaximumNodeCount | 群集的最大节点计数 |
| NodeDeallocationOption | 应如何解除分配节点 |
| 发布者 | 群集类型的发布者 |
| 产品 | 用于创建群集的产品/服务 |
| Sku | 在群集内创建的节点/VM 的 Sku |
| 版本 | 创建节点/VM 时使用的映像的版本 |
| SubnetId | 群集的 SubnetId |
| AllocationState | 群集分配状态 |
| CurrentNodeCount | 群集的当前节点计数 |
| TargetNodeCount | 群集的目标节点计数，同时增加/减少 |
| EventType | 群集创建期间的事件类型。 |
| NodeIdleTimeSecondsBeforeScaleDown | 缩减群集之前的空闲时间（以秒为单位） |
| PreemptedNodeCount | 群集的已抢占节点计数 |
| IsResizeGrow | 指示群集正在向上扩展的标志 |
| VmFamilyName | 可在群集内创建的节点的 VM 系列的名称 |
| LeavingNodeCount | 正在保留群集的节点计数 |
| UnusableNodeCount | 群集的不能使用的节点计数 |
| IdleNodeCount | 群集的空闲节点计数 |
| RunningNodeCount | 正在运行群集的节点计数 |
| PreparingNodeCount | 正在准备群集的节点计数 |
| QuotaAllocated | 分配给群集的配额 |
| QuotaUtilized | 群集使用的配额 |
| AllocationStateTransitionTime | 将时间从一种状态转换为另一种状态 |
| ClusterErrorCodes | 群集创建或扩展期间收到错误代码 |
| CreationApiVersion | 创建群集时使用的 Api 版本 |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents 表

| 属性 | Description |
|:--- |:--- |
| TimeGenerated | 生成日志条目的时间 |
| OperationName | 与日志事件关联的操作的名称 |
| 类别 | 日志事件的名称，AmlComputeClusterNodeEvent |
| ClusterName | 群集的名称 |
| NodeId | 创建的群集节点的 ID |
| VmSize | 节点的 Vm 大小 |
| VmFamilyName | 节点所属的 Vm 系列 |
| VmPriority | 已创建专用/LowPriority 节点的优先级 |
| 发布者 | Vm 映像的发布者，例如 microsoft-dsvm |
| 产品 | 与 VM 创建相关联的产品/服务 |
| Sku | 已创建的节点/VM 的 Sku |
| 版本 | 创建节点/VM 时使用的映像的版本 |
| ClusterCreationTime | 创建群集的时间 |
| ResizeStartTime | 群集扩展/启动的时间 |
| ResizeEndTime | 群集扩展/关闭的时间结束 |
| NodeAllocationTime | 分配节点的时间 |
| NodeBootTime | 节点启动的时间 |
| StartTaskStartTime | 向节点分配任务并启动任务的时间 |
| StartTaskEndTime | 分配到节点的任务结束的时间 |
| TotalE2ETimeInSeconds | 总时间节点处于活动状态 |

### <a name="metrics"></a>度量值

下表列出了为 Azure 机器学习收集的所有指标存储在命名空间**Azure 机器学习工作区**中的平台指标。

**Model**

| 度量值 | 单位 | Description |
| ----- | ----- | ----- |
| 模型部署失败 | 计数 | 失败的模型部署数。 |
| 模型部署已启动 | 计数 | 开始部署的模型数。 |
| 模型部署成功 | 计数 | 成功的模型部署数。 |
| 模型注册失败 | 计数 | 失败的模型注册数。 |
| 模型注册成功 | 计数 | 成功的模型注册数。 |

**配额**

配额信息仅用于 Azure 机器学习计算。

| 度量值 | 单位 | Description |
| ----- | ----- | ----- |
| 活动核心 | 计数 | 活动计算核心数。 |
| 活动节点 | 计数 | 活动节点数。 |
| 空闲内核数 | 计数 | 空闲计算核心数。 |
| 空闲节点 | 计数 | 空闲计算节点数。 |
| 正在离开内核 | 计数 | 离开内核的数量。 |
| 退出节点 | 计数 | 正在离开的节点数。 |
| 抢先核心 | 计数 | 被占用的内核数。 |
| 抢占节点 | 计数 | 被抢占的节点数。 |
| 配额使用率百分比 | 百分比 | 使用的配额的百分比。 |
| 总核心数 | 计数 | 内核总数。 |
| 节点总数 | 计数 | 节点总数。 |
| 不可用内核 | 计数 | 无法使用的内核数。 |
| 不可用节点 | 计数 | 无法使用的节点数。 |

下面是可用于筛选配额指标的维度：

| 维度 | 可用的指标 | Description |
| ---- | ---- | ---- |
| 群集名称 | 所有配额指标 | 计算实例的名称。 |
| Vm 系列名称 | 配额使用率百分比 | 群集使用的 VM 系列的名称。 |
| Vm 优先级 | 配额使用率百分比 | VM 的优先级。

**运行**

有关定型运行的信息。

| 度量值 | 单位 | Description |
| ----- | ----- | ----- |
| 已完成运行 | 计数 | 已完成的运行数。 |
| 失败的运行 | 计数 | 失败的运行数。 |
| 已开始运行 | 计数 | 已启动的运行数。 |

下面是可用于筛选运行度量值的维度：

| 维度 | Description |
| ---- | ---- |
| ComputeType | 运行的计算类型。 |
| PipelineStepType | 运行中使用的[PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py)的类型。 |
| PublishedPipelineId | 运行时使用的已发布管道的 ID。 |
| RunType | 运行的类型。 |

RunType 维度的有效值为：

| 值 | Description |
| ----- | ----- |
| 试验 | 非管道运行。 |
| PipelineRun | 管道运行，它是 StepRun 的父级。 |
| StepRun | 管道步骤的运行。 |
| ReusedStepRun | 重新使用之前运行的管道步骤的运行。 |

## <a name="see-also"></a>另请参阅

- 有关监视 Azure 机器学习的说明，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。
- 有关监视 Azure 资源的详细信息，请参阅[监视 azure 资源的 Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) 。
