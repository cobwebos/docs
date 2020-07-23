---
title: 查看适用于容器的 Azure Monitor 的部署（预览版）| Microsoft Docs
description: 本文介绍在不使用适用于容器的 Azure Monitor 中的 kubectl 的情况下，如何实时查看 Kubernetes 部署。
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2f1eac82ce67818c7bf86ce3ca8924155d8ee2aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337988"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>如何实时查看部署（预览版）

在适用于容器的 Azure Monitor 中，查看部署（预览版）功能通过公开 `kubeclt get deployments` 和 `kubectl describe deployment {your deployment}` 命令实时模拟对 Kubernetes 部署对象的直接访问。

>[!NOTE]
>此功能不支持以[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)形式启用的 AKS 群集。 此功能依赖于从浏览器通过代理服务器直接访问 Kubernetes API。 启用网络安全以阻止来自此代理的 Kubernetes API 阻止此流量。

若要了解详细信息，请查看有关[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)的 Kubernetes 文档。

## <a name="how-it-works"></a>工作原理

实时数据（预览版）功能可直接访问 Kubernetes API；有关身份验证模型的其他信息，请参阅[此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)。

部署（预览版）功能对部署终结点 `/apis/apps/v1/deployments` 执行一次（可刷新）加载。 它允许你选择给定的部署，并针对部署终结点 `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` 加载针对该特定部署的说明详细信息。

选择页面左上角的“刷新”将刷新部署列表。 这会模拟重新运行 `kubectl` 命令。

>[!IMPORTANT]
>此功能运行期间不会永久存储任何数据。 当你关闭或离开浏览器时，将删除在此会话期间捕获的所有信息。

>[!NOTE]
>不能将实时数据（预览版）数据从控制台固定到 Azure 仪表板。

## <a name="deployments-describe"></a>部署描述

若要查看部署的描述详细信息（与 `kubectl describe deployment` 等效），请执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组，然后选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的“监视”下，选择“见解” 。

3. 选择“部署(预览版)”选项卡。

    ![Azure 门户中的部署视图](./media/container-insights-livedata-deployments/deployment-view.png)

此视图显示所有正在运行的部署的列表，以及命名空间和其他详细信息，模拟了 `kubectl get deployments –all-namespaces` 命令的执行。 可以通过选择任何一列对结果进行排序。

![部署属性窗格详细信息](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

从列表中选择部署时，页面右侧将自动显示属性窗格。 其中显示了在运行 `kubectl describe deployment {deploymentName}` 命令时将看到的已选部署的相关信息。 你可能已注意到描述信息缺少一些详细信息。 最明显的是缺少模板。 选择“原始”选项卡可以导航到未分析的描述详细信息。

![部署属性窗格原始详细信息](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

查看部署详细信息时，可以实时查看容器日志和事件。 选择“显示实时控制台”，实时数据（预览版）控制台窗格将显示在部署数据网格下，你可以在其中查看连续流中的实时日志数据。 如果提取状态指示器显示绿色复选标记（位于窗格最右侧），则表示可以检索数据并开始将其流式传输到控制台。

还可以按命名空间或群集级别事件进行筛选。 若要了解有关在控制台中实时查看数据的详细信息，请参阅[使用适用于容器的 Azure Monitor 查看实时数据（预览版）](container-insights-livedata-overview.md)。

![在控制台中查看实时数据的部署](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>后续步骤

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以创建警报、呈现可视化效果或对群集执行进一步分析。
