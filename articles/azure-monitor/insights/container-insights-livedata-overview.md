---
title: 查看容器 Azure Monitor 的实时数据（预览） |Microsoft Docs
description: 本文介绍 Kubernetes 日志、事件和 pod 指标的实时视图，而无需在容器 Azure Monitor 中使用 kubectl。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 38615acf079c33c49e27394bfdb9b8104e5a68ac
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872118"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>如何实时查看 Kubernetes 日志、事件和 Pod 指标

容器 Azure Monitor 包括实时数据（预览）功能，这是一项高级诊断功能，可让你直接访问 Azure Kubernetes 服务（AKS）容器日志（stdout/stderror）、事件和 pod 指标。 它公开对`kubectl logs -c`、 `kubectl get`事件和`kubectl top pods`的直接访问。 控制台窗格显示由容器引擎生成的日志、事件和度量值，以便进一步帮助排查问题。

本文提供详细的概述，帮助你了解如何使用此功能。 

>[!NOTE]
>此功能不支持将 AKS 群集作为[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)启用。 此功能依赖于通过代理服务器直接从浏览器访问 Kubernetes API。 启用网络安全以阻止此代理中的 Kubernetes API 会阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 区域均可用，包括Azure 中国。 它目前在 Azure 美国政府版中不可用。

