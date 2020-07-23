---
title: 用于容器的 Azure Monitor 运行状况监视器配置 | Microsoft Docs
description: 本文介绍了用于容器的 Azure Monitor 中运行状况监视器的配置详细信息。
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80055702"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>用于容器的 Azure Monitor 运行状况监视器配置指南

在用于容器的 Azure Monitor 中，监视器是衡量运行状况和检测错误的主要元素。 本文可帮助你了解用于衡量运行状况的概念，以及组成运行状况模型的元素，学习这些内容后，你将能够使用[运行状况（预览版）](container-insights-health.md)功能监视和报告 Kubernetes 群集的运行状况。

>[!NOTE]
>运行状况功能目前处于公开预览状态。
>

## <a name="monitors"></a>监视器

监视器衡量托管对象某个方面的运行状况。 每个监视器都有两到三个运行状况状态。 在任何给定的时间，监视器都处于唯一一个可能的状态。 当容器化代理加载监视器后，它将初始化为正常状态。 仅当检测到另一个状态的指定条件时，状态才会更改。

特定对象的总体运行状况由其每个监视器的运行状况确定。 在用于容器的 Azure Monitor 中，此层次结构在“运行状况层次结构”窗格中显示。 有关如何汇总运行状况的策略在聚合监视器的配置部分介绍。

## <a name="types-of-monitors"></a>监视器类型

|监视 | 说明 | 
|--------|-------------|
| 单元监视器 |单元监视器衡量资源或应用程序的某些方面。 可能是检查性能计数器以确定资源的性能或其可用性。 |
|聚合监视器 | 聚合监视器将多个监视器组合在一起，以提供一个聚合运行状况状态。 单元监视器通常在特定的聚合监视器下配置。 例如，节点聚合监视器汇总节点 CPU 利用率、内存利用率和节点状态这三个状态。
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>聚合监视器运行状况汇总策略

每个聚合监视器都定义了一个运行状况汇总策略，这是一种逻辑，用于根据其下监视器的运行状况确定自己的运行状况。 聚合监视器的可能运行状况汇总策略如下：

#### <a name="worst-state-policy"></a>最差状态策略

聚合监视器的状态与具有最差运行状况状态的子监视器的状态一致。 这是聚合监视器最常使用的策略。

