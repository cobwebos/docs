---
title: 用于网络预览的 Azure Monitor
description: 网络 Azure Monitor 的概述，为所有部署的网络资源提供运行状况和指标的综合视图，无需任何配置。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: e2a43c4d0423b286984631fda75e5ff806ae9a57
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102755"
---
# <a name="azure-monitor-for-networks-preview"></a>用于网络预览的 Azure Monitor
网络 Azure Monitor 为所有部署的网络资源提供 [运行状况](../../service-health/resource-health-checks-resource-types.md) 和 [指标](../platform/metrics-supported.md) 的综合视图，无需任何配置。 它还提供对网络监视功能的访问，如 [连接监视器](../../network-watcher/connection-monitor-preview.md)、 [网络安全组的流日志记录 (nsg) ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)和 [流量分析](../../network-watcher/traffic-analytics.md)。 还提供了其他网络 [诊断](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) 功能。

网络 Azure Monitor 是围绕以下监视的关键组件构建的：
- [网络运行状况和指标](#networkhealth)
- [连接性](#connectivity)
- [交通](#traffic)
- [诊断工具包](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>网络运行状况和指标

"网络 Azure Monitor **概述** " 页提供一种简单的方法来直观显示网络资源的清单，以及资源运行状况和警报。 它分为四个主要功能区域：搜索和筛选、资源运行状况和指标、警报和依赖关系视图。

![显示 "概述" 页的屏幕截图。](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>搜索和筛选
你可以使用 " **订阅**"、" **资源组**" 和 " **类型**" 等筛选器来自定义 "资源运行状况" 和 "警报" 视图。

您可以使用 "搜索" 框搜索资源及其关联的资源。 例如，公共 IP 与应用程序网关相关联。 搜索公共 IP 的 DNS 名称将返回公共 IP 和关联的应用程序网关：

![显示网络搜索结果 Azure Monitor 的屏幕截图。](media/network-insights-overview/search.png)


### <a name="resource-health-and-metrics"></a>资源运行状况和指标
在下面的示例中，每个磁贴表示一个资源类型。 该磁贴显示了在所有选定订阅上部署的该资源类型的实例数。 它还显示资源的运行状况状态。 在此示例中，部署了 105 ER 和 VPN 连接。 103运行正常，2不可用。

![显示网络 Azure Monitor 中资源运行状况和指标的屏幕截图。](media/network-insights-overview/resource-health.png)

如果选择不可用的 ER 和 VPN 连接，将看到指标视图： 

![屏幕截图，显示网络 Azure Monitor 的指标视图。](media/network-insights-overview/metric-view.png)

您可以在网格视图中选择任何项。 选择 " **运行状况** " 列中的图标可获取该连接的资源运行状况。 选择 " **警报** " 列中的值，以跳到连接的 "警报和指标" 页。 

### <a name="alerts"></a>警报
页面右侧的 **警报** 框提供在所有订阅中为所选资源生成的所有警报的视图。 选择 "警报计数" 以打开 "详细警报" 页。

### <a name="dependency-view"></a>“依赖项”视图
依赖关系视图可帮助你可视化资源的配置方式。 依赖关系视图当前可用于 Azure 应用程序网关、Azure 虚拟 WAN 和 Azure 负载均衡器。 例如，对于应用程序网关，可以通过在度量值网格视图中选择应用程序网关资源名称来访问 "依赖关系" 视图。 可以为虚拟 WAN 和负载均衡器执行相同的操作。

![屏幕截图，它在网络的 Azure Monitor 中显示应用程序网关视图。](media/network-insights-overview/application-gateway.png)

应用程序网关的依赖关系视图提供了有关前端 Ip 如何连接到侦听器、规则和后端池的简化视图。 连接线的颜色编码，并提供基于后端池运行状况的其他详细信息。 此视图还提供了应用程序网关指标和指标的详细视图，适用于所有相关的后端池，如虚拟机规模集和 VM 实例。

![显示网络 Azure Monitor 中的依赖关系视图的屏幕截图。](media/network-insights-overview/dependency-view.png)

依赖项关系图提供了简单的导航到配置设置。 右键单击后端池可访问其他信息。 例如，如果后端池是 VM，则可以直接访问 VM Insights 和 Azure 网络观察程序连接故障排除，以确定连接问题：

![屏幕截图，显示网络的 Azure Monitor 中的依赖关系视图菜单。](media/network-insights-overview/dependency-view-menu.png)

"依赖关系" 视图上的搜索和筛选栏提供了一种简单的方法，可在图形中进行搜索。 例如，如果在前面的示例中搜索 **AppGWTestRule** ，则该视图将缩减到通过 AppGWTestRule 连接的所有节点：

![屏幕截图显示了在网络 Azure Monitor 中搜索的示例。](media/network-insights-overview/search-example.png)

各种筛选器可帮助您缩减到特定路径和状态。 例如，从 "**运行状况状态**" 列表中选择 "仅不**正常**"，以显示状态不正常的所有边缘。

选择 " **查看详细度量值** " 以打开预配置的工作簿，该工作簿提供了应用程序网关、所有后端池资源和前端 ip 的详细指标。 

## <a name="connectivity"></a><a name="connectivity"></a>连接性

" **连接** " 选项卡提供了一种简单的方法，可通过连接监视器和 [连接监视器 (预览) ](../../network-watcher/connection-monitor-preview.md) 为所选的订阅集直观显示所有测试。

![显示网络 Azure Monitor 中的 "连接" 选项卡的屏幕截图。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

测试按 **源** 和 **目标** 磁贴分组，并显示每个测试的可访问性状态。 可访问性设置提供了根据检查失败 (% ) 和 RTT (ms) 的可访问性条件的配置。 设置值后，基于选择条件的每个测试更新的状态。

![显示网络 Azure Monitor 中的连接测试的屏幕截图。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

您可以选择任意源或目标磁贴来打开度量值视图：

![显示网络 Azure Monitor 中连接指标的屏幕截图。](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


您可以在网格视图中选择任何项。 选择可 **访问** 性列中的图标可转到 "连接监视器" 门户页面，并查看跃点拓扑和发现的影响问题的连接。 选择 " **警报** " 列中的值以跳到 "警报"。 选择 " **检查失败百分比** " 和 " **往返时间" (ms) ** 列中的关系图，以切换到所选连接监视器的 "度量值" 页。

页面右侧的 **警报** 框提供了针对所有订阅中配置的连接测试生成的所有警报的视图。 选择 "警报计数" 以打开 "详细警报" 页。

## <a name="traffic"></a><a name="traffic"></a>交通
通过 " **流量** " 选项卡，可 [访问为所](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) 选订阅集配置的所有 nsg， [流量分析](../../network-watcher/traffic-analytics.md) 并按位置分组。 利用此选项卡上提供的搜索功能，可以确定为搜索的 IP 地址配置的 Nsg。 可以在环境中搜索任意 IP 地址。 平铺区域视图将显示所有 Nsg 和 NSG 流日志，并流量分析配置状态。

![显示网络的 Azure Monitor 中的 "流量" 选项卡的屏幕截图。](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

如果选择任何区域磁贴，则会显示网格视图。 网格提供 NSG 流日志，并在易于阅读和配置的视图中流量分析：  

![屏幕截图，显示网络的 Azure Monitor 的流量区域视图。](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

您可以在网格视图中选择任何项。 选择 **Flowlog 配置状态** 列中的图标，以编辑 NSG 流日志和流量分析配置。 选择 " **警报** " 列中的值，以打开为所选 NSG 配置的流量警报。 同样，您可以通过选择 **流量分析工作区**来切换到 "流量分析" 视图。  

页面右侧的 " **警报** " 框提供了所有订阅中所有基于流量分析工作区的警报的视图。 选择 "警报计数" 以打开 "详细警报" 页。

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 诊断工具包
诊断工具包提供对网络故障排除可用的所有诊断功能的访问权限。 可以使用此下拉列表访问 [数据包捕获](../../network-watcher/network-watcher-packet-capture-overview.md)、 [VPN 故障排除](../../network-watcher/network-watcher-troubleshoot-overview.md)、 [连接故障排除](../../network-watcher/network-watcher-connectivity-overview.md)、 [下一跃点](../../network-watcher/network-watcher-next-hop-overview.md)和 [IP 流验证](../../network-watcher/network-watcher-ip-flow-verify-overview.md)等功能：

![显示 "诊断工具" 选项卡的屏幕截图。](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>疑难解答 

有关一般故障排除指南，请参阅专用的基于工作簿的 insights [故障排除一文](troubleshoot-workbooks.md)。

本部分将帮助你诊断和解决在使用网络 Azure Monitor 时可能会遇到的一些常见问题。 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>如何实现解决性能问题或失败吗？

若要了解有关在网络 Azure Monitor 中识别的与网络相关的任何问题的疑难解答，请参阅故障排除资源的故障排除文档。 

下面是一些指向常用服务疑难解答文章的链接。 有关这些服务的更多疑难解答文章，请参阅该服务的目录的 "疑难解答" 部分中的其他文章。
* [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-peering-issues)
* [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/create-gateway-internal-load-balancer-app-service-environment)
* [Azure VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot)
* [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-expressroute-overview) 
* [Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-troubleshoot) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>为什么我看不到我选择的所有订阅的资源？

网络见解一次只能显示5个订阅的资源。 

### <a name="how-do-i-make-changes-or-add-visualizations-to-network-insights"></a>如何实现进行更改或将可视化效果添加到网络见解？

若要进行更改，请选择 " **编辑模式** " 以修改工作簿。 然后，你可以将所做的更改另存为与指定的订阅和资源组相关联的新工作簿。

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>固定工作簿的任何部分后，时间粒度是多少？

网络见解使用 **自动** 时间粒度，因此，时间粒度基于所选时间范围。

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>工作簿的任何部分固定时的时间范围是多少？

时间范围取决于仪表板设置。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-network-insights"></a>如果想要查看其他数据或生成自己的可视化效果，应该怎么做？ 如何对网络见解进行更改？

您可以通过使用编辑模式来编辑在任何侧面板或详细度量值视图中看到的工作簿。 然后，可以将所做的更改另存为新的工作簿。

## <a name="next-steps"></a>后续步骤

- 详细了解网络监视： [什么是 Azure 网络观察程序？](../../network-watcher/network-watcher-monitoring-overview.md)
- 了解工作簿的设计支持、创建报表和自定义现有报表的方案以及更多内容： [通过 Azure Monitor 工作簿创建交互式报表](../platform/workbooks-overview.md)
