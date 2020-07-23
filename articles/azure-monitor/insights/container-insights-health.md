---
title: 使用适用于容器的 Azure Monitor 监视 Kubernetes 运行状况 | Microsoft Docs
description: 本文介绍如何使用适用于容器的 Azure Monitor 查看和分析 AKS 和非 AKS 群集的运行状况。
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649573"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>通过适用于容器的 Azure Monitor 了解 Kubernetes 运行状况

使用适用于容器的 Azure Monitor 时，它会监视和报告托管基础结构组件的运行状况，以及在其支持的任何 Kubernetes 群集上运行的所有节点的运行状况。 通过此体验，不仅可以在[多群集视图](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)上计算和报告群集运行状况和状态，现在还可以了解群集中的一个或多个节点是否存在资源限制，或者某个节点或 Pod 是否不可用，从而可能会影响群集中正在运行的应用程序（基于特选指标）。

>[!NOTE]
>运行状况功能将在 2020 年 6 月末转换为私人预览版。 有关其他信息，请查看以下 [Azure 更新公告](https://azure.microsoft.com/updates/ci-health-limited-preview/)。
>

若要了解如何启用适用于容器的 Azure Monitor，请参阅[载入适用于容器的 Azure Monitor](container-insights-onboard.md)。

>[!NOTE]
>若要支持 AKS 引擎群集，请验证它是否满足以下条件：
>- 它使用最新版本的 [HELM 客户端](https://helm.sh/docs/using_helm/)。
>- 容器化代理版本为 microsoft/oms:ciprod11012019。 若要升级代理，请参阅 [在 Kubernetes 群集上升级代理](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent)。
>

## <a name="overview"></a>概述

在适用于容器的 Azure Monitor 中，运行状况（预览版）功能提供对 Kubernetes 群集的主动运行状况监视，可帮助确定和诊断问题。 利用它，可以查看检测到的重大问题。 评估集群运行状况的监视器在集群中的容器化代理上运行，而运行状况数据将被写入 Log Analytics 工作区中的“KubeHealth”表中。 

Kubernetes 群集运行状况信息基于大量监视方案，这些方案通过以下 Kubernetes 对象和抽象来进行组织：

- Kubernetes 基础结构 - 通过评估 CPU 和内存使用率，以及 Pod 可用性，提供关于在集群中部署的节点上运行的 Kubernetes API 服务器、ReplicaSets 和 DaemonSet 的汇总信息

    ![Kubernetes 基础结构运行状况汇总视图](./media/container-insights-health/health-view-kube-infra-01.png)

- 节点 - 通过评估 CPU 和内存使用率，以及 Kubernetes 报告的节点状态，提供关于节点池和每个池中各节点的状态的汇总信息。

    ![节点运行状况汇总视图](./media/container-insights-health/health-view-nodes-01.png)

目前仅支持提供虚拟 kubelet 的状态。 虚拟 kublet 节点的 CPU 和内存使用的运行状况状态将报告为“未知”，因为未从它们接收到信号。

所有监视器均显示在“运行状况层次结构”窗格中的层次结构布局中，其中最顶层的监视器是代表 Kubernetes 对象或抽象（即，Kubernetes 基础结构或节点）的聚合监视器，它反映了所有从属子监视器的综合运行状况。 用于派生运行状况的主要监视方案有：

* 评估节点和容器的 CPU 使用率。
* 评估节点和容器的内存使用率。
* 根据 Kubernetes 报告的就绪状态计算得出 Pod 和节点的状态。

用于指示状态的图标如下所示：

|图标|含义|  
|--------|-----------|  
|![绿色复选图标表示正常](./media/container-insights-health/healthyicon.png)|成功，运行状况正常（绿色）|  
|![黄色三角形和感叹号为警告](./media/container-insights-health/warningicon.png)|警告（黄色）|  
|![带白色 X 的红色按钮指示严重状态](./media/container-insights-health/criticalicon.png)|严重（红色）|  
|![灰显图标](./media/container-insights-health/grayicon.png)|未知（灰色）|  

## <a name="monitor-configuration"></a>监视器配置

若要了解支持适用于容器的 Azure Monitor 运行状况功能的每个监视器的行为和配置，请参阅[运行状况监视器配置指南](container-insights-health-monitors-config.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>查看 AKS 或非 AKS 群集的运行状况

通过从 Azure 门户的左窗格中选择“Insights”，可以直接从 AKS 群集访问适用于容器的 Azure Monitor 运行状况（预览版）功能。 在“见解”部分，选择“容器”。  

若要查看非 AKS 群集的运行状况（即托管在本地或 Azure Stack 上的 AKS Engine 群集），请从 Azure 门户的左窗格中选择“Azure Monitor”"。 在“见解”部分，选择“容器”。   在多群集页上，选择列表中的非 AKS 群集。

在适用于容器的 Azure Monitor 中，选择“集群”页面上的“运行状况”。

![群集运行状况仪表板示例](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>查看群集运行状况

当“运行状况”页打开时，会默认选中“运行状况”网格中的“Kubernetes 基础结构” 。  该网格汇总了 Kubernetes 基础结构和群集节点的当前运行状况状态。 选择两个类别中任一个的运行状况后，将在运行状况层次结构窗格（即中间窗格）中更新结果，并以层次结构布局显示所有子监视器，同时显示其当前运行状况状态。 若要查看有关任何从属监视器的详细信息，可以选择一个监视器，随即页面右侧会自动显示属性窗格。 

![群集运行状况属性窗格](./media/container-insights-health/health-view-property-pane.png)

在属性窗格中，可了解以下内容：

- 在“概述”选项卡上，它显示选定的监视器的当前状态、上次计算监视器的时间以及上次发生状态更改的时间。 根据在层次结构中选择的监视器类型，会显示其他信息。

    如果在“运行状况层次结构”窗格中选择聚合监视器，则在“属性”窗格上的“概述”选项卡下，将显示层次结构中所有子监视器的汇总信息，以及分别有多少个聚合监视器处于“严重”、“警告”和“正常”状态。 

    ![运行状况属性窗格中的聚合监视器概述选项卡](./media/container-insights-health/health-overview-aggregate-monitor.png)

    如果在“运行状况层次结构”窗格中选择一个单元监视器，它还会在“上次状态更改”下显示最近四个小时内由容器化代理计算和报告的之前的状态。 这是基于单元监视器的计算结果，该计算比较多个连续值以确定其状态。 例如，如果选择了“Pod 就绪状态”单元监视器，它会显示由参数“ConsecutiveSamplesForStateTransition”控制的最后两个示例。 有关详细信息，请参阅[单元监视器](container-insights-health-monitors-config.md#unit-monitors)的详细说明。
    
    ![运行状况属性窗格概述选项卡](./media/container-insights-health/health-overview-unit-monitor.png)

    如果“上次状态更改”所报告的时间为一天或更长时间，则表示该监视器的状态未发生更改。 但是，如果收到的关于单元监视器的上一个示例的时间早于四个小时之前，这很可能表示容器化代理尚未发送数据。 如果代理知道存在某个特定资源（例如某个节点），但它未从（例如）该节点的 CPU 或内存使用率监视器收到数据，则该监视器的运行状况状态将设置为“未知”。  

- “配置”选项卡上将显示默认配置参数设置（仅适用于单元监视器，不适用于聚合监视器）及其值。
- “知识”选项卡上包含说明监视器行为以及如何评估不正常运行状况的信息。

此页上的监视数据不会自动刷新，需要在页面顶部选择“刷新”，查看从群集接收的最新运行状况状态。

## <a name="next-steps"></a>后续步骤

通过查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，可查看预定义的查询和示例，从而对其进行评估或自定义，以便对群集执行警报、可视化或分析操作。
