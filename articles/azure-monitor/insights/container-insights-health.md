---
title: 用 Azure Monitor 容器监视 Kubernetes 群集运行状况 |Microsoft Docs
description: 本文介绍了如何通过 Azure Monitor 容器来查看和分析 AKS 和非 AKS 群集的运行状况。
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843984"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>使用适用于容器的 Azure Monitor 了解 Kubernetes 群集运行状况

对于容器 Azure Monitor，它会监视和报告托管基础结构组件的运行状况状态，以及在 Azure Monitor for 容器支持的任何 Kubernetes 群集上运行的所有节点的运行状况状态。 此体验超出了在[多群集视图](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)上计算和报告的群集运行状况状态，现在你可以了解群集中的一个或多个节点是否受资源约束，或者节点或 pod 是否不可用，可能会影响群集中正在运行的应用程序（基于特选指标）。

>[!NOTE]
>运行状况功能目前处于公共预览状态。
>

有关如何为容器启用 Azure Monitor 的信息，请参阅[容器的板载 Azure Monitor](container-insights-onboard.md)。

>[!NOTE]
>若要支持 AKS 引擎群集，请验证它是否满足以下各项：
>- 它使用的是最新版本的[HELM 客户端](https://helm.sh/docs/using_helm/)。
>- 容器化代理版本为*microsoft/oms： ciprod11012019*。 若要升级代理，请参阅[升级 Kubernetes 群集上的代理](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster)。
>

## <a name="overview"></a>概述

在容器 Azure Monitor 中，"运行状况（预览版）" 功能提供对 Kubernetes 群集的主动运行状况监视，以帮助你确定和诊断问题。 它使您能够查看检测到的重要问题。 在群集中的容器化代理上评估群集运行状况的监视器，运行状况数据将写入 Log Analytics 工作区中的**KubeHealth**表。 

Kubernetes 群集运行状况基于由以下 Kubernetes 对象和抽象组织的大量监视方案：

- Kubernetes 基础结构-通过评估 CPU 和内存使用率以及 pod 可用性，为群集中部署的节点提供 Kubernetes API 服务器、ReplicaSets 和 Daemonset 的汇总

    ![Kubernetes 基础结构运行状况汇总视图](./media/container-insights-health/health-view-kube-infra-01.png)

- 节点-通过评估 CPU 和内存使用率以及 Kubernetes 报告的节点状态，提供每个池中各个节点的节点池和状态的汇总。

    ![节点运行状况汇总视图](./media/container-insights-health/health-view-nodes-01.png)

目前仅支持虚拟 kubelet 的状态。 虚拟 kublet 节点的 CPU 和内存使用率的运行状况状态报告为 "**未知**"，因为未收到信号。

所有监视器均以层次结构布局显示在 "运行状况层次结构" 窗格中，其中表示 Kubernetes 对象或抽象（即，Kubernetes 基础结构或节点）的聚合监视器是反映所有从属的子监视器。 用于派生运行状况的主要监视方案为：

* 评估节点和容器的 CPU 利用率。
* 评估节点和容器的内存使用率。
* 基于 Kubernetes 报告的就绪状态的计算箱和节点的状态。

用于指示状态的图标如下所示：

|图标|含义|  
|--------|-----------|  
|![绿色复选图标指示正常](./media/container-insights-health/healthyicon.png)|成功、运行状况正常（绿色）|  
|![黄色三角形和感叹号为警告](./media/container-insights-health/warningicon.png)|警告（黄色）|  
|![带有白色 X 的红色按钮指示严重状态](./media/container-insights-health/criticalicon.png)|严重（红色）|  
|![灰显图标](./media/container-insights-health/grayicon.png)|未知（灰色）|  

## <a name="monitor-configuration"></a>监视配置

若要了解支持容器运行状况功能 Azure Monitor 的每个监视器的行为和配置，请参阅[运行状况监视器配置指南](container-insights-health-monitors-config.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>查看 AKS 或非 AKS 群集的运行状况

通过从 Azure 门户的左窗格中选择 "**见解**"，可以直接从 AKS 群集访问为容器运行状况（预览版）功能 Azure Monitor。 在“见解”部分，选择“容器”。 

若要查看非 AKS 群集的运行状况（这是托管在本地或 Azure Stack 上的 AKS Engine 群集），请从 Azure 门户的左窗格中选择 " **Azure Monitor** "。 在“见解”部分，选择“容器”。  在 "多群集" 页上，从列表中选择非 AKS 群集。

在 "容器 Azure Monitor" 中的 "**群集**" 页上，选择 "**运行状况**"。

![群集运行状况仪表板示例](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>查看群集运行状况

当 "运行状况" 页打开时，默认情况下，在 "**运行状况**" 网格中选择**Kubernetes 基础结构**。  该网格汇总了 Kubernetes 基础结构和群集节点的当前运行状况汇总状态。 选择 "运行状况"，可在 "运行状况层次结构" 窗格（即中间窗格）中更新结果，并显示层次结构布局中的所有子监视器，并显示当前的运行状况状态。 若要查看有关任何相关监视器的详细信息，可以选择一个监视器，并自动在页面右侧显示属性窗格。 

![群集运行状况属性窗格](./media/container-insights-health/health-view-property-pane.png)

在 "属性" 窗格中，您将了解以下内容：

- 在 "**概述**" 选项卡上，该选项卡显示所选监视器的当前状态、上次计算监视器的时间以及上次发生状态更改的时间。 将显示其他信息，具体取决于在层次结构中选择的监视器类型。

    如果在 "运行状况层次结构" 窗格中选择聚合监视器，则在 "属性" 窗格中的 "**概述**" 选项卡下会显示层次结构中子监视器总数的汇总，以及有多少个聚合监视器处于严重、警告和正常状态。 

    ![聚合监视器的运行状况属性窗格概述选项卡](./media/container-insights-health/health-overview-aggregate-monitor.png)

    如果在 "运行状况层次结构" 窗格中选择一个单元监视器，它还显示在 "**上次状态**" 下。 这基于用于比较若干连续值以确定其状态的单元监视器计算。 例如，如果选择了 " *Pod 就绪状态*单元监视器"，则会显示由参数*ConsecutiveSamplesForStateTransition*控制的最后两个样本。 有关详细信息，请参阅[单元监视器](container-insights-health-monitors-config.md#unit-monitors)的详细说明。
    
    ![运行状况属性窗格概述选项卡](./media/container-insights-health/health-overview-unit-monitor.png)

    如果**上一次状态更改**报告的时间为一天或更长时间，则它是监视器的状态更改的结果。 但是，如果单元监视器收到的最后一个示例超过四个小时，则这可能表示容器化代理未发送数据。 如果代理知道某个特定资源存在（例如某个节点），但它未从节点的 CPU 或内存使用率监视器收到数据（例如），则该监视器的运行状况状态将设置为 "**未知**"。  

- 在 "**配置**" 选项卡上，它显示默认配置参数设置（仅适用于单元监视器，而不是聚合监视器）及其值。
- "**知识**" 选项卡上包含的信息说明了监视器的行为以及它如何评估不正常情况。

此页上的监视数据不会自动刷新，你需要选择页面顶部的 "**刷新**" 以查看从群集接收到的最新运行状况状态。

## <a name="next-steps"></a>后续步骤

查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以便对群集进行警报、可视化或分析。