若要获取有关设置实时数据（预览）功能的帮助或进行故障排除，请查看我们的[设置指南](container-insights-livedata-setup.md)。 此功能直接访问 Kubernetes API，可以在[此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到有关身份验证模式的其他信息。 

## <a name="live-data-preview-functionality-overview"></a>实时数据（预览）功能概述

### <a name="search"></a>搜索

![实时数据控制台窗格筛选器示例](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

实时数据（预览）功能包括搜索功能。 在**搜索**字段中，可以通过键入关键字或字词来筛选结果，并突出显示任何匹配结果以允许快速查看。 在查看事件时，可以进一步对结果进行限制，只需使用在搜索栏右侧发现的“筛选器”**** 药丸图标即可。 根据所选的资源，欣然会列出要从中选择的 Pod、命名空间或群集。  

### <a name="scroll-lock-and-pause"></a>滚动锁定和暂停 

若要暂停自动滚动并控制该窗格的行为，允许您手动滚动读取新数据，可以使用**scroll**选项。 若要重新启用自动滚动，只需再次选择**滚动**选项。 还可以通过选择 "**暂停**" 选项暂停对日志或事件数据的检索，并在准备好继续时，只需选择 "**播放**"。  

![实时数据控制台窗格暂停实时视图](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>建议你在解决问题时，只会在短时间内暂停或暂停自动滚动。 这些请求可能会影响群集上 Kubernetes API 的可用性和限制。 

>[!IMPORTANT]
>在操作此功能的过程中，不会永久存储数据。 当你关闭浏览器或离开浏览器或离开浏览器时，将删除会话期间捕获的所有信息。 数据仅在指标功能的五分钟窗口内保持可视化任何超过五分钟的指标也会被删除。 实时数据（预览）缓冲区在合理内存使用量限制内查询。

## <a name="view-logs"></a>查看日志

可以查看实时日志数据，因为它们由容器引擎从 "**节点**"、"**控制器**" 和 "**容器**" 视图中生成。 若要查看日志数据，请执行以下步骤。

1. 在 Azure 门户中，浏览到 "AKS" 群集资源组，然后选择 "AKS" 资源。

2. 在 AKS 群集仪表板的左侧 "**监视**" 下，选择 "**见解**"。 

3. 选择 "**节点**"、"**控制器**" 或 "**容器**" 选项卡。

4. 从 "性能" 网格中选择一个对象，然后在右侧的 "属性" 窗格中选择 "**查看实时数据（预览）** " 选项。 如果使用 Azure AD 将 AKS 群集配置为使用单一登录，则在浏览器会话过程中首次使用时，系统将提示你进行身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

    >[!NOTE]
    >通过在 "属性" 窗格中选择 "**在分析中查看**" 选项查看 Log Analytics 工作区中的数据时，日志搜索结果可能会显示可能不再存在的**节点**、**守护程序集**、**副本集**、**作业**、 **Cron 作业** **、pod 和****容器**。 尝试搜索在中`kubectl`不可用的容器的日志也会失败。 查看 "[在分析中查看](container-insights-log-search.md#search-logs-to-analyze-data)" 功能，了解有关查看历史日志、事件和指标的详细信息。  

成功进行身份验证后，"实时数据（预览）" 控制台窗格将显示在 "性能" 数据网格下，你可以在其中查看连续流中的日志数据。 如果提取状态指示器显示一个绿色复选标记，该标记位于窗格的最右侧，则表示可检索数据，并开始流式传输到控制台。  

![节点属性窗格查看数据选项](./media/container-insights-livedata-overview/node-properties-pane.png)  

窗格标题会显示进行容器分组时所使用的 Pod 的名称。

## <a name="view-events"></a>查看事件

如果选择了容器、pod、node、ReplicaSet、DaemonSet、作业、CronJob 或部署，则可以在容器引擎从**节点**、**控制器**、**容器**和**部署（预览）** 视图中生成实时事件数据。 若要查看事件，请执行以下步骤。

1. 在 Azure 门户中，浏览到 "AKS" 群集资源组，然后选择 "AKS" 资源。

2. 在 AKS 群集仪表板的左侧 "**监视**" 下，选择 "**见解**"。 

3. 选择 "**节点**"、"**控制器**"、"**容器**" 或 "**部署（预览）** " 选项卡。

4. 从 "性能" 网格中选择一个对象，然后在右侧的 "属性" 窗格中选择 "**查看实时数据（预览）** " 选项。 如果使用 Azure AD 将 AKS 群集配置为使用单一登录，则在浏览器会话过程中首次使用时，系统将提示你进行身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

    >[!NOTE]
    >通过在 "属性" 窗格中选择 "**在分析中查看**" 选项查看 Log Analytics 工作区中的数据时，日志搜索结果可能会显示可能不再存在的**节点**、**守护程序集**、**副本集**、**作业**、 **Cron 作业** **、pod 和****容器**。 尝试搜索在中`kubectl`不可用的容器的日志也会失败。 查看 "[在分析中查看](container-insights-log-search.md#search-logs-to-analyze-data)" 功能，了解有关查看历史日志、事件和指标的详细信息。  

成功进行身份验证后，"实时数据（预览）" 控制台窗格将显示在 "性能" 数据网格下方。 如果提取状态指示器显示一个绿色复选标记，该标记位于窗格的最右侧，则表示可检索数据，并开始流式传输到控制台。 
    
如果你选择的对象是一个容器，请在窗格中选择 "**事件**" 选项。 如果选择了节点、Pod 或控制器，则会自动选择 "查看事件"。 

!["控制器属性" 窗格查看事件](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

窗格标题显示容器的分组容器的名称。

### <a name="filter-events"></a>筛选事件 

在查看事件时，可以进一步对结果进行限制，只需使用在搜索栏右侧发现的“筛选器”**** 药丸图标即可。 根据所选的资源，欣然会列出要从中选择的 Pod、命名空间或群集。  

## <a name="view-metrics"></a>查看指标 

仅当选择了**Pod**时，才能从 "**节点**" 或 "**控制器**" 视图中的容器引擎生成实时度量值数据。 若要查看指标，请执行以下步骤。

1. 在 Azure 门户中，浏览到 "AKS" 群集资源组，然后选择 "AKS" 资源。

2. 在 AKS 群集仪表板的左侧 "**监视**" 下，选择 "**见解**"。 

3. 选择 "**节点**" 或 "**控制器**" 选项卡。

4. 从 "性能" 网格中选择一个**Pod**对象，然后在右侧的 "属性" 窗格中选择 "**查看实时数据（预览）** " 选项。 如果使用 Azure AD 将 AKS 群集配置为使用单一登录，则在浏览器会话过程中首次使用时，系统将提示你进行身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

    >[!NOTE]
    >通过在 "属性" 窗格中选择 "**在分析中查看**" 选项查看 Log Analytics 工作区中的数据时，日志搜索结果可能会显示可能不再存在的**节点**、**守护程序集**、**副本集**、**作业**、 **Cron 作业** **、pod 和****容器**。 尝试搜索在中`kubectl`不可用的容器的日志也会失败。 查看 "[在分析中查看](container-insights-log-search.md#search-logs-to-analyze-data)" 功能，了解有关查看历史日志、事件和指标的详细信息。  

成功进行身份验证后，"实时数据（预览）" 控制台窗格将显示在 "性能" 数据网格下方。 检索度量值数据并开始流式传输到控制台，以便在两个图表中进行演示。 窗格标题会显示进行容器分组时所使用的 Pod 的名称。

![查看 Pod 指标示例](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>后续步骤

- 若要继续了解如何使用 Azure Monitor 和监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看用于创建警报、可视化效果或进一步分析群集的预定义查询和示例。
