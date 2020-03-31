---
title: 使用 Azure 监视器监视容器库伯内斯群集运行状况 |微软文档
description: 本文介绍如何使用容器的 Azure 监视器查看和分析 AKS 和非 AKS 群集的运行状况。
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843984"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>使用容器的 Azure 监视器了解库伯内斯群集运行状况

使用容器的 Azure 监视器，它监视和报告托管基础结构组件和在 Azure 监视器支持的任何 Kubernetes 群集上运行的所有节点的运行状况状态。 此体验超出了在[多群集视图](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)上计算和报告的群集运行状况状态，现在您可以了解群集中的一个或多个节点是否受资源约束，或者节点或 pod 不可用，可能会影响基于精心策划的指标的群集中的正在运行的应用程序。

>[!NOTE]
>此时，"运行状况"功能处于公共预览版中。
>

若要了解如何启用适用于容器的 Azure Monitor，请参阅[载入适用于容器的 Azure Monitor](container-insights-onboard.md)。

>[!NOTE]
>要支持 AKS 引擎群集，请验证它满足以下内容：
>- 它使用最新版本的[HELM客户端](https://helm.sh/docs/using_helm/)。
>- 容器化代理版本是*微软/oms：ciprod11012019*。 要升级代理，请参阅[库伯内斯群集上的升级代理](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster)。
>

## <a name="overview"></a>概述

在容器的 Azure 监视器中，"运行状况（预览）"功能提供 Kubernetes 群集的主动运行状况监视，以帮助您识别和诊断问题。 它使您能够查看检测到的重要问题。 监视器评估群集中容器化代理上运行的群集运行状况，并将运行状况数据写入日志分析工作区中的**KubeHealth**表。 

库伯内斯群集运行状况基于以下 Kubernetes 对象和抽象组织的多个监视方案：

- Kubernetes 基础结构 - 通过评估 CPU 和内存利用率以及 Pods 可用性，提供在群集中部署的节点上运行的 Kubernetes API 服务器、副本集和守护程序集的汇总

    ![库伯内斯基础设施运行状况汇总视图](./media/container-insights-health/health-view-kube-infra-01.png)

- 节点 - 通过评估 CPU 和内存利用率以及 Kubernetes 报告节点的状态，提供每个池中节点池和单个节点状态的汇总。

    ![节点运行状况汇总视图](./media/container-insights-health/health-view-nodes-01.png)

目前，仅支持虚拟库贝莱特的状态。 虚拟库布节点的 CPU 和内存利用率的运行状况状态报告为 **"未知"，** 因为未从它们接收信号。

所有监视器都显示在"运行状况层次结构"窗格中的分层布局中，其中表示库伯奈斯对象或抽象（即 Kubernetes 基础结构或节点）的聚合监视器是反映所有运行状况组合的监视器。依赖子监视器。 用于派生运行状况的关键监视方案包括：

* 评估节点和容器的 CPU 利用率。
* 评估节点和容器的内存利用率。
* 基于 Kubernets 报告的就绪状态的计算的 Pod 和节点的状态。

用于表示状态的图标如下所示：

|图标|含义|  
|--------|-----------|  
|![绿色复选图标指示正常](./media/container-insights-health/healthyicon.png)|成功、运行状况正常（绿色）|  
|![黄色三角形和感叹号为警告](./media/container-insights-health/warningicon.png)|警告（黄色）|  
|![带白色 X 的红色按钮指示严重状态](./media/container-insights-health/criticalicon.png)|严重（红色）|  
|![灰显图标](./media/container-insights-health/grayicon.png)|未知（灰色）|  

## <a name="monitor-configuration"></a>监视器配置

要了解支持容器运行状况功能的每个监视器的行为和配置，请参阅[运行状况监视器配置指南](container-insights-health-monitors-config.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 Azure[门户](https://portal.azure.com)。 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>查看 AKS 或非 AKS 群集的运行状况

通过从 Azure 门户中的左侧窗格中选择 **"见解"，** 可以直接从 AKS 群集访问容器运行状况（预览）功能。 在“见解”部分，选择“容器”。******** 

要查看非 AKS 群集（即本地或 Azure 堆栈上托管的 AKS 引擎群集）的运行状况，请从 Azure 门户中的左窗格中选择**Azure 监视器**。 在“见解”部分，选择“容器”。********  在多群集页上，从列表中选择非 AKS 群集。

在容器的 Azure 监视器中，从 **"群集"** 页中选择 **"运行状况**"。

![群集运行状况仪表板示例](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>查看群集运行状况

打开"运行状况"页时，默认情况下在 **"运行状况"网格**中选择**库伯内特基础结构**。  网格总结了库贝内斯基础结构和群集节点的当前运行状况汇总状态。 选择任一运行状况方面会更新"运行状况层次结构"窗格（即中间窗格）中的结果，并在分层布局中显示所有子监视器，显示其当前运行状况状态。 要查看有关任何从属监视器的详细信息，可以选择一个属性窗格，并自动显示在页面右侧。 

![群集运行状况属性窗格](./media/container-insights-health/health-view-property-pane.png)

在属性窗格中，您将了解以下内容：

- 在 **"概述"** 选项卡上，它显示所选监视器的当前状态、上次计算监视器的时以及上次状态更改的次。 根据层次结构中选择的监视器类型显示其他信息。

    如果在"运行状况层次结构"窗格中选择聚合监视器，则在属性窗格的 **"概述"** 选项卡下，将显示层次结构中子监视器总数以及处于严重、警告和状态的聚合监视器数的汇总。 

    ![用于聚合监视器的运行状况属性窗格"概述"选项卡](./media/container-insights-health/health-overview-aggregate-monitor.png)

    如果在"运行状况层次结构"窗格中选择单位监视器，它还在 **"上次状态"** 下显示容器化代理在过去四小时内计算和报告以前的示例。 这基于用于比较多个连续值以确定其状态的单位监视器计算。 例如，如果选择了 Pod*就绪状态*单元监视器，它将显示由参数 *"连续采样ForState转换*"控制的最后两个样本。 有关详细信息，请参阅[单元监视器](container-insights-health-monitors-config.md#unit-monitors)的详细说明。
    
    ![运行状况属性窗格"概述"选项卡](./media/container-insights-health/health-overview-unit-monitor.png)

    如果**Last 状态更改**报告的时间为一天或更久，则它是监视器状态没有变化的结果。 但是，如果为单元监视器接收的最后一个样本超过 4 小时，这可能表示容器化代理未发送数据。 如果代理知道存在特定资源（例如 Node），但它尚未从节点的 CPU 或内存利用率监视器接收数据（例如），则监视器的运行状况状态设置为 **"未知**"。  

- 在**Config**选项卡上，它显示默认配置参数设置（仅适用于单元监视器，而不是聚合监视器）及其值。
- 在 **"知识"** 选项卡上，它包含解释监视器行为及其评估不正常情况的方式的信息。

监视此页面上的数据不会自动刷新，您需要选择页面顶部的 **"刷新"** 以查看从群集接收的最新运行状况状态。

## <a name="next-steps"></a>后续步骤

请参阅[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看预定义的查询，以及有关群集警报、可视化或分析的评估或自定义示例。
