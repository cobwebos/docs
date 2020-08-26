---
title: Azure 网络监视器（预览版）
description: 简要概述 Azure 网络监视器，它为已部署的所有网络资源提供运行状况和指标的全面视图，并且无需任何配置。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: ccc5578944dc6eea9a62360045272896d78e2fac
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128669"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure 网络监视器（预览版）
Azure 网络监视器为已部署的所有网络资源提供运行状况和指标的全面视图，并且无需任何配置。 高级搜索功能可帮助标识资源依赖项，以启用相关方案，例如通过直接搜索托管网站名称来标识托管网站的资源。

Azure 网络监视器的“概览”页面提供了一种轻松的方法来可视化网络资源的清单以及资源运行状况和警报。 它分为四个主要功能区域：

- 搜索和筛选
- 资源运行状况和指标
- 警报 
- “依赖项”视图

![概述页](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>搜索和筛选
可以使用“订阅”、“资源组”和“资源类型”等筛选器来自定义资源运行状况和警报视图  。 搜索框提供搜索资源属性的功能。

搜索框可用于搜索资源和关联资源。 例如，公共 IP 与应用程序网关相关联。 搜索公共 IP DNS 名称将同时标识公共 IP 和关联的应用程序网关。

![搜索](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>资源运行状况和指标
每个磁贴代表一种资源类型，其中显示了所选全部订阅中部署的实例数以及资源运行状况状态。 在下面的示例中，部署了 105 个 ER 和 VPN 连接，103 个正常，2 个不可用。

![资源运行状况](media/network-insights-overview/resource-health.png)

单击这两个不可用的 ER 和 VPN 连接，将启动指标视图。 

![指标视图](media/network-insights-overview/metric-view.png)

你可以单击网格视图中的每个元素。 单击“运行状况”图标可重定向到该连接的资源运行状况。 单击“警报”可分别重定向到该连接的“警报和指标”页面。 

## <a name="alerts"></a>警报
右侧的“警报”网格提供所有订阅中选定资源生成的所有警报的视图。 单击警报计数可导航到详细的警报页面。

## <a name="dependency-view"></a>“依赖项”视图
“依赖项”视图有助于可视化资源的配置方式。 目前，应用程序网关、虚拟 WAN 和负载均衡器现在支持依赖项视图。 例如，对于应用程序网关，可以通过在指标网格视图中单击应用程序网关资源名称来访问 "依赖关系" 视图。 这也适用于虚拟 WAN 和负载均衡器。 

![“应用程序网关”视图](media/network-insights-overview/application-gateway.png)

应用程序网关的“依赖项”视图提供了前端 IP 如何连接到侦听器、规则和后端池的简化视图。 连接边缘采用颜色编码，并根据后端池运行状况提供其他详细信息。 该视图还提供了所有相关后端池（如 VMSS 和 VM 实例）的应用程序网关指标的详细视图。

![“依赖项”视图](media/network-insights-overview/dependency-view.png)

通过依赖项图可轻松导航到配置设置。 右键单击后端池可访问其他功能。 例如，如果后端池是 VM，则可以直接访问 VM Insights 和网络观察程序连接疑难解答，以确定连接问题。

![“依赖项”视图菜单](media/network-insights-overview/dependency-view-menu.png)

“依赖项”视图上的搜索和筛选栏提供了一种轻松的方法来搜索图形。 例如，在下面的示例中搜索 AppGWTestRule 会将图形视图缩小到通过 AppGWTestRule 连接的所有节点 。 

![搜索示例](media/network-insights-overview/search-example.png)

不同的筛选器有助于缩小到特定的路径和状态。 例如，从“运行状况状态”下拉列表中仅选择“运行不正常”可显示状态为“运行不正常”的所有边缘。

单击“详细指标视图”可启动预配置的工作簿，其中包含应用程序网关、所有后端池资源和前端 IP 的详细指标。 

## <a name="next-steps"></a>后续步骤 

- 要详细了解网络监视，请参阅[什么是 Azure 网络观察程序？](../../network-watcher/network-watcher-monitoring-overview.md)。
