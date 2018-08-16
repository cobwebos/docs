---
title: Azure 中的网络性能监视器解决方案 | Microsoft Docs
description: Azure 中的网络性能监视器可以近乎实时地监视网络性能，以检测并找到网络性能瓶颈。
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
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: na
ms.openlocfilehash: 3640f5bb2c2e9457e269bd189cbec3b627ee7349
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626866"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Azure 中的网络性能监视器解决方案

![网络性能监视器符号](./media/log-analytics-network-performance-monitor/npm-symbol.png)


网络性能监视器是一项基于云的混合网络监视解决方案，可以监视网络基础结构中不同点之间的网络性能。 它还可以监视到服务和应用程序终结点的网络连接，以及 Azure ExpressRoute 的性能。 

网络性能监视器可检测诸如流量黑洞、路由错误之类的网络问题，以及传统网络监视方法无法检测到的问题。 只要突破网络链接的阈值，解决方案就会生成警报并进行通知。 它还可以确保及时检测到网络性能问题，然后确定问题根源所在的特定网络段或设备。 

网络性能监视器提供以下三种用途广泛的功能： 

* [性能监视器](log-analytics-network-performance-monitor-performance-monitor.md)：可以跨云部署和本地位置、多个数据中心和分支机构、任务关健型多层应用程序或微服务监视网络连接。 使用性能监视器，可以在用户产生抱怨之前检测到网络问题。

* [服务连接性监视器](log-analytics-network-performance-monitor-service-endpoint.md)：可监视从用户到所关注的服务的连接，确定路径中存在的具体基础结构，并识别网络瓶颈的发生位置。 可以赶在用户之前了解中断情况，沿网络路径查看问题的具体位置。 

    此功能可以用来执行基于 HTTP、HTTPS、TCP 和 ICMP 的测试，以近实时的方式或根据历史事实的方式监视服务的可用性和响应时间。 还可以监视网络在多大程度上造成了包丢失和延迟。 使用网络拓扑图可以隔离导致网络速度下降的问题点。 凭借每个跃点上的延迟数据，可以确定从节点到服务这一段网络路径中出现的问题点。 可以通过内置的测试在不使用任何预配置的情况下监视 Office 365 和 Dynamics CRM 的网络连接。 可以使用此功能监视到任何支持 TCP 的终结点（例如网站、SaaS 应用程序、PaaS 应用程序和 SQL 数据库）的网络连接。

* [ExpressRoute 监视器](log-analytics-network-performance-monitor-expressroute.md)：通过 Azure ExpressRoute 监视分支办事处与 Azure 之间的端到端连接和性能。  