![聚合监视器汇总最差状态示例](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>百分比策略

源对象与处于最佳状态的指定百分比目标对象的单个成员的最差状态一致。 当特定百分比的目标对象必须处于正常状态才能将某个目标对象视为正常时，将使用此策略。 百分比策略按状态的严重性对监视器降序排序，聚合监视器的状态计算为 N% 的最差状态（N 由配置参数 StateThresholdPercentage 指定）。

例如，假设一个容器映像有五个容器实例，它们的状态分别为“严重”、“警告”、“正常”、“正常”、“正常”    。  容器 CPU 利用率监视器的状态将为“严重”，因为按严重性降序排序时，90% 容器的最差状态为“严重” 。

## <a name="understand-the-monitoring-configuration"></a>了解监视配置

用于容器的 Azure Monitor 包括许多关键的监视方案，配置如下。

### <a name="unit-monitors"></a>单元监视器

|**监视器名称** | 监视器类型 | **说明** | **参数** | **值** |
|-----------------|--------------|-----------------|---------------|-----------|
|节点内存利用率 |单元监视器 |此监视器使用 cadvisor 报告的数据，每分钟评估一个节点的内存利用率。 |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|节点 CPU 利用率 |单元监视器 |此监视器使用 cadvisor 报告的数据，每分钟检查一次节点的 CPU 利用率。 | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|节点状态 |单元监视器 |此监视器检查 Kubernetes 报告的节点状况。<br> 目前检查以下节点状况：磁盘压力、内存压力、PID 压力、磁盘不足、网络不可用、节点的就绪状态。<br> 在上述情况下，如果“磁盘不足”或“网络不可用”为 true，则监视器将更改为“严重”状态  。<br> 如果除“就绪”状态外的任何其他状况均等于 true，监视器将更改为“警告”状态  。 | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|容器内存利用率 |单元监视器 |此监视器报告容器实例内存利用率 (RSS) 的综合运行状况状态。<br> 它执行简单的比较，将每个样本与单个阈值进行比较，并由配置参数 ConsecutiveSamplesForStateTransition 指定。<br> 其状态计算为 90% 容器 (StateThresholdPercentage) 实例的最差状态，按容器运行状况状态的严重性（即严重、警告、正常）降序排序。<br> 如果没有从容器实例收到任何记录，则容器实例的运行状况状态将报告为“未知”，并且在排序顺序上比“严重”状态具有更高的优先级 。<br> 使用配置中指定的阈值计算各容器实例的状态。 如果使用率超过临界阈值 (90%)，则实例处于“严重”状态，如果低于临界阈值 (90%) 但高于警告阈值 (80%)，则实例处于“警告”状态 。 否则，它处于“正常”状态。 |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|容器 CPU 利用率 |单元监视器 |此监视器报告容器实例 CPU 利用率的综合运行状况状态。<br> 它执行简单的比较，将每个样本与单个阈值进行比较，并由配置参数 ConsecutiveSamplesForStateTransition 指定。<br> 其状态计算为 90% 容器 (StateThresholdPercentage) 实例的最差状态，按容器运行状况状态的严重性（即严重、警告、正常）降序排序。<br> 如果没有从容器实例收到任何记录，则容器实例的运行状况状态将报告为“未知”，并且在排序顺序上比“严重”状态具有更高的优先级 。<br> 使用配置中指定的阈值计算各容器实例的状态。 如果使用率超过临界阈值 (90%)，则实例处于“严重”状态，如果低于临界阈值 (90%) 但高于警告阈值 (80%)，则实例处于“警告”状态 。 否则，它处于“正常”状态。 |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|系统工作负载 Pod 就绪 |单元监视器 |此监视器基于给定工作负载中处于就绪状态的 Pod 百分比报告状态。 如果小于 100% 的 Pod 处于“正常”状态，则其状态设置为“严重”  |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Kube API 状态 |单元监视器 |此监视器报告 Kube API 服务的状态。 当 Kube API 终结点不可用时，监视器处于“严重”状态。 对于此特定监视器，状态是通过查询 kube-api 服务器的“节点”终结点来确定的。 除了 OK 之外的任何其他响应代码都会将监视器更改为“严重”状态。 | 无配置属性 |||

### <a name="aggregate-monitors"></a>聚合监视器

|**监视器名称** | **说明** | **算法** |
|-----------------|-----------------|---------------|
|节点 |此监视器是所有节点监视器的聚合。 它与具有最差运行状况状态的子监视器的状态一致：<br> 节点 CPU 利用率<br> 节点内存利用率<br> 节点状态 | 最差|
|节点池 |此监视器报告节点池 agentpool 中所有节点的综合运行状况状态。 这是一个三状态监视器，其状态基于节点池中 80% 节点的最差状态，按节点状态的严重性（即严重、警告、正常）降序排序。|百分比 |
|节点（节点池的父级） |这是所有节点池的聚合监视器。 其状态基于其子监视器（即群集中存在的节点池）的最差状态。 |最差 |
|群集（节点的父级/<br> Kubernetes 基础结构） |这是父监视器，与具有最差运行状况状态的子监视器（即 Kubernetes 基础结构和节点）的状态一致。 |最差 |
|Kubernetes 基础结构 |此监视器报告群集托管基础结构组件的综合运行状况状态。 其状态计算为子监视器状态中的“最差”状态，即 kube 系统工作负载和 API 服务器状态。 |最差|
|系统工作负载 |此监视器报告 kube 系统工作负载的运行状况状态。 此监视器与具有最差运行状况状态的子监视器的状态一致，即“处于就绪状态的 Pod”（监视器和工作负载中的容器）。 |最差 |
|容器 |此监视器报告给定工作负载中容器的总体运行状况状态。 此监视器与具有最差运行状况状态的子监视器的状态一致，即“CPU 利用率”和“内存利用率”监视器 。 |最差 |

## <a name="next-steps"></a>后续步骤

查看[监视器群集运行状况](container-insights-health.md)，了解如何查看 Kubernetes 群集的运行状况状态。
