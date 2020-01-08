---
title: 与 Azure Monitor 容器一起实时查看指标 |Microsoft Docs
description: 本文介绍了度量值的实时视图，而无需将 kubectl 与容器的 Azure Monitor 一起使用。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: fbb08a8ed3deeff061065916241ee2d724603be3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404935"
---
# <a name="how-to-view-metrics-in-real-time"></a>如何实时查看指标

容器的 Azure Monitor "实时数据（预览）" 功能允许您实时直观显示群集中节点和 pod 状态的指标。 它模拟对 `kubectl top nodes`、`kubectl get pods –all-namespaces`和 `kubectl get nodes` 命令的直接访问，以便在此见解中包含的性能图表中调用、分析和可视化数据。 

本文提供详细的概述，帮助你了解如何使用此功能。  

>[!NOTE]
>此功能不支持将 AKS 群集作为[专用群集](https://azure.microsoft.com/updates/aks-private-cluster/)启用。 此功能依赖于通过代理服务器直接从浏览器访问 Kubernetes API。 启用网络安全以阻止此代理中的 Kubernetes API 会阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 区域（包括 Azure 中国）中可用。 它当前在 Azure 美国政府版中不可用。

若要获取有关设置实时数据（预览）功能或对其进行故障排除的帮助，请参阅我们的[安装指南](container-insights-livedata-setup.md)。

## <a name="how-it-works"></a>工作原理 

实时数据（预览版）功能直接访问 Kubernetes API，还可以在[此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到有关身份验证模型的其他信息。 

此功能对指标终结点（包括 `/api/v1/nodes`、`/apis/metrics.k8s.io/v1beta1/nodes`和 `/api/v1/pods`）执行轮询操作，默认情况下每隔五秒。 此数据在浏览器中进行缓存，并在 "**群集**" 选项卡的 "Azure Monitor 容器" 中包含的四个性能图表中，通过选择 "上线 **（预览）** "。 每个后续轮询都将绘制到滚动五分钟的可视化窗口中。 

![群集视图中的 "上线" 选项](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

"轮询间隔" 是从 "**设置间隔**" 下拉项配置的，使你可以每隔1、5、15和30秒为新数据设置轮询。 

![上线下拉轮询间隔](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.ping.png)

>[!IMPORTANT]
>建议在一小段时间内对问题进行故障排除时，将轮询间隔设置为1秒。 这些请求可能会影响群集上 Kubernetes API 的可用性和限制。 之后，重新配置为更长的轮询间隔。 

>[!IMPORTANT]
>在操作此功能的过程中，不会永久存储数据。 当你关闭浏览器或离开此功能时，将立即删除在此会话期间捕获的所有信息。 数据只会在五分钟窗口内保持可视化的显示;任何超过五分钟的指标也会永久删除。

无法将这些图表固定到在实时模式下查看的上一个 Azure 仪表板。

## <a name="metrics-captured"></a>指标捕获

### <a name="node-cpu-utilization---node-memory-utilization-"></a>节点 CPU 使用率%/节点内存使用率百分比 

这两个性能图表对应于调用 `kubectl top nodes`，并将**CPU%** 和**内存%** 列的结果捕获到相应的图表中。 

![Kubectl 顶级节点示例结果](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![节点 CPU 使用率百分比图表](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![节点内存使用率百分比图表](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

百分点计算将在较大的群集中运行，以帮助确定群集中的离群节点。 例如，若要了解节点是否受到充分利用以降低规模的目的。 利用**最小**聚合，可以查看群集中的使用率较低的节点。 若要进一步调查，请选择 "**节点**" 选项卡，并按 CPU 或内存使用率对网格进行排序。

这还可以帮助你了解哪些节点正在被推送到其限制中以及是否需要向外扩展。 利用**Max**和**P95**聚合，可以帮助你查看群集中是否存在具有高资源利用率的节点。 为了进一步进行调查，将再次切换到 "**节点**" 选项卡。

### <a name="node-count"></a>节点计数

此性能图表映射到 `kubectl get nodes` 调用的等效项，并将**状态**列映射到按状态类型分组的图表。

![Kubectl 获取节点示例结果](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![节点计数图表](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

节点将报告为 "**就绪**" 或 "**未就绪**" 状态。 将对它们进行计数（并且创建总计数），这两个聚合的结果将绘制到图表中。
例如，要了解节点是否处于失败状态。 利用 "**未就绪**" 聚合，可以快速查看群集中当前处于 "**未就绪**" 状态的节点数。

### <a name="active-pod-count"></a>活动 pod 计数

此性能图表映射到调用 `kubectl get pods –all-namespaces` 的等效项，并按状态类型映射图表的**状态**列。

![Kubectl 获取盒示例结果](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![节点 pod 计数图表](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>`kubectl` 解释的状态名称在图表中可能不完全匹配。 

## <a name="next-steps"></a>后续步骤

查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看用于创建警报、可视化效果或进一步分析群集的预定义查询和示例。
