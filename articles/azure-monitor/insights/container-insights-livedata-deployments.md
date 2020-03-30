---
title: 查看容器部署（预览）的 Azure 监视器 |微软文档
description: 本文介绍了 Kubernetes 部署的实时视图，而不在 Azure 监视器中使用容器的 kubectl。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404770"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>如何实时查看部署（预览）

使用容器的 Azure 监视器，视图部署（预览）功能通过公开`kubeclt get deployments`和`kubectl describe deployment {your deployment}`命令来实时模拟对 Kubernetes 部署对象的直接访问。 

>[!NOTE]
>此功能不支持作为[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)启用的 AKS 群集。 此功能依赖于通过浏览器通过代理服务器直接访问 Kubernetes API。 启用网络安全性来阻止 Kubernetes API 来自此代理将阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 区域均可用，包括Azure 中国。 它目前在 Azure 美国政府版中不可用。

要了解更多信息，请查看库伯奈斯关于[部署的文档](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)。 

## <a name="how-it-works"></a>工作原理

实时数据（预览）功能直接访问 Kubernetes API，有关身份验证模型的其他信息可[在此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。 

部署（预览）功能对部署终结点`/apis/apps/v1/deployments`执行一次性（可刷新）负载。 它允许您选择给定的部署，并针对部署终结点`/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`加载该特定部署的描述详细信息。 

选择页面左上角的 **"刷新"** 会刷新部署列表。 这将模拟重新运行该`kubectl`命令。 

>[!IMPORTANT]
>在此功能操作期间，不会永久存储任何数据。 当您关闭浏览器或导航远离浏览器时，会话期间捕获的所有信息都将被删除。  

>[!NOTE]
>不能将实时数据（预览）数据从控制台固定到 Azure 仪表板。

## <a name="deployments-describe"></a>部署描述

要查看部署的"描述详细信息"（相当于`kubectl describe deployment`）执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组并选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的**监视**下，选择 **"见解**"。 

3. 选择 **"部署（预览）"** 选项卡。

    ![Azure 门户中的部署视图](./media/container-insights-livedata-deployments/deployment-view.png)

该视图显示所有正在运行的部署的列表以及命名空间和其他详细信息，模拟命令`kubectl get deployments –all-namespaces`的执行。 您可以通过选择任一列对结果进行排序。 

![部署属性窗格详细信息](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

从列表中选择部署时，属性窗格会自动显示在页面右侧。 它显示与所选部署相关的信息，如果运行命令`kubectl describe deployment {deploymentName}`，您将查看这些信息。 您可能已经注意到描述信息缺少一些详细信息。 最值得注意的是**缺少模板**。 选择 **"原始**"选项卡，可以导航到未解析的"描述"详细信息。  

![部署属性窗格原始详细信息](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

查看部署详细信息时，可以实时查看容器日志和事件。 选择 **"显示实时控制台**"，"实时数据（预览"）控制台窗格将显示在部署数据网格下方，您可以在其中查看连续流中的实时日志数据。 如果提取状态指示器显示位于窗格最右侧的绿色复选标记，则意味着可以检索数据并开始流式传输到您的控制台。

还可以按命名空间或群集级别事件进行筛选。 要了解有关在控制台中实时查看数据的信息，请参阅使用 Azure[监视器查看实时数据（预览）以访问容器](container-insights-livedata-overview.md)。 

![部署在控制台中查看实时数据](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>后续步骤

- 要继续了解如何使用 Azure 监视器并监视 AKS 群集的其他方面，[请参阅查看 Azure 库伯奈斯服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看预定义的查询和示例，以创建警报、可视化或对群集执行进一步分析。
