---
title: "Azure 中的网络性能监视器解决方案 | Microsoft Docs"
description: "Azure 中的网络性能监视器可以近乎实时地监视网络性能，以检测并找到网络性能瓶颈。"
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
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Azure 中的网络性能监视器解决方案

![网络性能监视器符号](./media/log-analytics-network-performance-monitor/npm-symbol.png)


网络性能监视器 (NPM) 是一项基于云的混合网络监视解决方案，可以监视网络基础结构中不同点之间的网络性能、监视到服务/应用程序终结点的网络连接，以及监视 Azure ExpressRoute 的性能。  

NPM 可检测到流量黑洞、路由错误之类的网络问题，以及传统网络监视方法无法检测到的问题。 此解决方案可生成警报、在突破某个网络链接的阈值时进行通知、确保及时检测到网络性能问题，并可将问题的根源归结到特定网络段或设备。 

NPM 提供三种用途广泛的功能： 

[性能监视器](log-analytics-network-performance-monitor-performance-monitor.md)：跨云部署和本地位置、多个数据中心和分支机构、任务关健型多层应用程序/微服务监视网络连接。 使用性能监视器，可以在用户产生抱怨之前检测到网络问题。  

[服务终结点监视器](log-analytics-network-performance-monitor-service-endpoint.md)：可以监视从用户到所关注的服务的连接，确定路径中存在的具体基础结构，以及网络瓶颈的发生位置。 赶在用户之前了解中断情况，沿网络路径查看问题的具体位置。 

此功能可以用来执行基于 HTTP、HTTPS、TCP 和 ICMP 的测试，以近实时的方式或根据历史事实的方式监视服务的可用性和响应时间，以及网络在多大程度上造成了包丢失和延迟。 有了网络拓扑图，再结合使用每个跃点上的延迟数据，就可以确定从节点到服务这一段网络路径中出现的问题点，厘清网络速度下降的原因。 可以通过内置的测试在不使用任何预配置的情况下监视到 Office365 和 Dynamics CRM 的网络连接。 可以使用此功能监视到任何支持 TCP 的终结点（例如网站、SaaS 应用程序、PaaS 应用程序、SQL 数据库等）的网络连接。  

[ExpressRoute 监视器](log-analytics-network-performance-monitor-expressroute.md)：通过 Azure ExpressRoute 监视分支办事处与 Azure 之间的端到端连接和性能。  
 

## <a name="set-up-and-configure"></a>安装和配置

### <a name="install-and-configure-agents"></a>安装并配置代理 

使用[将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md) 和[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md) 中的基本过程安装代理。

### <a name="where-to-install-the-agents"></a>代理安装位置 

**性能监视器：**将 OMS 代理安装在至少一个已连接到每个子网的节点上。需要监视从每个子网到其他子网的网络连接。  

若要监视某个网络链接，必须在该链接的两个终结点上都安装代理。  如果不确定网络的拓扑，请在具有关键工作负荷且这些负荷之间的网络性能需要进行监视的服务器上安装代理。 例如，如果需要监视 Web 服务器和运行 SQL 的服务器之间的网络连接，请将代理安装在这两个服务器上。 代理会监视主机之间的网络连接（链接），而不是主机本身。 

**服务终结点监视器：**如果需要监视从节点到服务终结点的网络连接，则在每个这样的节点上安装 OMS 代理。 例如，如果打算监视从办公地点 O1、O2、O3 到 Office365 的网络连接，则请在 O1、O2、 O3 中的至少一个节点上安装 OMS 代理。 

**ExpressRoute 监视器：**在 Azure VNET 中安装至少一个 OMS 代理，并在通过 ExpressRoute 专用对等互连连接的本地子网中安装至少一个代理。  

### <a name="configure-oms-agents-for-monitoring"></a>配置 OMS 代理的监视功能  

NPM 使用综合事务来监视源代理和目标代理之间的网络性能。 在性能监视器和服务终结点监视器功能中，此解决方案允许在 TCP 和 ICMP 之间选择一项作为用于监视的协议，而 TCP 则用于 ExpressRoute 监视器。 请确保防火墙允许在 OMS 代理之间通信，这些代理用于对选择的可以进行监视的协议进行监视。  

**TCP 协议：**如果已选择 TCP 作为用于监视的协议，请在用于性能监视器和 ExpressRoute 监视器功能的代理上打开防火墙端口，确保代理能够互相连接。 为此，请使用管理员权限在 PowerShell 窗口中运行不带任何参数的 EnableRules.ps1 PowerShell 脚本。  

