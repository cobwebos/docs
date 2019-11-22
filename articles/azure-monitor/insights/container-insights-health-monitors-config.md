---
title: 容器的 Azure Monitor 运行状况监视器配置 |Microsoft Docs
description: 本文提供了介绍 Azure Monitor for 容器中的运行状况监视器的详细配置的内容。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/12/2019
ms.author: magoedte
ms.openlocfilehash: 7a774adb33646635832dba5505abf57b2703de5d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279694"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>容器的 Azure Monitor 运行状况监视器配置指南

监视器是用于测量运行状况和检测容器 Azure Monitor 中的错误的主要元素。 本文将帮助你了解如何测量运行状况的概念，以及构成运行状况模型的元素，以监视和报告具有[运行状况（预览版）](container-insights-health.md)功能的 Kubernetes 群集的运行状况。

>[!NOTE]
>运行状况功能目前处于公共预览状态。
>

## <a name="monitors"></a>监视器

监视器衡量托管对象某个方面的运行状况。 监视器每个具有两个或三个运行状况状态。 在任何给定时间，监视器都将处于一种或多种状态。 容器化代理加载监视器时，会将监视器初始化为正常状态。 仅当检测到另一种状态的指定条件时，状态才会更改。

特定对象的总体运行状况由其每个监视器的运行状况确定。 此层次结构在容器 Azure Monitor 中的 "运行状况层次结构" 窗格中进行说明。 如何汇总运行状况的策略是聚合监视器的配置的一部分。

## <a name="types-of-monitors"></a>监视器类型

|监视 | 说明 | 
|--------|-------------|
| 单元监视器 |单元监视器测量资源或应用程序的某个方面。 这可能会检查性能计数器以确定资源的性能或其可用性。 |
|聚合监视器 | 聚合监视器将多个监视器组合在一起，以提供单个运行状况聚合运行状况状态。 单元监视器通常在特定聚合监视器下进行配置。 例如，节点聚合监视器汇总节点的 CPU 使用率、内存使用率和节点状态的状态。
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>聚合监视器运行状况汇总策略

每个聚合监视器都定义了一个 "运行状况汇总策略"，它是用于根据监视器的运行状况确定聚合监视器的运行状况的逻辑。 聚合监视器的可能的运行状况汇总策略如下：

#### <a name="worst-state-policy"></a>最差状态策略

聚合监视器的状态与具有最差运行状况状态的子监视器的状态相匹配。 这是聚合监视器使用的最常见的策略。

