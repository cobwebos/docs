---
title: 使用用于容器的 Azure Monitor 实时查看指标 | Microsoft Docs
description: 本文介绍在用于容器的 Azure Monitor 中如何在不使用 kubectl 的情况下实时查看指标。
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 81d7210778fd6b5d75fb4b4fa8e066d2e015174f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85338018"
---
# <a name="how-to-view-metrics-in-real-time"></a>如何实时查看指标

借助用于容器的 Azure Monitor 实时数据（预览版）功能，你可以实时可视化群集中有关节点和 pod 状态的指标。 它模拟对 `kubectl top nodes`、`kubectl get pods –all-namespaces` 和 `kubectl get nodes` 命令的直接访问，以调用、分析和可视化此见解中包含的性能图表中的数据。

本文详细介绍了此功能，并帮助你了解如何使用此功能。

>[!NOTE]
>此功能不支持以[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)形式启用的 AKS 群集。 此功能依赖于从浏览器通过代理服务器直接访问 Kubernetes API。 启用网络安全以阻止来自此代理的 Kubernetes API 阻止此流量。

有关设置实时数据（预览版）功能或对其进行故障排除的帮助，请参阅[安装指南](container-insights-livedata-setup.md)。

## <a name="how-it-works"></a>工作方式

实时数据（预览版）功能可直接访问 Kubernetes API；有关身份验证模型的其他信息，请参阅[此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)。

该功能对指标终结点（包括 `/api/v1/nodes`、`/apis/metrics.k8s.io/v1beta1/nodes` 和 `/api/v1/pods`）执行轮询操作，默认每 5 秒轮询一次。 这些数据缓存在浏览器中，你可以在用于容器的 Azure Monitor 中将它们绘制成四个性能图表，操作方法是在“群集”选项卡上选择“上线(预览)” 。 每个后续轮询都将绘制到一个滚动显示五分钟的可视化窗口中。

![“群集”视图中的“上线”选项](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

轮询间隔在“设置间隔”下拉列表中配置，允许你每 1、5、15 和 30 秒为新数据设置一次轮询。

![“上线”下拉轮询间隔](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>建议将轮询间隔设置为 1 秒，并在较短的时间内排除问题。 这些请求可能会影响群集上 Kubernetes API 的可用性和限制。 然后，重新配置为更长的轮询间隔。

>[!IMPORTANT]
>此功能运行期间不会永久存储任何数据。 当你关闭浏览器或退出此功能时，在此会话期间捕获的所有信息将立即删除。 数据只在一个显示五分钟的窗口内以可视化效果呈现；任何超过五分钟的指标也将永久删除。

无法将这些图表固定到在实时模式下查看的最后一个 Azure 仪表板。

## <a name="metrics-captured"></a>指标捕获

### <a name="node-cpu-utilization---node-memory-utilization-"></a>节点 CPU 利用率/节点内存利用率

这两个性能图表分别对应于调用 `kubectl top nodes` 并将 CPU% 和 MEMORY% 列的结果捕获到相应的图表 。

![Kubectl top nodes 示例结果](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![节点 CPU 利用率百分比图表](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![节点内存利用率百分比图表](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

百分位数计算在较大的群集中很有用，可帮助识别群集中的异常节点。 例如，了解节点是否未充分利用，以便纵向缩减。 使用 Min 聚合，可以看到群集中哪些节点的利用率较低。 要进一步调查，请选择“节点”选项卡，并按 CPU 或内存利用率对网格进行排序。

这也有助于你了解哪些节点即将到达极限以及是否需要横向扩展。 同时使用 Max 和 P95 聚合可以帮助你查看群集中是否存在具有高资源利用率的节点 。 为了进一步调查，你将再次切换到“节点”选项卡。

### <a name="node-count"></a>节点计数

此性能图表对应于 `kubectl get nodes` 并将“状态”列映射到按状态类型分组的图表。

![Kubectl get nodes 示例结果](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![节点计数图表](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

节点报告为“就绪”或“未就绪”状态 。 将对这些节点进行计数（并创建总计数），这两个聚合的结果将绘制到图表中。
例如，了解节点是否处于失败状态。 利用“未就绪”聚合，你可以快速看到群集中当前处于“未就绪”状态的节点数 。

### <a name="active-pod-count"></a>活动 Pod 计数

此性能图表对应于调用 `kubectl get pods –all-namespaces` 并将“状态”列映射到按状态类型分组的图表。

![Kubectl get pods 示例结果](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![节点 pod 计数图表](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>`kubectl` 解释的状态名称可能与图表中不完全一致。

## <a name="next-steps"></a>后续步骤

查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以创建警报、呈现可视化效果或对群集执行进一步分析。