可在线查看相关信息，深入了解[网络性能监视器](https://docs.microsoft.com/azure/networking/network-monitoring-overview)支持的各种功能。
 
## <a name="supported-regions"></a>支持的区域
NPM 可从以下任一区域托管的工作区监视世界上任何地方网络和应用程序之间的连接：
* 西欧
* 美国中西部
* 美国东部
* 日本东部
* 东南亚
* 澳大利亚东南部
* 英国南部
* 美国弗吉尼亚州政府

可在[文档](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions)中查看支持 ExpressRoute 监视器的区域列表。


## <a name="set-up-and-configure"></a>安装和配置

### <a name="install-and-configure-agents"></a>安装并配置代理 

使用[将 Windows 计算机连接到 Azure Log Analytics](log-analytics-windows-agents.md) 和[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md) 中的基本过程安装代理。

### <a name="where-to-install-the-agents"></a>代理安装位置 

* **性能监视器**：如果需要监视每个子网到其他子网的网络连接，请将 Operations Management Suite 代理安装在至少一个已连接到每个子网的节点上。

    要监视某个网络链接，请在该链接的两个终结点上安装代理。 如果不确定网络的拓扑，请在具有关键工作负荷且这些负荷之间的网络性能需要进行监视的服务器上安装代理。 例如，如果需要监视 Web 服务器和运行 SQL 的服务器之间的网络连接，请将代理安装在这两个服务器上。 代理会监视主机之间的网络连接（链接），而不是主机本身。 

* **服务终结点监视器**：要监视节点到服务终结点的网络连接，需在每个此类节点上安装 Operations Management Suite 代理。 例如，假设要监视从标记为 O1、O2 和 O3 的办公网站到 Office 365 的网络连接。 分别在 O1、O2 和 O3 中的至少一个节点上安装 Operations Management Suite 代理。 

* **ExpressRoute 监视器**：在 Azure 虚拟网络中安装至少一个 Operations Management Suite 代理。 并在通过 ExpressRoute 专用对等互连连接的本地子网中安装至少一个代理。  

### <a name="configure-operations-management-suite-agents-for-monitoring"></a>配置 Operations Management Suite 代理的监视功能 

网络性能监视器使用综合事务来监视源代理和目标代理之间的网络性能。 在性能监视器和服务连接性监视器功能中，可选择 TCP 或 ICMP 作为监视协议。 只有 TCP 可用作 ExpressRoute 监视器的监视协议。 请确保防火墙允许在 Operations Management Suite 代理之间通信，这些代理用于监视选定的协议。 

* **TCP 协议**：如果选择 TCP 作为用于监视的协议，请在用于网络性能监视器和 ExpressRoute 监视器的代理上打开防火墙端口，确保代理能够互相连接。 若要打开端口，请使用管理员权限在 PowerShell 窗口中运行不带任何参数的 [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell 脚本。

    该脚本可创建解决方案所需的注册表项。 它还会创建 Windows 防火墙规则，允许代理创建彼此之间的 TCP 连接。 该脚本创建的注册表项指定是否记录调试日志和该日志文件的路径。 该脚本还会定义用于通信的代理 TCP 端口。 该脚本会自动设置这些注册表项的值。 请勿手动更改这些注册表项。 默认打开的端口为 8084。 通过向该脚本提供参数 portNumber 即可使用自定义端口。 在运行该脚本的所有计算机上使用相同端口。 

    >[!NOTE]
    > 此脚本仅在本地配置 Windows 防火墙。 如果有网络防火墙，请确保该防火墙允许流量去往网络性能监视器使用的 TCP 端口。

    >[!NOTE]
    > 不需为服务终结点监视器运行 [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell 脚本。

    

* **ICMP 协议**：如果选择 ICMP 作为用于监视的协议，请启用以下防火墙规则，以便可靠地利用 ICMP：
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>配置解决方案 

1. 将网络性能监视器解决方案从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)添加到工作区。 也可以使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程。 
2. 打开 Log Analytics 工作区，然后选择“概览”磁贴。 
3. 选择消息为“解决方案需要进行额外配置”的“网络性能监视器”磁贴  **。

   ![网络性能监视器磁贴](media/log-analytics-network-performance-monitor/npm-config.png)

4. 在“安装”页的“常用设置”视图中，可以看到用于安装 Operations Management Suite 代理并配置代理监视功能的选项。 如上所述，如果安装并配置了 Operations Management Suite 代理，请选择“安装”视图来配置要使用的功能。 

   **性能监视器**：在“默认”性能监视器规则中选择用于综合事务的协议，然后选择“保存并继续”。 此协议选择仅适用于系统生成的默认规则。 每次显式创建性能监视器规则时，均需要选择协议。 始终可以转到“性能监视器”选项卡中的“默认”规则设置（这在完成第 0 天的配置后显示），在以后更改协议。 如果不需要性能监视器功能，则可在“性能监视器”选项卡的“默认”规则设置中禁用默认规则。

   ![“性能监视器”视图](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **服务连接性监视器**：此功能提供内置的预配置测试，用于监视代理到 Office 365 和 Dynamics 365 的网络连接。 通过选中 Office 365 和 Dynamics 365 服务旁边的复选框，选择需要监视的服务。 要选择要从其进行监视的代理，请选择“添加代理”。 如果不希望使用此功能，或希望在以后进行设置，请不要做出任何选择，而是选择“保存并继续”。

   ![“服务终结点监视器”视图](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute 监视器**：选择“立即发现”，以便发现所有已连接到 Azure 订阅（通过此 Log Analytics 工作区进行链接）中的虚拟网络的 ExpressRoute 专用对等互连。 

   >[!NOTE] 
   > 此解决方案目前只发现 ExpressRoute 专用对等互连。 

   >[!NOTE] 
   > 只发现特定的专用对等互连，这些对等互连连接到与订阅关联的虚拟网络，而订阅则通过此 Log Analytics 工作区进行链接。 如果 ExpressRoute 所连接到的虚拟网络位于链接到此工作区的订阅之外，请在这些订阅中创建 Log Analytics 工作区。 使用网络性能监视器监视这些对等互连。

   ![“ExpressRoute 监视器”视图](media/log-analytics-network-performance-monitor/npm-express-route.png)

   完成发现操作以后，发现的专用对等互连就会列在表中。 

   ![“网络性能监视器配置”页](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
针对这些对等互连的监视功能一开始处于禁用状态。 选择要监视的每个对等互连，然后从右侧的详细信息视图中配置相应的监视功能。 选择“保存”以保存配置。 要了解详细信息，请参阅“配置 ExpressRoute 监视”一文。 

安装完成以后，数据的填充需要 30 分钟到 1 小时。 当解决方案正从网络聚合数据时，网络性能监视器“概览”磁贴中会显示“解决方案需要进行额外配置”消息。 收集数据并为其编制索引以后，“概览”磁贴会变化，并以摘要形式告知网络运行状况。 然后可以编辑对安装了 Operations Management Suite 代理的节点和从环境中发现的子网的监视设置。

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>编辑子网和节点的监视设置 

配置页的 **“子网”** 选项卡中会列出至少安装了一个代理的所有子网。 


启用或禁用对特定子网的监视：

1. 选中或清除“子网 ID”旁的复选框 ****。 然后，请确保选中或清除 **“用于监视”** （视情况而定）。 可以选择或清除多个子网。 禁用时，不会监视子网，但会更新代理以停止 ping 其他代理。 
2. 在特定子网中选择要监视的节点。 从列表中选择子网，并在包含未监视节点的列表与包含已监视节点的列表之间移动所需节点。 可以向子网添加自定义说明。
3. 选择“保存”以保存配置。 

#### <a name="choose-nodes-to-monitor"></a>选择要监视的节点

所有已安装代理的节点都会列在“节点”选项卡上。 

1. 选择要监视的节点或清除要停止监视的节点。 
2. 选中“用于监视”，或清除它（视情况而定）。 
3. 选择“保存”。 


配置所需功能：

- [性能监视器](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [服务终结点监视器](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute 监视器](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>数据收集详细信息
收集丢失与延迟信息时，如果选择 TCP 作为协议，网络性能监视器使用 TCP SYN-SYNACK-ACK 握手数据包。 如果选择 ICMP 作为协议，网络性能监视器使用 ICMP ECHO ICMP ECHO REPLY。 此外，还使用跟踪路由来获取拓扑信息。

下表显示了数据收集方法，以及有关如何为网络性能监视器收集数据的其他详细信息。

| 平台 | 直接代理 | System Center Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |每隔 5 秒发送 TCP 握手/ICMP ECHO 消息，每隔 3 分钟发送数据 |
 

 
解决方案使用综合事务来评估网络的运行状况。 网络中各个点安装的 Operations Management Suite 代理会相互交换 TCP 数据包或 ICMP Echo。 代理是使用 TCP 数据包还是 ICMP Echo 取决于选择用于监视的协议。 在此过程中，代理将了解往返时间和丢包情况（如果有）。 每个代理还会定期对其他代理执行跟踪路由，以全部找出网络中必须测试的各种路由。 使用此数据，代理就可以推断出网络延迟和丢包数字。 每隔五秒重复执行测试。 代理聚合数据三分钟，然后数据将上传到 Log Analytics 服务。



>[!NOTE]
> 尽管代理相互频繁地通信，但它们在进行测试时并不会产生大量网络流量。 代理仅仅依靠 TCP SYN-SYNACK-ACK 握手数据包确定丢失和延迟。 不会交换任何数据包。 在此过程中，代理只在需要时才会进行相互通信。 代理通信拓扑经过优化，可减少网络流量。

## <a name="use-the-solution"></a>使用解决方案 

### <a name="network-performance-monitor-overview-tile"></a>网络性能监视器“概览”磁贴 

在启用了网络性能监视器解决方案后，“概览”页中的解决方案磁贴会提供网络运行状况的快速概览。 

 ![网络性能监视器“概览”磁贴](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>网络性能监视器仪表板 

* **排名靠前的网络运行状况事件**：本页提供系统中最新运行状况事件和警报的列表以及事件发生后所经过的时间。 每当监视规则的所选指标（丢失、延迟、响应时间或带宽利用率）的值超出阈值时，就会生成运行状况事件或警报。 

* **ExpressRoute 监视器**：本页提供解决方案监视的各个 ExpressRoute 对等互连连接的运行状况摘要。 “拓扑”磁贴显示在网络中受监视的通过 ExpressRoute 线路的网络路径数。 选择此磁贴转到“拓扑”视图。

* **服务连接性监视器**：本页提供所创建的不同测试的运行状况摘要。 “拓扑”磁贴显示受监视的终结点数。 选择此磁贴转到“拓扑”视图。

* **性能监视器**：本页提供解决方案监视的“网络”链接和“子网”链接的运行状况摘要。 “拓扑”磁贴显示在网络中受监视的网络路径数。 选择此磁贴转到“拓扑”视图。 

* **常见查询**：本页包含一组用于直接提取网络监视原始数据的搜索查询。 可以基于这些查询创建用于生成自定义报表的查询。 

   ![网络性能监视器仪表板](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>深入了解 

可以选择解决方案仪表板中的各个链接来进一步了解任何感兴趣部分。 例如，看到警报或不正常的网络链接出现在仪表板上时，可选择它进一步进行调查。 会转到列出该特定网络链接的所有子网链接的页面。 可以看到每个子网链接的丢失、延迟和运行状况状态。 可以快速找到引发问题的子网链接。 选择“查看节点链接” ，查看不正常的子网链接的所有节点链接。 然后，可以查看个别节点到节点链接，找到不正常的节点链接。 

选择 **“查看拓扑”** ，查看源节点和目标节点之间路由的逐跳拓扑。 不正常的路由显示为红色。 可以查看每个跃点导致的延迟，从而快速确定网络特定部分的问题。

 

### <a name="network-state-recorder-control"></a>网络状态记录器控件

每个视图显示特定时间点的网络运行状况快照。 默认会显示最新的状态。 页面顶部栏显示该状态所处的时间点。 要查看先前某时间的网络运行状况快照，请选择“操作”。 查看最新状态时，还可以启用或禁用任何页面的自动刷新。 

 ![网络状态记录器](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>趋势图 

在向下钻取的每个级别，都可以看到适用指标的趋势。 这些指标包括丢失、延迟、响应时间或带宽利用率。 要更改趋势的时间间隔，请使用图表顶部的时间控制。 

趋势图从历史角度呈现了性能指标的性能。 某些网络问题本质上是瞬时发生的，仅通过查看该网络的当前状态难以捕获。 问题可能会快速浮现并在有人注意到之前就消失，只在之后的某个时间点才会再次出现。 此类瞬时问题对于应用程序管理员而言也比较棘手。 这些问题常常表现为应用程序响应时间原因不明的增加，即使所有应用程序组件看起来都平稳地运行时也会如此。 

通过查看趋势图，可以轻松检测到此类问题。 该问题表现为网络延迟或数据包丢失突增。 要调查此问题，请使用网络状态记录器控件查看问题发生时的时间点的网络快照和拓扑。

 
![趋势图](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>拓扑图 

网络性能监视器显示交互式拓扑图中源节点和目标节点之间路由的逐跳拓扑。 要查看拓扑图，请选择解决方案仪表板上的“拓扑”磁贴。 还可以选择向下钻取页面上的“查看拓扑”链接。 

拓扑图会显示源和目标之间存在多少个路由，以及数据包会采用哪条路径。 由每个网络跃点导致的延迟也是可见的。 所有总路径延迟高出阈值（在相应的监视规则中设置）的路径都显示为红色。 

在拓扑图上选择某个节点或将光标悬停在其上方时，会看到诸如 FQDN 和 IP 地址之类的节点属性。 选择跃点查看其 IP 地址。 可以通过观察网络跃点导致的延迟来确定有问题的网络跃点。 要筛选特定路由，请使用可折叠的操作窗格中的筛选器。 要简化网络拓扑，请通过使用操作窗格中的滑块隐藏中间跃点。 可以使用鼠标滚轮来放大或缩小拓扑图。 

图中所示的拓扑为第 3 层拓扑，不包含第 2 层的设备和连接。 

 
![拓扑图](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Log Analytics 搜索 

通过网络性能监视器仪表板和向下钻取页面以图形方式显示的所有数据也可以在 [Log Analytics 搜索](log-analytics-log-search-new.md)中以本地方式使用。 可对存储库中的数据执行交互式分析，并关联来自不同源的数据。 还可以创建自定义警报和视图，并将数据导出到 Excel、Power BI 或可共享的链接。 仪表板的 **“常见查询”** 区域中有一些查询非常有用，可以基于这些查询创建自己的查询和报表。 

## <a name="alerts"></a>警报

网络性能监视器使用 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) 的警报功能。

这意味着使用[操作组](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#overview)管理所有警报。  

如果你是通过 OMS 创建警报的 NPM 用户： 
1. 你将看到一个链接，该链接可将你重定向到 Azure 门户。 单击该链接以访问门户。
2. 单击“网络性能监视器解决方案”磁贴。 
3. 导航到“配置”。  
4. 选择要针对其创建警报的测试，并按照下述步骤进行操作。

如果你是通过 Azure 门户创建警报的 NPM 用户：  
1. 可以选择直接输入电子邮件，也可以选择通过操作组创建警报。
2. 如果选择直接输入电子邮件，则将创建名为 **NPM Email ActionGroup** 的操作组，并将电子邮件 ID 添加到该操作组。
3. 如果选择使用操作组，则需要选择一个以前创建的操作组。 可以在[此处](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal)了解如何创建操作组。 
4. 成功创建警报后，可以使用“管理警报”链接来管理警报。 

##<a name="pricing"></a>定价

[联机](log-analytics-network-performance-monitor-pricing-faq.md)提供了有关定价的信息。

## <a name="provide-feedback"></a>提供反馈 

* **UserVoice**：可以发表有关希望我们开发的网络性能监视器功能的想法。 请访问 [UserVoice 页](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring)。 

* **加入我们的队伍**：我们总是希望一直有新客户不断加入我们的队伍。 那样，能够在早期接触到新功能并有机会帮助我们改进网络性能监视器。 如果有兴趣加入，请填写此 [快速调查](https://aka.ms/npmcohort)。 

## <a name="next-steps"></a>后续步骤 
详细了解[性能监视器](log-analytics-network-performance-monitor-performance-monitor.md)、[服务连接性监视器](log-analytics-network-performance-monitor-performance-monitor.md)和 [ExpressRoute 监视器](log-analytics-network-performance-monitor-expressroute.md)。 
