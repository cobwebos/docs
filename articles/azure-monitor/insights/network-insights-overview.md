---
title: 网络 Azure Monitor （预览）
description: Azure Monitor 的网络概述，提供所有已部署的网络资源的运行状况和指标的综合视图，无需任何配置。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 3bb92b85779366081349dab777be67e063469678
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73840049"
---
# <a name="azure-monitor-for-networks-preview"></a>网络 Azure Monitor （预览）
网络 Azure Monitor 为所有部署的网络资源提供运行状况和指标的综合视图，无需任何配置。 高级搜索功能可帮助识别资源依赖关系，从而通过只需搜索托管网站名称来识别托管网站的资源。

"网络 Azure Monitor**概述**" 页提供一种简便的方式来可视化网络资源的清单以及资源运行状况和警报。 它分为四个主要功能区域：

- 搜索和筛选
- 资源运行状况和指标
- 警报 
- 依赖关系视图

![概述页](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>搜索和筛选
可以使用**订阅**、**资源组**和**资源类型**等筛选器自定义 "资源运行状况" 和 "警报" 视图。 "搜索" 框提供了搜索资源属性的功能。

"搜索" 框可用于搜索资源和关联的资源。 例如，公共 IP 关联到应用程序网关。 搜索公共 IP DNS 名称将标识公共 IP 和关联的应用程序网关。

![搜索](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>资源运行状况和指标
每个磁贴代表一种资源类型，并在选择的所有订阅上部署的实例数以及资源运行状况状态。 在下面的示例中，部署了 105 ER 和 VPN 连接，103处于正常状态，2不可用。

![资源运行状况](media/network-insights-overview/resource-health.png)

单击两个不可用的 ER 和 VPN 连接时，将启动一个度量值视图。 

![度量值视图](media/network-insights-overview/metric-view.png)

您可以在网格视图中单击每个元素。 单击 "运行状况" 图标以重定向到该连接的资源运行状况。 单击 "警报"，分别重定向到该连接的 "警报和指标" 页。 

## <a name="alerts"></a>警报
右侧的 "**警报**" 网格提供在所有订阅中为所选资源生成的所有警报的视图。 单击警报计数以导航到 "详细警报" 页。

## <a name="dependency-view"></a>依赖关系视图
**依赖关系**视图可帮助可视化资源的配置方式。 目前，应用程序网关仅支持依赖项视图。 可以通过在指标网格视图中单击应用程序网关资源名称来访问依赖关系视图。

![应用程序网关视图](media/network-insights-overview/application-gateway.png)

应用程序网关的**依赖关系**视图提供了有关前端 ip 如何连接到侦听器、规则和后端池的简化视图。 连接边缘是颜色编码的，并提供基于后端池运行状况的其他详细信息。 此视图还提供了应用程序网关指标和指标的详细视图，适用于所有相关的后端池，如 VMSS 和 VM 实例。

![依赖关系视图](media/network-insights-overview/dependency-view.png)

依赖项关系图可轻松导航到配置设置。 右键单击后端池可以访问其他功能。 例如，如果后端池是 VM，则可以直接访问 VM Insights 和网络观察程序连接疑难解答，以确定连接问题。

![依赖关系视图菜单](media/network-insights-overview/dependency-view-menu.png)

"依赖关系" 视图上的搜索和筛选栏提供了一种简便的方式来搜索图形。 例如，在以下示例中搜索*AppGWTestRule*会将图形视图缩小到通过*AppGWTestRule*连接的所有节点。 

![搜索示例](media/network-insights-overview/search-example.png)

不同的筛选器提供有助于缩小到特定路径和状态的帮助。 例如，从 "**运行状况状态**" 下拉选择 "仅不*正常*"，以显示状态不*正常*的所有边缘。

单击 "**详细指标视图**" 以启动预配置的工作簿，其中包含应用程序网关、所有后端池资源和前端 ip 的详细指标。 

## <a name="next-steps"></a>后续步骤 

- 有关网络监视的详细信息，请参阅[Azure 网络观察](/azure/network-watcher/network-watcher-monitoring-overview)程序是什么？。
