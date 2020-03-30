---
title: 使用 Azure 监视器查看容器的实时数据（预览） |微软文档
description: 本文介绍了 Kubernetes 日志、事件和 pod 指标的实时视图，而无需在 Azure 监视器中使用容器的 kubectl。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216536"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>如何实时查看 Kubernetes 日志、事件和 Pod 指标

容器的 Azure 监视器包括实时数据（预览）功能，这是一个高级诊断功能，允许您直接访问 Azure Kubernetes 服务 （AKS） 容器日志 （stdout/stderror）、事件和 pod 指标。 它公开对`kubectl logs -c`的`kubectl get`直接访问 ，事件`kubectl top pods`和 。 控制台窗格显示容器引擎生成的日志、事件和指标，以进一步帮助实时解决问题。

本文提供了详细的概述，并帮助您了解如何使用此功能。 

>[!NOTE]
>此功能不支持作为[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)启用的 AKS 群集。 此功能依赖于通过浏览器通过代理服务器直接访问 Kubernetes API。 启用网络安全性来阻止 Kubernetes API 来自此代理将阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 区域均可用，包括Azure 中国。 它目前在 Azure 美国政府版中不可用。

有关设置或排除实时数据（预览）功能的帮助，请查看我们的[设置指南](container-insights-livedata-setup.md)。 此功能直接访问 Kubernetes API，有关身份验证模型的其他信息可[在此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。 

## <a name="live-data-preview-functionality-overview"></a>实时数据（预览）功能概述

### <a name="search"></a>搜索

![实时数据控制台窗格筛选器示例](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

实时数据（预览）功能包括搜索功能。 在 **"搜索"** 字段中，可以通过键入一个关键词或术语来筛选结果，并突出显示任何匹配的结果以允许快速查看。 在查看事件时，可以进一步对结果进行限制，只需使用在搜索栏右侧发现的“筛选器”**** 药丸图标即可。 根据您选择的资源，该药片列出了要从中选择的 Pod、命名空间或群集。  

### <a name="scroll-lock-and-pause"></a>滚动锁定和暂停 

要挂起自动滚动并控制窗格的行为，允许您手动滚动遍读的新数据，可以使用 **"滚动"** 选项。 要重新启用自动滚动，只需再次选择 **"滚动"** 选项即可。 您还可以通过选择 **"暂停"** 选项暂停日志或事件数据的检索，当您准备好恢复时，只需选择 **"播放**"。  

![实时数据控制台窗格暂停实时视图](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>我们建议在排除故障时仅暂停或暂停自动滚动短时间。 这些请求可能会影响群集上的 Kubernetes API 的可用性和限制。 

>[!IMPORTANT]
>在此功能操作期间，不会永久存储任何数据。 当您关闭浏览器或导航远离浏览器时，会话期间捕获的所有信息都将被删除。 数据仅保留在指标要素的五分钟窗口内进行可视化;任何超过五分钟的指标也会被删除。 实时数据（预览）缓冲区查询在合理的内存使用限制内（需要在这里更具体，什么是合理的？ 

## <a name="view-logs"></a>查看日志

您可以查看实时日志数据，因为它们由容器引擎从**节点**、**控制器**和**容器**视图生成。 要查看日志数据，请执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组并选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的**监视**下，选择 **"见解**"。 

3. 选择"**节点**、**控制器**"或 **"容器"** 选项卡。

4. 从性能网格中选择对象，在右侧找到的属性窗格上，选择 **"查看实时数据（预览）"** 选项。 如果 AKS 群集配置了使用 Azure AD 的单一登录，系统将提示您在该浏览器会话期间首次使用时进行身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

    >[!NOTE]
    >通过从属性窗格中选择 **"分析中的视图"** 选项来查看日志分析工作区中的数据时，日志搜索结果可能会显示可能不再存在的**节点**、**守护进程集**、**副本集**、**作业****、Cron 作业****、Pod**和**容器**。 尝试搜索日志以查找不在 的`kubectl`容器也会失败。 查看[分析中的视图](container-insights-log-search.md#search-logs-to-analyze-data)功能，了解有关查看历史日志、事件和指标的更多信息。  

成功验证后，"实时数据（预览"）控制台窗格将显示在性能数据网格下方，您可以在其中查看连续流的日志数据。 如果提取状态指示器显示位于窗格最右侧的绿色复选标记，则意味着可以检索数据并开始流式传输到您的控制台。  

![节点属性窗格视图数据选项](./media/container-insights-livedata-overview/node-properties-pane.png)  

窗格标题会显示进行容器分组时所使用的 Pod 的名称。

## <a name="view-events"></a>查看事件

当选择容器、pod、节点、副本集、守护蒙集、作业、CronJob 或部署时，可以从容器引擎从 **"节点**、**控制器**、**容器**和**部署（预览）"** 视图中查看它们生成的实时事件数据。 要查看事件，执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组并选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的**监视**下，选择 **"见解**"。 

3. 选择 **"节点**、**控制器**、**容器**"或 **"部署（预览）"** 选项卡。

4. 从性能网格中选择对象，在右侧找到的属性窗格上，选择 **"查看实时数据（预览）"** 选项。 如果 AKS 群集配置了使用 Azure AD 的单一登录，系统将提示您在该浏览器会话期间首次使用时进行身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

    >[!NOTE]
    >通过从属性窗格中选择 **"分析中的视图"** 选项来查看日志分析工作区中的数据时，日志搜索结果可能会显示可能不再存在的**节点**、**守护进程集**、**副本集**、**作业****、Cron 作业****、Pod**和**容器**。 尝试搜索日志以查找不在 的`kubectl`容器也会失败。 查看[分析中的视图](container-insights-log-search.md#search-logs-to-analyze-data)功能，了解有关查看历史日志、事件和指标的更多信息。  

成功验证后，"实时数据（预览"）控制台窗格将显示在性能数据网格下方。 如果提取状态指示器显示位于窗格最右侧的绿色复选标记，则意味着可以检索数据并开始流式传输到您的控制台。 
    
如果所选对象是容器，请在窗格中选择"**事件**"选项。 如果选择了节点、Pod 或控制器，则会自动选择查看事件。 

![控制器属性窗格视图事件](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

窗格标题显示容器分组的 Pod 的名称。

### <a name="filter-events"></a>筛选事件 

在查看事件时，可以进一步对结果进行限制，只需使用在搜索栏右侧发现的“筛选器”**** 药丸图标即可。 根据您选择的资源，该药片列出了要从中选择的 Pod、命名空间或群集。  

## <a name="view-metrics"></a>查看指标 

您可以查看实时指标数据，因为它们由容器引擎从**节点**或**控制器**视图中生成，仅当选择**Pod**时。 要查看指标，执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组并选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的**监视**下，选择 **"见解**"。 

3. 选择"**节点**"或"**控制器"** 选项卡。

4. 从性能网格中选择**Pod**对象，在右侧找到的属性窗格上，选择 **"查看实时数据（预览）"** 选项。 如果 AKS 群集配置了使用 Azure AD 的单一登录，系统将提示您在该浏览器会话期间首次使用时进行身份验证。 选择帐户，然后使用 Azure 完成身份验证。  

    >[!NOTE]
    >通过从属性窗格中选择 **"分析中的视图"** 选项来查看日志分析工作区中的数据时，日志搜索结果可能会显示可能不再存在的**节点**、**守护进程集**、**副本集**、**作业****、Cron 作业****、Pod**和**容器**。 尝试搜索日志以查找不在 的`kubectl`容器也会失败。 查看[分析中的视图](container-insights-log-search.md#search-logs-to-analyze-data)功能，了解有关查看历史日志、事件和指标的更多信息。  

成功验证后，"实时数据（预览"）控制台窗格将显示在性能数据网格下方。 将检索指标数据，并开始流式传输到您的控制台，以便在两个图表中进行演示。 窗格标题会显示进行容器分组时所使用的 Pod 的名称。

![查看 Pod 指标示例](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>后续步骤

- 要继续了解如何使用 Azure 监视器并监视 AKS 群集的其他方面，[请参阅查看 Azure 库伯奈斯服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看预定义的查询和示例，以创建警报、可视化或对群集执行进一步分析。