该脚本会创建解决方案所需的注册表项，还会创建用于允许代理创建相互之间的 TCP 连接的 Windows 防火墙规则。 该脚本创建的注册表项还指定是否记录调试日志和该日志文件的路径。 还会定义用于通信的代理 TCP 端口。 该脚本会自动设置这些注册表项的值，因此不应手动更改这些注册表项。 默认打开的端口为 8084。 通过向该脚本提供参数 portNumber 即可使用自定义端口。 不过，应在运行该脚本的所有计算机上使用相同端口。 

>[!NOTE]
> 此脚本仅在本地配置 Windows 防火墙。 如果有网络防火墙，应确保该防火墙允许流量去往 NPM 正在使用的 TCP 端口 

>[!NOTE]
> 不需为服务终结点监视器运行 EnableRules.ps1 PowerShell 脚本 

 

**ICMP 协议** - 如果已选择 ICMP 作为用于监视的协议，请启用以下防火墙规则，以便可靠地利用 ICMP： 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>配置解决方案 

1. 从 [Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)或使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将网络性能监视器解决方案添加到工作区。 
2. 打开 Log Analytics 工作区，然后打开“概览”磁贴。  
3. 单击标题为“网络性能监视器” 且消息为“解决方案需要进行额外配置”的磁贴 **。

    ![NPM 磁贴](media/log-analytics-network-performance-monitor/npm-config.png)

