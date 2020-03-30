---
title: 使用 Azure 监视器实时查看容器指标 |微软文档
description: 本文介绍了指标的实时视图，而不将库布ectl与 Azure 监视器一起使用容器。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216594"
---
# <a name="how-to-view-metrics-in-real-time"></a>如何实时查看指标

Azure 监视器用于容器实时数据（预览）功能，允许您实时可视化有关群集中节点和 Pod 状态的指标。 它模拟直接访问`kubectl top nodes`，`kubectl get pods –all-namespaces`和`kubectl get nodes`命令来调用、 分析和可视化此 Insight 中包含的性能图表中的数据。 

本文提供了详细的概述，并帮助您了解如何使用此功能。  

>[!NOTE]
>此功能不支持作为[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)启用的 AKS 群集。 此功能依赖于通过浏览器通过代理服务器直接访问 Kubernetes API。 启用网络安全性来阻止 Kubernetes API 来自此代理将阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 区域均可用，包括Azure 中国。 它目前在 Azure 美国政府版中不可用。

有关设置或排除实时数据（预览）功能或故障排除的帮助，请查看我们的[设置指南](container-insights-livedata-setup.md)。

## <a name="how-it-works"></a>工作方式 

实时数据（预览）功能直接访问 Kubernetes API，有关身份验证模型的其他信息可[在此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。 

此功能对指标终结点（包括`/api/v1/nodes`，`/apis/metrics.k8s.io/v1beta1/nodes`和`/api/v1/pods`） 执行轮询操作，默认情况下每五秒执行一次轮询操作。 此数据缓存在浏览器中，并通过选择 **"Go Live"（预览），** 在 **"群集**"选项卡上的容器 Azure 监视器中包含的四个性能图表中绘制图表。 每个后续轮询都绘制成滚动的五分钟可视化窗口。 

![在群集视图中上线选项](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

轮询间隔从 **"设置"间隔**下拉列表进行配置，允许您每 1、5、15 和 30 秒设置新数据的轮询。 

![转到实时下拉轮询间隔](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>我们建议将轮询间隔设置为一秒，同时对问题进行短期故障排除。 这些请求可能会影响群集上的 Kubernetes API 的可用性和限制。 之后，重新配置为更长的轮询间隔。 

>[!IMPORTANT]
>在此功能操作期间，不会永久存储任何数据。 当您关闭浏览器或导航远离功能时，将立即删除此会话期间捕获的所有信息。 数据仅保留在五分钟窗口内进行可视化;任何超过五分钟的指标也会永久删除。

无法将这些图表固定到您在实时模式下查看的最后一个 Azure 仪表板。

## <a name="metrics-captured"></a>指标捕获

### <a name="node-cpu-utilization---node-memory-utilization-"></a>节点 CPU 利用率 % / 节点内存利用率 % 

这两个性能图表映射到等效于调用`kubectl top nodes`和捕获 CPU% 和**MEMORY%** 列**MEMORY%** 的结果到相应的图表。 

![库布克特尔顶部节点示例结果](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![节点 CPU 利用率百分比图表](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![节点内存利用率百分比图表](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

百分位数计算将在较大的群集中起作用，以帮助识别群集中的异常值节点。 例如，了解节点是否未用于缩减用途。 利用**最小**聚合，您可以看到群集中的哪些节点利用率较低。 要进一步调查，请选择 **"节点"** 选项卡，然后按 CPU 或内存利用率对网格进行排序。

这还可以帮助您了解哪些节点被推送到其限制，以及是否需要横向扩展。 利用**Max**和**P95**聚合可以帮助您了解群集中是否有具有高资源利用率的节点。 为了进一步调查，您将再次切换到 **"节点"** 选项卡。

### <a name="node-count"></a>节点计数

此性能图表映射到等效于调用`kubectl get nodes` **STATUS**列并将其映射到按状态类型分组的图表。

![库布克特尔获取节点示例结果](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![节点计数图表](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

节点以 **"就绪"** 或 **"未就绪**"状态报告。 它们被计数（并创建总计数），并绘制这两个聚合的结果。
例如，了解节点是否处于失败状态。 利用 **"未就绪"** 聚合，您可以快速查看群集中当前处于 **"未就绪"** 状态的节点数。

### <a name="active-pod-count"></a>活动窗格计数

此性能图表映射到等效于调用`kubectl get pods –all-namespaces`和映射按状态类型分组的"**状态**"列。

![库布克特尔获取窗格示例结果](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![节点窗格计数图表](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>由 其`kubectl`解释的状态名称在图表中可能不完全匹配。 

## <a name="next-steps"></a>后续步骤

查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看预定义的查询和示例，以创建警报、可视化或对群集执行进一步分析。