![聚合监视器汇总最差状态示例](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>百分比策略

源对象与处于最佳状态的目标对象的指定百分比的单个成员的最差状态匹配。 当目标对象的一定百分比必须处于正常状态才能将目标对象视为正常时，将使用此策略。 百分比策略按状态严重性的降序对监视器进行排序，而聚合监视器的状态则计算为 N% 的最差状态（N 由配置参数*StateThresholdPercentage*决定）。

例如，假设有一个容器映像的五个容器实例，其各个状态为 "**严重**"、"**警告**"、"**正常**"、"**正常"、"** **正常**"。  容器 CPU 使用率监视器的状态为 "**严重**"，因为在按严重性的降序排序时，90% 的容器的最差状态为 "**严重**"。

## <a name="understand-the-monitoring-configuration"></a>了解监视配置

容器 Azure Monitor 包括一些按如下方式配置的关键监视方案。

### <a name="unit-monitors"></a>单元监视器

|**监视器名称** | 监视器类型 | **说明** | **Parameter** | **值** |
|-----------------|--------------|-----------------|---------------|-----------|
|节点内存利用率 |单元监视器 |此监视器使用 cadvisor 报告的数据每分钟计算节点的内存使用率。 |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|节点 CPU 使用率 |单元监视器 |此监视器使用 cadvisor 报告的数据每分钟检查节点的 CPU 使用率。 | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|节点状态 |单元监视器 |此监视器检查 Kubernetes 报告的节点状态。<br> 当前已检查以下节点条件：磁盘压力、内存压力、PID 压力、磁盘空间不足、网络不可用、节点就绪状态。<br> 在上述情况下，如果*磁盘空间不足*或*网络不可用*都为**true**，则监视器将更改为 "**严重**" 状态。<br> 如果任何其他条件都等于**true**（即 "**就绪**" 状态），则监视器将更改为 "**警告**" 状态。 | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|容器内存利用率 |单元监视器 |此监视器报告容器实例的内存使用率（RSS）的运行状况状态。<br> 它执行简单的比较，比较每个样本和单个阈值，并由配置参数**ConsecutiveSamplesForStateTransition**指定。<br> 其状态计算为容器（StateThresholdPercentage）实例的最差90状态，以容器运行状况状态严重性的降序排序（即 "严重"、"警告"、"正常"）。<br> 如果没有从容器实例接收到任何记录，则容器实例的运行状况状态将报告为 "**未知**"，并且在 "**严重**" 状态下的排序顺序中优先级较高。<br> 使用在配置中指定的阈值计算每个单独容器实例的状态。 如果使用率高于严重阈值（90%），则实例处于**严重**状态（如果它小于严重阈值（90%））但大于警告阈值（80%），则实例处于**警告**状态。 否则，该状态处于**正常**状态。 |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|容器 CPU 使用率 |单元监视器 |此监视器报告容器实例的 CPU 使用率的合并运行状况状态。<br> 它执行简单的比较，比较每个样本和单个阈值，并由配置参数**ConsecutiveSamplesForStateTransition**指定。<br> 其状态计算为容器（StateThresholdPercentage）实例的最差90状态，以容器运行状况状态严重性的降序排序（即 "严重"、"警告"、"正常"）。<br> 如果没有从容器实例接收到任何记录，则容器实例的运行状况状态将报告为 "**未知**"，并且在 "**严重**" 状态下的排序顺序中优先级较高。<br> 使用在配置中指定的阈值计算每个单独容器实例的状态。 如果使用率高于严重阈值（90%），则实例处于**严重**状态（如果它小于严重阈值（90%））但大于警告阈值（80%），则实例处于**警告**状态。 否则，该状态处于**正常**状态。 |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|系统工作负荷箱就绪 |单元监视器 |此监视器根据给定工作负荷中处于 "就绪" 状态的箱的百分比报告状态。 如果小于100% 的 pod 处于**正常**状态，则将其状态设置为 "**严重**" |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 个 ||
|Kube API 状态 |单元监视器 |此监视器报告 Kube Api 服务的状态。 当 Kube Api 终结点不可用时，监视器处于严重状态。 对于此特定监视器，通过对 kube-api 服务器的 "节点" 终结点进行查询来确定状态。 除 OK 响应代码以外的任何内容都会将监视器更改为**严重**状态。 | 无配置属性 |||

### <a name="aggregate-monitors"></a>聚合监视器

|**监视器名称** | **说明** | **算法** |
|-----------------|-----------------|---------------|
|节点 |此监视器是所有节点监视器的聚合。 它与具有最差运行状况状态的子监视器的状态相匹配：<br> 节点 CPU 使用率<br> 节点内存利用率<br> 节点状态 | 最差|
|节点池 |此监视器报告节点池*agentpool*中所有节点的合并运行状态。 这是一个三状态监视器，其状态基于节点池中的80% 节点的最差状态，按节点状态严重性的降序排序（即 "严重"、"警告"、"正常"）。|百分比 |
|节点（节点池的父节点） |这是所有节点池的聚合监视器。 其状态基于子监视器的最差状态（即群集中存在的节点池）。 |最差 |
|群集（节点的父节点/<br> Kubernetes 基础结构） |这是与具有最差运行状况状态的子监视器的状态相匹配的父监视器，即 kubernetes 基础结构和节点。 |最差 |
|Kubernetes 基础结构 |此监视器报告群集的托管基础结构组件的运行状况状态。 其状态计算为子监视器状态的 "最差"，即 kube 工作负荷和 API 服务器状态。 |最差|
|系统工作负荷 |此监视器报告 kube 工作负荷的运行状况状态。 此监视器将子监视器的状态与最差的运行状况状态（即**就绪状态监视器中**的 pod 和工作负荷中的容器）相匹配。 |最差 |
|容器 |此监视器报告给定工作负荷中的容器的总体运行状况状态。 此监视器与运行状况状态最差的子监视器的状态（即 " **CPU 使用率**" 和 "**内存使用率**" 监视器）相匹配。 |最差 |

## <a name="next-steps"></a>后续步骤

查看[监视群集运行状况](container-insights-health.md)，了解如何查看 Kubernetes 群集的运行状况状态。