3. 在“安装”页的“常用设置”视图中，可以看到用于安装 OMS 代理并配置代理监视功能的选项。 如上所述，如果已安装并配置 OMS 代理，则请单击“安装”视图，以便配置想要使用的功能。  

    **“性能监视器”视图** - 在“默认的性能监视器规则”中选择应该用于综合事务的具体协议，然后单击“保存并继续”。 此协议选择仅适用于系统生成的默认规则。每次显式创建性能监视器规则时，都需要选择该协议。 始终可以转到“性能监视器”选项卡中的“默认规则设置”（这在完成第 0 天的配置后显示），在以后更改协议。 如果对性能监视器功能不感兴趣，则可在“性能监视器”选项卡的“默认规则设置”中禁用默认规则。 

    ![NPM 配置](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **“服务终结点监视器”视图** - 此功能提供内置的预配置测试，用于监视从代理到 Office365 和 Dynamcis365 的网络连接。 选择要监视的 Office365 和 Dynamcis365 服务，只需勾选其旁边的复选框即可。 选择要从其进行监视的代理，方法是单击“添加代理”按钮。 如果不希望使用此功能，或者想要在以后进行设置，则可选择跳过此项，直接单击“保存并继续”，不需选择任何内容。  

    ![NPM 配置](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **“ExpressRoute 监视器”视图** - 单击“立即发现”按钮，以便发现所有已连接到 Azure 订阅（通过此 Log Analytics 工作区进行链接）中的 VNET 的 ExpressRoute 专用对等互连。  


    >[!NOTE] 
    > 此解决方案目前只发现 ExpressRoute 专用对等互连。 

    >[!NOTE] 
    > 只发现特定的专用对等互连，这些对等互连连接到与订阅关联的 VNET，而订阅则通过此 Log Analytics 工作区进行链接。 如果 ExpressRoute 所连接到的 VNET 位于链接到此工作区的订阅之外，则需在这些订阅中创建 Log Analytics 工作区，然后使用 NPM 来监视这些对等互连。 

    ![NPM 配置](media/log-analytics-network-performance-monitor/npm-express-route.png)

    完成发现操作以后，发现的专用对等互连就会列在表中。  

    ![NPM 配置](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    针对这些对等互连的监视功能一开始处于禁用状态。 单击要监视的每个对等互连，然后在右侧 (RHS) 详细信息视图中为其配置监视功能。  单击“保存”按钮，保存配置。 若要了解详细信息，请参阅[配置 ExpressRoute 监视]()。  

    安装完成以后，数据的填充需要 30 分钟到 1 小时。 当解决方案正从网络聚合数据时，NPM 概览磁贴中会显示“解决方案需要进行额外配置”消息。 收集数据并为其编制索引以后，概览磁贴会变化，并会告知网络运行状况的摘要。 然后即可选择编辑对节点（其上安装了 OMS 代理）和子网（从环境中发现）的监视 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>编辑子网和节点的监视设置 

配置页的“子网”选项卡中会列出至少安装了一个代理的所有子网。 


启用或禁用对特定子网的监视 

1. 选中或清除“子网 ID” **** 旁边的框，并确保已选中或清除“用于监视” **** （视情况而定）。 可以选择或清除多个子网。 禁用时，不会监视子网，因为会更新代理以停止 ping 其他代理。 
2. 通过以下方式选择想要监视的特定子网中的节点：从列表中选择子网，并在包含未监视节点的列表与包含已监视节点的列表之间移动所需节点。 可以根据需要向子网添加“自定义说明”。 
3. 单击“保存”以保存配置。 

#### <a name="choose-nodes-to-monitor"></a>选择要监视的节点

所有已安装代理的节点会列在“节点”选项卡上。 

1. 选择要监视的节点或清除要停止监视的节点。 
2. 单击“用于监视” ****，或清除它（视情况而定）。 
3. 单击“ **保存**”。 


配置感兴趣的功能： 
- 配置[性能监视器](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- 配置[服务终结点监视器](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- 配置 [ExpressRoute 监视器](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>数据收集详细信息
选择 TCP 作为协议来收集丢失与延迟信息时，网络性能监视器使用 TCP SYN-SYNACK-ACK 握手数据包；如果选择 ICMP 作为协议来收集此类信息，则它会使用 ICMP ECHO ICMP ECHO REPLY。 此外，还使用跟踪路由来获取拓扑信息。

下表显示了数据收集方法，以及有关如何为网络性能监视器收集数据的其他详细信息。

| 平台 | 直接代理 | SCOM 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |每隔 5 秒发送 TCP 握手/ICMP ECHO 消息，每隔 3 分钟发送数据 |
 

 
解决方案使用综合事务来评估网络的运行状况。 网络中各个点安装的 OMS 代理会相互交换 TCP 数据包或 ICMP Echo（取决于选择用于监视的协议）。 在此过程中，代理将了解往返时间和丢包情况（如果有）。 每个代理还会定期对其他代理执行跟踪路由，以全部找出网络中必须测试的各种路由。 使用此数据，代理就可以推断出网络延迟和丢包数字。 代理每隔 5 秒钟重复执行测试，聚合数据 3 分钟，然后将数据上传到 Log Analytics 服务。 



>[!NOTE]
> 尽管代理相互频繁地通信，但它们在进行测试时并不会产生大量网络流量。 代理仅仅依靠 TCP SYN-SYNACK-ACK 握手数据包确定丢失和延迟 - 不会交换任何数据包。 在此过程中，代理只在需要时才会进行相互通信，并且会对代理通信拓扑进行优化以减少网络流量。

## <a name="using-the-solution"></a>使用解决方案 

### <a name="npm-overview-tile"></a>NPM 概览磁贴 

在启用了网络性能监视器解决方案后，“概览”页中的解决方案磁贴会提供网络运行状况的快速概览。 

 ![NPM 概览磁贴](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>网络性能监视器仪表板 

“排名靠前的网络运行状况事件”页提供系统中最新运行状况事件和警报的列表以及事件发生后所经过的时间。 每当监视规则的所选指标（丢失、延迟、响应时间或带宽利用率）的值超出阈值时，就会生成运行状况事件或警报。 

“性能监视器”页汇总了解决方案所监视的“网络”链接和“子网”链接的运行状况。 **** “拓扑”磁贴告知在网络中受监视的网络路径数。 单击此磁贴会直接导航到“拓扑”视图。 

“服务终结点监视器”页汇总了已创建的不同测试的运行状况。 **** “拓扑”磁贴告知受监视的终结点数。 单击此磁贴会直接导航到“拓扑”视图。

“ExpressRoute 监视器”页 ****汇总了解决方案监视的各种 ExpressRoute 对等互连连接的运行状况。 “拓扑”磁贴告知在网络中受监视的通过 ExpressRoute 线路的网络路径数。 单击此磁贴会直接导航到“拓扑”视图。

“常见查询” ****页中包含一组用于直接提取网络监视原始数据的搜索查询。 可以基于这些查询创建用于生成自定义报表的查询。 

![NPM 仪表板](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>深入了解 

可以单击解决方案仪表板中的各个链接来进一步了解任何感兴趣部分。 例如，当看到警报或不正常的网络链接出现在仪表板上时，可以单击它以进一步进行调查。 会你将转到列出该特定网络链接的所有子网链接的页面。 将能够查看每个子网链接的丢失、延迟和运行状况状态，快速找出哪个子网链接导致了该问题。 然后可以单击“查看节点链接” **** ，查看不正常的子网链接的所有节点链接。 然后，可以查看个别节点到节点链接，找到不正常的节点链接。 

可以单击“查看拓扑” **** ，查看源节点和目标节点之间路由的逐跳拓扑。 不正常的路由会显示为红色，这样就可以查看每个跃点导致的延迟，从而快速确定网络特定部分的问题。 

 

### <a name="network-state-recorder"></a>网络状态记录器 

每个视图显示特定时间点的网络运行状况快照。 默认会显示最新的状态。 页面顶部栏显示该状态所处的时间点。 可以选择后退到某个时间，并在栏中单击“操作”，查看网络运行状况的快照。 查看最新状态时，还可以选择启用或禁用任何页面的自动刷新。 

 ![网络状态记录器](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>趋势图 

在向下钻取的每个级别，都可以看到适用指标（丢失、延迟、响应时间、带宽利用率）的趋势。 可以通过使用图表顶部的时间控制，更改趋势的时间间隔。 

趋势图从历史角度呈现了性能指标的性能。 某些网络问题本质上是瞬时发生的，仅通过查看该网络的当前状态难以捕获。 这是因为问题可能会快速浮现并在有人注意到之前就消失，只在之后的某个时间点才会再次出现。 此类瞬时问题对于应用程序管理员而言也比较棘手，因为这些问题常常表现为应用程序响应时间原因不明的增加，即使所有应用程序组件看起来都平稳地运行时也会如此。 

可以通过查看趋势图（其中，问题会表现为网络延迟或数据包丢失突增）轻松地检测到这些类型的问题。 然后就可以对问题进行调查，方法是：使用网络状态记录器查看问题发生时的时间点的网络快照和拓扑。 

 
![趋势图](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>拓扑图 

NPM 会显示交互式拓扑图中源节点和目标节点之间路由的逐跳拓扑。 可以查看拓扑图，方法是单击解决方案仪表板中的“拓扑”磁贴，或者单击向下钻取页上的“查看拓扑”链接。  

拓扑图会显示源和目标之间存在多少个路由，以及数据包会采用哪条路径。 由每个网络跃点导致的延迟也是可见的。 所有总路径延迟高出阈值（在相应的监视规则中设置）的路径都显示为红色。  

当在拓扑图上单击某个节点或将光标悬停在其上方时，会看到诸如 FQDN 和 IP 地址之类的节点属性。 单击跃点查看其 IP 地址。 可以通过观察网络跃点导致的延迟来确定有问题的网络跃点。 可以使用可折叠的操作窗格中的筛选器来选择筛选特定的路由。 还可以通过使用操作窗格中的滑块隐藏中间跃点来简化网络拓扑。 可以使用鼠标滚轮来放大或缩小拓扑图。 

请注意，图中所示的拓扑为第 3 层拓扑，不包含第 2 层的设备和连接。 

 
![拓扑图](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Log Analytics 搜索 

通过 NPM 仪表板和向下钻取页面以图形方式显示的所有数据也可以在 [Log Analytics 搜索](log-analytics-log-search-new.md)中以本地方式使用。 可以对存储库中的数据进行交互式分析、将不同源中的数据进行关联、创建自定义警报、创建自定义视图，以及将数据导出到 Excel、PowerBI 或可共享的链接。 仪表板的“常见查询”区域中有一些查询非常有用，可以基于这些查询创建自己的查询和报表。 

 

## <a name="provide-feedback"></a>提供反馈 

**UserVoice** - 可以发表有关希望我们开发的网络性能监视器功能的想法。 请访问我们的  [UserVoice 页](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring)。 

**加入我们的队伍** - 我们总是希望一直有新客户不断加入我们的队伍。 那样，能够在早期接触到新功能并帮助我们改进网络性能监视器。 如果有兴趣加入，请填写此 [快速调查](https://aka.ms/npmcohort)。 

## <a name="next-steps"></a>后续步骤 
- 详细了解[性能监视器](log-analytics-network-performance-monitor-performance-monitor.md)、[服务终结点监视器](log-analytics-network-performance-monitor-performance-monitor.md)和 [ExpressRoute 监视器](log-analytics-network-performance-monitor-expressroute.md)。 
