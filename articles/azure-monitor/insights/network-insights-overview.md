---
title: Azure 网络监视器（预览版）
description: 简要概述 Azure 网络监视器，它为已部署的所有网络资源提供运行状况和指标的全面视图，并且无需任何配置。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 5f076f477c36f96d1807ce7071720225a6df8e03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803800"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure 网络监视器（预览版）
网络 Azure Monitor 为所有部署的网络资源提供 [运行状况](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) 和 [指标](../platform/metrics-supported.md) 的综合视图，无需任何配置。  它还提供对所有网络监视功能的访问，例如 [连接监视器](../../network-watcher/connection-monitor-preview.md)、 [网络安全组的流日志记录 (nsg) ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)、 [流量分析](../../network-watcher/traffic-analytics.md)和其他网络 [诊断](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) 功能。

网络 Azure Monitor 的结构如下所示：
- [网络运行状况和指标](#networkhealth)
- [连接](#connectivity)
- [交通](#traffic)
- [诊断工具包](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>网络运行状况和指标

Azure 网络监视器的“概览”页面提供了一种轻松的方法来可视化网络资源的清单以及资源运行状况和警报。 它分为四个关键功能领域-搜索和筛选、资源运行状况和指标、警报。 and 依赖关系视图

![概述页](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>搜索和筛选
可以使用“订阅”、“资源组”和“资源类型”等筛选器来自定义资源运行状况和警报视图  。 搜索框提供搜索资源属性的功能。

搜索框可用于搜索资源和关联资源。 例如，公共 IP 与应用程序网关相关联。 搜索公共 IP DNS 名称将同时标识公共 IP 和关联的应用程序网关。

![网络 Azure Monitor-搜索](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>资源运行状况和指标
每个磁贴代表一种资源类型，其中显示了所选全部订阅中部署的实例数以及资源运行状况状态。 在下面的示例中，部署了 45 ER 和 VPN 连接，44处于正常状态，1个不可用。

![网络 Azure Monitor-资源运行状况](media/network-insights-overview/resource-health.png)

单击 "不可用的 ER 和 VPN 连接" 时，将启动一个指标视图。 

![网络 Azure Monitor-指标视图](media/network-insights-overview/metric-view.png)

你可以单击网格视图中的每个元素。 单击“运行状况”图标可重定向到该连接的资源运行状况。 单击“警报”可分别重定向到该连接的“警报和指标”页面。 

### <a name="alerts"></a>警报
右侧的“警报”网格提供所有订阅中选定资源生成的所有警报的视图。 单击警报计数可导航到详细的警报页面。

### <a name="dependency-view"></a>“依赖项”视图
“依赖项”视图有助于可视化资源的配置方式。 应用程序网关、虚拟 WAN 和负载均衡器目前支持“依赖项”视图。 例如，在使用应用程序网关时，可以通过单击指标网格视图中的应用程序网关资源名称来访问“依赖项”视图。 这也适用于虚拟 WAN 和负载均衡器。

![网络 Azure Monitor-应用程序网关视图](media/network-insights-overview/application-gateway.png)

应用程序网关的“依赖项”视图提供了前端 IP 如何连接到侦听器、规则和后端池的简化视图。 连接边缘采用颜色编码，并根据后端池运行状况提供其他详细信息。 该视图还提供了应用程序网关指标和所有相关后端池（如虚拟机规模集和 VM 实例）的指标的详细视图。

![网络 Azure Monitor-依赖关系视图](media/network-insights-overview/dependency-view.png)

通过依赖项图可轻松导航到配置设置。 右键单击后端池可以访问其他功能。 例如，如果后端池是 VM，则可以直接访问 VM Insights 和网络观察程序连接疑难解答，以确定连接问题。

![网络 Azure Monitor-依赖关系视图菜单](media/network-insights-overview/dependency-view-menu.png)

“依赖项”视图上的搜索和筛选栏提供了一种轻松的方法来搜索图形。 例如，在下面的示例中搜索 AppGWTestRule 会将图形视图缩小到通过 AppGWTestRule 连接的所有节点 。

![网络 Azure Monitor-搜索示例](media/network-insights-overview/search-example.png)

不同的筛选器有助于缩小到特定的路径和状态。 例如，从“运行状况状态”下拉列表中仅选择“运行不正常”可显示状态为“运行不正常”的所有边缘。

单击 " **详细指标视图** " 以启动预配置的工作簿，其中包含应用程序网关、所有后端池资源和前端 ip 的详细指标。 

## <a name="connectivity"></a><a name="connectivity"></a>连接性

使用 " **连接** " 选项卡，可以轻松地直观显示所选订阅集的使用连接监视器和 [连接监视器 (预览) ](../../network-watcher/connection-monitor-preview.md) 配置的所有测试。

![网络 Azure Monitor 中的 "连接" 选项卡](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

测试按源和目标磁贴分组，并显示每个测试的可访问性状态。 可访问性设置提供了一种简单的访问权限，可根据检查失败 (% ) 和 RTT (ms) 来配置可访问性标准。 设置值后，将根据选择条件更新每个测试的状态。

![网络 Azure Monitor 中的连接测试](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

单击任意源或目标磁贴将启动指标视图。

![网络 Azure Monitor 的连接指标](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


你可以单击网格视图中的每个元素。 单击 "可 **访问** 的图标" 可重定向到 " **连接监视器** 门户" 页，以查看跃点拓扑和已识别的连接影响问题的跃点。 单击 " **警报** "，重定向到 "警报"，然后 **检查 "失败百分比/往返时间** " 以重定向到所选连接监视器的 "指标" 页。

右侧的 " **警报**"   网格提供对所有订阅中配置的连接测试生成的所有警报的视图。 单击警报计数可导航到详细的警报页面。

## <a name="traffic"></a><a name="traffic"></a>交通
"流量" 选项卡可用于访问为所选订阅集配置[流量分析](../../network-watcher/traffic-analytics.md)[的所有 nsg，](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)并按位置分组。 此选项卡上提供的搜索功能可以识别为搜索的 IP 地址配置的 Nsg。 您可以在您的环境中搜索任何 IP 地址，平铺区域视图将显示所有 Nsg 以及 NSG 流日志和流量分析配置状态。

![网络 Azure Monitor 中的流量视图](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

单击任意区域磁贴将启动 "网格" 视图，通过该视图可轻松查看和配置 NSG 流日志和流量分析。  

![网络 Azure Monitor 中的流量区域视图](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

你可以单击网格视图中的每个元素。 单击 "配置状态" 以编辑 NSG flow 日志和流量分析配置。 单击 "警报" 以重定向到为所选 NSG 配置的流量警报。 同样，您可以通过单击工作区导航到 "流量分析" 视图。  

右侧的 " **警报**"   网格提供所有订阅中基于流量分析工作区的所有警报的视图。 单击警报计数可导航到详细的警报页面。

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 诊断工具包
诊断工具包提供对网络故障排除可用的所有诊断功能的访问权限。 从此下拉列表中，你可以访问 [数据包捕获](../../network-watcher/network-watcher-packet-capture-overview.md)、 [VPN 故障排除](../../network-watcher/network-watcher-troubleshoot-overview.md)、 [连接故障排除](../../network-watcher/network-watcher-connectivity-overview.md)、 [下一跃点](../../network-watcher/network-watcher-next-hop-overview.md) 和 [IP 流验证](../../network-watcher/network-watcher-ip-flow-verify-overview.md)等功能。

![诊断工具包选项卡](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>故障排除 

如需常规故障排除指南，请参阅专用的基于工作簿的见解[故障排除文章](troubleshoot-workbooks.md)。

本部分将帮助你诊断和解决在使用网络 Azure Monitor 时可能会遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="resolving-performance-issues-or-failures"></a>解决性能问题或故障

若要帮助解决你在网络 Azure Monitor 中标识的任何网络相关问题，请参阅故障排除资源的故障排除文档。 下面列出了用于高使用服务的故障排除链接。
* 虚拟网络 (VNET) 
* 应用程序网关
* VPN 网关
* ExpressRoute 
* 负载均衡器 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>为什么我看不到我选择的所有订阅中的资源

网络见解一次只能显示5个订阅中的资源。 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>我要对网络见解进行更改或添加其他可视化效果，如何实现此目的

要进行更改，请选择“编辑模式”修改工作簿，然后可以将工作保存为绑定到指定订阅和资源组的新工作簿。

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>固定工作簿的任何部分后，时间粒度是多少

我们使用“自动”时间粒度，因此，它取决于选择的时间范围。

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>固定工作簿的任何部分后，时间范围是多少

时间范围将取决于面板设置。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>如果想要查看其他数据或生成自己的可视化效果，应该怎么做？ 如何对网络见解进行更改

您可以通过使用编辑模式来编辑在任何侧面板和详细指标视图中看到的工作簿，然后将您的工作保存为新的工作簿，该工作簿将包含所有新的更改。


## <a name="next-steps"></a>后续步骤

- 有关网络监视的详细信息 [，请参阅 Azure 网络观察程序是什么？](../../network-watcher/network-watcher-monitoring-overview.md)。
- 查看[使用 Azure Monitor 工作簿创建交互式报表](../platform/workbooks-overview.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。
