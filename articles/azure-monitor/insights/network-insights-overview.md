---
title: 网络 Azure 监视器（预览）
description: 网络 Azure 监视器的快速概述，它为所有部署的网络资源提供运行状况和指标的全面视图，而无需任何配置。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654862"
---
# <a name="azure-monitor-for-networks-preview"></a>网络 Azure 监视器（预览）
Azure 网络监视器为所有部署的网络资源提供运行状况和指标的全面视图，而无需任何配置。 高级搜索功能可帮助识别资源依赖项，从而启用诸如通过搜索托管网站名称来标识托管网站的资源等方案。

"Azure 网络**监视器概述**"页提供了一种轻松的方式来可视化网络资源的清单以及资源运行状况和警报。 它分为四个关键功能领域：

- 搜索和筛选
- 资源运行状况和指标
- 警报 
- 依赖项视图

![概述页](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>搜索和筛选
可以使用**订阅**、**资源组**和资源**类型**等筛选器自定义资源运行状况和警报视图。 搜索框提供搜索资源属性的功能。

搜索框可用于搜索资源和关联资源。 例如，公共 IP 与应用程序网关相关联。 搜索公共 IP DNS 名称将同时标识公共 IP 和相关的应用程序网关。

![搜索](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>资源运行状况和指标
每个磁贴表示一种资源类型，在所有订阅中部署的实例数以及资源运行状况状态一起选择。 在下面的示例中，部署了 105 个 ER 和 VPN 连接，103 个正常连接，2 个不可用。

![资源运行状况](media/network-insights-overview/resource-health.png)

单击两个不可用的 ER 和 VPN 连接，将启动指标视图。 

![指标视图](media/network-insights-overview/metric-view.png)

您可以单击网格视图中的每个元素。 单击"运行状况"图标以重定向到该连接的资源运行状况。 单击"警报"可分别重定向到该连接的警报和指标页。 

## <a name="alerts"></a>警报
右侧的 **"警报"** 网格提供所有订阅中所选资源生成的所有警报的视图。 单击警报计数以导航到详细警报页面。

## <a name="dependency-view"></a>依赖项视图
**依赖项**视图有助于可视化资源的配置方式。 当前依赖项视图仅支持应用程序网关。 可以通过单击指标网格视图中的应用程序网关资源名称来访问依赖项视图。

![应用程序网关视图](media/network-insights-overview/application-gateway.png)

应用程序网关**的依赖项**视图提供了前端 IP 如何连接到侦听器、规则和后端池的简化视图。 连接边采用颜色编码，并根据后端池运行状况提供其他详细信息。 该视图还提供了所有相关后端池（如 VMSS 和 VM 实例）的应用程序网关指标和指标的详细视图。

![依赖项视图](media/network-insights-overview/dependency-view.png)

依赖项图允许轻松导航到配置设置。 右键单击后端池以访问其他功能。 例如，如果后端池是 VM，则可以直接访问 VM 见解和网络观察程序连接疑难解答，以确定连接问题。

![依赖项视图菜单](media/network-insights-overview/dependency-view-menu.png)

依赖项视图上的搜索和筛选栏提供了一种轻松的方法来搜索图形。 例如，在下面的示例中搜索*AppGWTestRule*会将图形视图缩小到通过*AppGWTestRule*连接的所有节点。 

![搜索示例](media/network-insights-overview/search-example.png)

不同的筛选器有助于缩小到特定的路径和状态。 例如，从"**运行状况状态**"下拉列表中仅选择 *"不正常"* 以显示状态为 *"不正常"* 的所有边。

单击 **"详细指标视图**"可启动预配置的工作簿，其中包含应用程序网关、所有后端池资源和前端 IP 的详细指标。 

## <a name="next-steps"></a>后续步骤 

- 在什么是[Azure 网络观察程序上](/azure/network-watcher/network-watcher-monitoring-overview)了解有关网络监视的更多信息？
