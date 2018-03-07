---
title: "Azure Log Analytics 中的网络性能监视器解决方案 | Microsoft 文档"
description: "使用网络性能监视器中的 ExpressRoute 管理器功能可以通过 Azure ExpressRoute 监视分支办事处与 Azure 之间的端到端连接和性能。"
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute 管理器

使用[网络性能监视器](log-analytics-network-performance-monitor.md)中的 ExpressRoute 管理器功能可以通过 Azure ExpressRoute 监视分支办事处与 Azure 之间的端到端连接和性能。 主要优势包括： 

- 自动检测与你的订阅关联的 ExpressRoute 线路 
- 跟踪 ExpressRoute 在线路、对等互连和 VNet 级别的带宽利用率、丢包情况和延迟 
- 发现 ExpressRoute 线路的网络拓扑 

![ExpressRoute 监视器](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>配置 
若要打开网络性能监视器的配置，请打开[网络性能监视器解决方案](log-analytics-network-performance-monitor.md)并单击“配置”按钮。

### <a name="configure-nsg-rules"></a>配置 NSG 规则 
对于 Azure 中用于通过 NPM 进行监视的服务器，必须配置网络安全组 (NSG) 规则，允许将 NPM 在端口上使用的 TCP 流量用于综合事务。 默认端口为 8084。 这样，Azure VM 上安装的 OMS 代理便可与本地监视代理通信。 

有关 NSG 的详细信息，请参阅 [网络安全组](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。 

>[!NOTE]
> 确保已安装代理（包括本地服务器代理和 Azure 服务器代理），并且在执行此步骤前已运行 EnableRules.ps1 PowerShell 脚本。 

 
### <a name="discover-expressroute-peering-connections"></a>发现 ExpressRoute 对等连接 
 
1. 单击“ExpressRoute 对等互连”视图。  
2. 单击“立即发现”按钮，以便发现所有已连接到 Azure 订阅（通过此 Log Analytics 工作区进行链接）中的 VNET 的 ExpressRoute 专用对等互连。  

>[!NOTE]  
> 此解决方案目前只发现 ExpressRoute 专用对等互连。 

>[!NOTE]  
> 只发现特定的专用对等互连，这些对等互连连接到与订阅关联的 VNET，而订阅则通过此 Log Analytics 工作区进行链接。 如果 ExpressRoute 所连接到的 VNET 位于链接到此工作区的订阅之外，则需在这些订阅中创建 Log Analytics 工作区，然后使用 NPM 来监视这些对等互连。 

 ![配置 ExpressRoute 监视器](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 完成发现操作以后，发现的专用对等互连就会列在表中。 针对这些对等互连的监视功能一开始处于禁用状态。 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>启用 ExpressRoute 对等连接监视 

1. 单击想要监视的专用对等互连。  
2. 在 RHS 窗格中，单击“监视此对等互连”对应的复选框。 
3. 如果想要为此连接创建运行状况事件，请选中“为此对等互连启用运行状况监视”。 
4. 选择监视条件。 可以通过键入阈值，针对运行状况事件生成设置自定义阈值。 只要条件值超出其针对对等互连选择的阈值，就会生成运行状况事件。 
5. 单击“添加代理”，选择想要用来监视此对等连接的监视代理。 需确保在连接的两端添加代码：与此对等互连连接的 Azure VNET 中至少有一个代理，并且至少有一个本地代理与此对等互连连接。 
6. 单击“保存”以保存配置。 

![配置 ExpressRoute 监视器](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


启用规则并选择要监视的的值和代理后，需要等待约 30-60 分钟后值才会开始填充，“ExpressRoute 监视”磁贴也将变为可用。 看到监视磁铁后，NPM 也将开始监视 ExpressRoute 线路和连接资源。 

>[!NOTE]
> 在已升级到新查询语言的工作区中，此功能能够可靠地运行。  

## <a name="walkthrough"></a>演练 

网络性能监视仪表板显示 ExpressRoute 线路和对等连接的运行状况概述。 

![NPM 仪表板 ExpressRoute](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>线路列表 

若要查看所有受监视的 ExpressRoute 线路的列表，请单击“ExpressRoute 线路”磁贴。 可以选择一条线路并查看其运行状态以及数据包丢失、带宽使用率和延迟的趋势图表。 这些图表是交互式的。 可以选择自定义时间段来绘制图表。 可以在图表上方区域拖动鼠标来放大图表，详细查看数据点。 

![ExpressRoute 线路列表](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>丢包、延迟和吞吐量的趋势 

带宽、延迟和丢失图表是交互式的。 可以使用鼠标控件放大这些图表的任何部分。 单击左上角“操作”按钮下的“日期/时间”，还可以查看其它时间间隔内的带宽、延迟和数据丢失趋势。 

![ExpressRoute 延迟](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>对等互连列表 

单击仪表板上的“专用对等互连”磁贴，会显示通过专用对等互连到虚拟网络的所有连接列表。 可以在此处选择一个虚拟网络连接，并查看其运行状态以及数据包丢失、带宽使用率和延迟的趋势图表。 

![ExpressRoute 对等互连](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>线路拓扑 

若要查看线路拓扑，请单击“拓扑”磁贴。 你将转到所选线路或对等互连的拓扑视图。 此拓扑图显示该网络上每个分段的延迟情况，并且每个第 3 层跃点由图表上的一个节点表示。 单击跃点即可查看该跃点的详细信息。 通过移动“筛选器”下的滚动条，可以增加可见级别以包含本地跃点。 向左/右移动滚动条即可增加/减少拓扑图中的跃点数。 还将显示每个分段的延迟情况，据此可以更快地隔离网络中的高延迟分段。 

![ExpressRoute 拓扑](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>线路的详细拓扑图视图 

此视图显示 VNet 连接。 

![ExpressRoute VNet](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>诊断 

可以借助网络性能监视器诊断多种线路连接问题。 下面列出了其中的一些问题 

**线路已关闭。** 一旦本地资源与 Azure VNET 之间的连接断开，NPM 立即会发出通知。 这可以帮助你在收到用户事务升级之前采取主动措施，并减少停机时间 

![ExpressRoute 线路已关闭](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**流量不流过预期的线路。** 每当流量意外地未流经预期的 ExpressRoute 线路时，NPM 会发出通知。 如果该线路已关闭，并且流量流过备用路由，或者存在路由问题，则可能会出现此问题。 此信息可帮助你主动管理路由策略中的任何配置问题，并确保使用最佳的安全路由。 

 

**流量不流过主线路。** 当流量流经辅助 ExpressRoute 线路时，此功能会发出通知。 尽管在此情况下可能不会遇到任何连接问题，但主动排查主线路的问题能够做好充分的准备。 

 
![ExpressRoute 流量流](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**高峰利用率导致性能下降。** 可将带宽利用率趋势与延迟趁势相关联，识别 Azure 工作负荷性能下降的原因是否为带宽利用率出现峰值，并采取相应的措施。  

![ExpressRoute 监视器](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看详细的网络性能数据记录。
