---
title: Azure Log Analytics 中的网络性能监视器解决方案 | Microsoft 文档
description: 使用网络性能监视器中的 ExpressRoute 管理器功能通过 Azure ExpressRoute 监视分支办事处与 Azure 之间的端到端连接和性能。
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: d97e380f83cc4e5e5a2d9686a7ac8426967f4b93
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302193"
---
# <a name="expressroute-manager"></a>ExpressRoute 管理器

可以使用[网络性能监视器](log-analytics-network-performance-monitor.md)中的 Azure ExpressRoute 管理器功能通过 Azure ExpressRoute 监视分支办事处与 Azure 之间的端到端连接和性能。 主要优势包括： 

- 自动检测与你的订阅关联的 ExpressRoute 线路。
- 跟踪 ExpressRoute 在线路、对等互连和 Azure 虚拟网络级别的带宽利用率、丢包情况和延迟。
- 发现 ExpressRoute 线路的网络拓扑。

![ExpressRoute 监视器](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>配置 
若要打开网络性能监视器的配置，请打开[网络性能监视器解决方案](log-analytics-network-performance-monitor.md)并选择“配置”。

### <a name="configure-network-security-group-rules"></a>配置网络安全组规则 
对于 Azure 中用于通过网络性能监视器进行监视的服务器，配置网络安全组 (NSG) 规则以允许将网络性能监视器在端口上使用的 TCP 流量用于综合事务。 默认端口为 8084。 此配置允许安装在 Azure VM 上的 Operations Management Suite 代理与本地监视代理进行通信。 

有关 NSG 的详细信息，请参阅 [网络安全组](../virtual-network/manage-network-security-group.md)。 

>[!NOTE]
> 在继续执行此步骤之前，请安装本地服务器代理和 Azure 服务器代理，然后运行 EnableRules.ps1 PowerShell 脚本。 

 
### <a name="discover-expressroute-peering-connections"></a>发现 ExpressRoute 对等连接 
 
1. 选择“ExpressRoute 对等互连”视图。
2. 选择“立即发现”，以便发现所有已连接到 Azure 订阅（通过此 Azure Log Analytics 工作区进行链接）中的虚拟网络的 ExpressRoute 专用对等互连。

    >[!NOTE]
    > 此解决方案目前只发现 ExpressRoute 专用对等互连。 

    >[!NOTE]
    > 只发现与此 Log Analytics 工作区链接的订阅关联的虚拟网络所连接的专用对等互连。 如果 ExpressRoute 所连接到的虚拟网络位于链接到此工作区的订阅之外，请在这些订阅中创建 Log Analytics 工作区。 然后，使用网络性能监视器监视这些对等互连。 

    ![ExpressRoute 监视器配置](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 完成发现操作以后，发现的专用对等互连就会列在表中。 针对这些对等互连的监视功能一开始处于禁用状态。 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>启用 ExpressRoute 对等连接监视 

1. 选择要监视的专用对等连接。
2. 在右侧窗格中，选中“监视此对等互连”复选框。 
3. 如果想要为此连接创建运行状况事件，请选中“为此对等互连启用运行状况监视”。 
4. 选择监视条件。 可以通过输入阈值，针对运行状况事件生成设置自定义阈值。 每当条件值超出其针对对等互连选择的阈值时，就会生成一个运行状况事件。 
5. 选择“添加代理”，以选择想要用来监视此对等连接的监视代理。 确保在连接的两端都添加代理。 连接到此对等互连的虚拟网络中至少需要有一个代理。 至少还需要有一个连接到此对等互连的本地代理。 
6. 选择“保存”以保存配置。 

   ![ExpressRoute 监视配置](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


在启用规则并选择值和代理后，等待 30 到 60 分钟来填充值并等待 **ExpressRoute 监视**磁贴显示。 看到监视磁贴后，网络性能监视器也将开始监视 ExpressRoute 线路和连接资源。 

>[!NOTE]
> 在已升级到新查询语言的工作区中，此功能能够可靠地运行。

## <a name="walkthrough"></a>演练 

网络性能监视器仪表板显示 ExpressRoute 线路和对等连接的运行状况概述。 

![网络性能监视器仪表板](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>线路列表 

若要查看所有受监视的 ExpressRoute 线路的列表，请选择“ExpressRoute 线路”磁贴。 可以选择一条线路并查看其运行状态以及数据包丢失、带宽使用率和延迟的趋势图表。 这些图表是交互式的。 可以选择自定义时间段来绘制图表。 在图表的某个区域上拖动鼠标来放大图表，详细查看数据点。 

![ExpressRoute 线路列表](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>丢包、延迟和吞吐量的趋势 

带宽利用率、延迟和丢包图表是交互式的。 可以使用鼠标控件放大这些图表的任何部分。 还可以看到其他时间间隔的带宽、延迟和丢包数据。 在左上角的“操作”按钮下，选择“日期/时间”。 **** 

![ExpressRoute 延迟](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>对等互连列表 

若要显示通过专用对等互连建立的到虚拟网络的所有连接的列表，请选择仪表板上的“专用对等互连”磁贴。 可以在此处选择一个虚拟网络连接，并查看其运行状态以及数据包丢失、带宽使用率和延迟的趋势图表。 

![ExpressRoute 对等互连](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>线路拓扑 

若要查看线路拓扑，请选择“拓扑”磁贴。 此操作将显示所选线路或对等互连的拓扑视图。 此拓扑图显示该网络上每个分段的延迟情况，并且每个第 3 层跃点由图表上的一个节点表示。 选择某个跃点可查看该跃点的详细信息。 若要增大可见级别以包含本地跃点，请移动“筛选器”下的滚动条。 向左或向右移动滚动条可增加或减少拓扑图中的跃点数。 还将显示每个分段的延迟情况，据此可以更快地隔离网络中的高延迟分段。 

![ExpressRoute 拓扑](media/log-analytics-network-performance-monitor/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>线路的详细拓扑图视图 

此视图显示虚拟网络连接。 

![ExpressRoute 虚拟网络连接](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>诊断 

可以借助网络性能监视器诊断多种线路连接问题。 下面列出了其中的一些问题。 

**线路已关闭。** 一旦本地资源与 Azure 虚拟网络之间的连接断开，网络性能监视器立即会发出通知。 此通知可以帮助你在收到用户事务升级之前采取主动措施，并减少停机时间。

![ExpressRoute 线路已关闭](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**流量不流过预期的线路。** 每当流量未流经预期的 ExpressRoute 线路时，网络性能监视器都会发出通知。 如果该线路已关闭，并且流量流经备用路由，则可能会出现此问题。 如果存在路由问题，则也可能会出现此问题。 此信息可帮助你主动管理路由策略中的任何配置问题，并确保使用最佳的安全路由。 

 

**流量不流过主线路。** 当流量流经辅助 ExpressRoute 线路时，网络性能监视器会发出通知。 尽管在此情况下可能不会遇到任何连接问题，但主动排查主线路的问题能够做好充分的准备。 

 
![ExpressRoute 流量流](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**高峰利用率导致性能下降。** 可将带宽利用率趋势与延迟趁势相关联，以查明 Azure 工作负荷性能下降的原因是否为带宽利用率出现峰值。 然后可以相应地采取措施。

![ExpressRoute 带宽利用率](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>后续步骤
[搜索日志](log-analytics-log-searches.md)以查看详细的网络性能数据记录。
