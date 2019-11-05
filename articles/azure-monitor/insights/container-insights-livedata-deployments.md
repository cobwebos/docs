---
title: 查看容器部署 Azure Monitor （预览版） |Microsoft Docs
description: 本文介绍 Kubernetes 部署的实时视图，而无需在容器 Azure Monitor 中使用 kubectl。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: c7135aec98936b5247b39ae659e21735168bd289
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514376"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>如何实时查看部署（预览）

对于容器 Azure Monitor，视图部署（预览版）功能通过公开 `kubeclt get deployments` 和 `kubectl describe deployment {your deployment}` 命令实时模拟对 Kubernetes 部署对象的直接访问。 

>[!NOTE]
>此功能不支持将 AKS 群集作为[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)启用。 此功能依赖于通过代理服务器直接从浏览器访问 Kubernetes API。 启用网络安全以阻止此代理中的 Kubernetes API 会阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 区域（包括 Azure 中国）中可用。 它当前在 Azure 美国政府版中不可用。

若要了解详细信息，请查看有关[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)的 Kubernetes 文档。 

## <a name="how-it-works"></a>工作原理

实时数据（预览版）功能直接访问 Kubernetes API，还可以在[此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到有关身份验证模型的其他信息。 

部署（预览版）功能对部署终结点执行一次（可刷新）加载 `/apis/apps/v1/deployments`。 它允许你选择给定的部署，并针对部署终结点 `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`为该特定部署加载描述详细信息。 

选择页面左上角的 "**刷新**" 将刷新部署列表。 这会模拟重新运行 `kubectl` 命令。 

>[!IMPORTANT]
>在操作此功能的过程中，不会永久存储数据。 当你关闭浏览器或离开浏览器或离开浏览器时，将删除会话期间捕获的所有信息。  

>[!NOTE]
>不能将实时数据（预览版）数据从控制台固定到 Azure 仪表板。

## <a name="deployments-describe"></a>部署描述

若要查看与 `kubectl describe deployment`等效的部署的详细信息，请执行以下步骤。

1. 在 Azure 门户中，浏览到 "AKS" 群集资源组，然后选择 "AKS" 资源。

2. 在 AKS 群集仪表板的左侧 "**监视**" 下，选择 "**见解**"。 

3. 选择 "**部署（预览）** " 选项卡。

    ![Azure 门户中的部署视图](./media/container-insights-livedata-deployments/deployment-view.png)

此视图显示所有正在运行的部署的列表，以及命名空间和其他详细信息，用于模拟命令 `kubectl get deployments –all-namespaces`的执行。 您可以通过选择任何一列对结果进行排序。 

![部署属性窗格详细信息](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

从列表中选择部署时，会自动在页面右侧显示一个 "属性" 窗格。 它将显示与所选部署相关的信息，如果运行 `kubectl describe deployment {deploymentName}`命令，则会查看这些信息。 您可能已注意到说明信息缺少一些详细信息。 最值得注意的是，缺少**模板**。 选择 "**原始**" 选项卡可以导航到未分析的描述详细信息。  

![部署属性窗格原始详细信息](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

查看部署详细信息时，可以实时查看容器日志和事件。 选择 "**显示实时控制台**"，"实时数据（预览）" 控制台窗格将显示在 "部署" 数据网格下，你可以在其中查看连续流中的实时日志数据。 如果提取状态指示器显示一个绿色复选标记，该标记位于窗格的最右侧，则表示可检索数据，并开始流式传输到控制台。

还可以按命名空间或群集级别事件进行筛选。 若要详细了解如何在控制台中实时查看数据，请参阅[查看容器 Azure Monitor 的 "查看实时数据（预览版）](container-insights-livedata-overview.md)"。 

![部署查看控制台中的实时数据](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>后续步骤

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看用于创建警报、可视化效果或进一步分析群集的预定义查询和示例。