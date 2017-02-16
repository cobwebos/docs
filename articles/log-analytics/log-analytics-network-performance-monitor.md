---
title: "OMS 中的网络性能监视器解决方案 | Microsoft Docs"
description: "网络性能监视器帮助你近乎实时地监视网络性能，以检测并找到网络性能瓶颈。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 820a9463c0e58054cf70324b680c5af8fdcacade
ms.openlocfilehash: 794d9b7d5031730f9ea0f8daae251c825f7b05b0


---
# <a name="network-performance-monitor-preview-solution-in-oms"></a>OMS 中的网络性能监视器（预览）解决方案
> [!NOTE]
> 这是[预览解决方案](log-analytics-add-solutions.md#preview-management-solutions-and-features)。
>
>

本文档介绍了如何设置和使用 OMS 中的网络性能监视器解决方案，这可帮助你近乎实时地监视网络性能，以检测并找到网络性能瓶颈。 通过使用网络性能监视器解决方案，可以监视两个网络、子网或服务器之间的丢失和延迟。 网络性能监视器会检测到诸如流量黑洞、路由错误和常规网络监视方法无法检测到的问题之类的网络问题。 只要突破网络链接的阈值，网络性能监视器就会生成警报并进行通知。 系统可以自动获知这些阈值，也可以配置这些阈值以使用自定义警报规则。 网络性能监视器确保及时检测到网络性能问题，然后将问题的根源本地化为特定网络段或设备。

可以使用解决方案仪表板检测网络问题，该仪表板会显示与网络相关的汇总信息，包括最近的网络运行状况事件、不正常的网络链接以及面临大量数据包丢失和延迟的子网链接。 可以深入网络链接，查看子网链接的当前运行状况状态以及节点到节点链接。 还可以查看网络、子网和节点到节点级别的丢失和延迟的历史走向。 可以通过查看历史走向图检测到瞬时网络问题，并在拓扑图上找到网络瓶颈。 交互式拓扑图使你可以可视化逐跳网络路由，并确定问题的根源。 如同任何其他解决方案一样，可以针对各种分析需求使用日志搜索，以基于网络性能监视器收集的数据创建自定义报告。

该解决方案使用综合事务作为检测网络故障的主要机制。 因此，无需考虑特定网络设备的供应商或型号，就可以使用它。 可跨本地、云 (IaaS) 和混合环境进行工作。 该解决方案会自动发现网络拓扑和你网络中的各种路由。

典型的网络监视产品重点监视网络设备（路由器、交换机等）运行状况，并不会就两个点之间网络连接实际质量提供见解（网络性能监视器会提供这种见解）。

### <a name="using-the-solution-standalone"></a>单独使用解决方案
如果想要监视其关键工作负荷、网络、数据中心或办公地点之间的网络连接质量，可以使用网络性能监视器解决方案本身来监视以下各项之间的连接状况：

* 使用公共网络或专用网络连接的多个数据中心或办公地点
* 正在运行业务线应用程序的关键工作负荷
* 诸如 Microsoft Azure 或 Amazon Web Services (AWS) 之类的公有云服务和本地网络（如果提供 IaaS (VM) 并且将网关配置为允许本地网络和云网络之间进行通信）
* Azure 和本地网络（使用 Express Route 时）

### <a name="using-the-solution-with-other-networking-tools"></a>结合其他网络工具使用解决方案
如果想要监视业务线应用程序，可以将网络性能监视器解决方案用作其他网络工具的配套解决方案。 网速较慢会导致应用程序运行缓慢，网络性能监视器可以帮助你调查因潜在网络问题引起的应用程序性能问题。 由于该解决方案不需要对网络设备进行任何访问，因此应用程序管理员不需要依靠网络团队提供有关网络如何影响应用程序的信息。

此外，如果已购买其他网络监视工具，那么该解决方案可以补充这些工具，因为大多数常规网络监视解决方案不会就端到端网络性能指标（如丢失和延迟）提供见解。  网络性能监视器解决方案可帮助填补这一空白。

## <a name="installing-and-configuring-agents-for-the-solution"></a>安装和配置适用于解决方案的代理
使用[将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md) 和[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md) 中的基本过程安装代理。

> [!NOTE]
> 若要有足够数据发现并监视你的网络资源，需要安装至少 2 个代理。 否则，该解决方案会一直保持为配置状态，直到你安装并配置其他代理。
>
>

### <a name="where-to-install-the-agents"></a>代理安装位置
在安装代理之前，请考虑网络的拓扑结构以及要监视网络的哪些部分。 我们建议你为想要监视的每个子网安装一个以上的代理。 也就是说，对于要监视的每一个子网，请选择至少两个服务器或 VM，并在其上安装代理。

如果不确定你网络的拓扑结构，请在具有关键工作负荷、想要监视网络性能的服务器上安装代理。 例如，可能想要跟踪 Web 服务器与运行 SQL Server 的服务器之间的网络连接。 在该示例中，应在这两台服务器上都安装代理。

代理会监视主机之间的网络连接（链接） - 而不是主机本身。 因此，若要监视某个网络链接，必须在该链接的两个终结点上都安装代理。

### <a name="configure-agents"></a>配置代理
在完成安装代理后，需要打开这些计算机的防火墙端口，确保代理可以进行通信。 需要下载 [EnableRules.ps1 PowerShell 脚本](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634)，然后使用管理员权限在 PowerShell 窗口中在不使用任何参数的情况下运行该脚本。

该脚本会创建网络性能监视器所需的注册表项，还会创建用于允许代理创建相互之间的 TCP 连接的 Windows 防火墙规则。 该脚本创建的注册表项还指定是否记录调试日志和该日志文件的路径。 还会定义用于通信的代理 TCP 端口。 该脚本会自动设置这些注册表项的值，因此不应手动更改这些注册表项。

默认打开的端口为 8084。 通过向该脚本提供参数 `portNumber` 即可使用自定义端口。 不过，应在运行该脚本的所有计算机上使用相同端口。

> [!NOTE]
> EnableRules.ps1 脚本仅在运行该脚本的计算机上配置 Windows 防火墙规则。 如果有网络防火墙，应确保该防火墙允许流量去往网络性能监视器正在使用的 TCP 端口。
>
>

## <a name="configuring-the-solution"></a>配置解决方案
使用以下信息安装和配置解决方案。

1. 网络性能监视器解决方案将从运行 Windows Server 2008 SP1 或更高版本或者 Windows 7 SP1 或更高版本的计算机获取数据，这与 Microsoft Monitoring Agent (MMA) 的要求是相同的。
2. 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将网络性能监视器解决方案添加到 OMS 工作区。  
   ![网络性能监视器符号](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. 在 OMS 门户中，将看到一个标题为**网络性能监视器**的新磁贴以及*解决方案需要进行额外配置*消息。 需要配置解决方案，以基于代理发现的子网和节点添加网络。 单击“网络性能监视器”开始配置默认网络。  
   ![解决方案需要进行额外配置](./media/log-analytics-network-performance-monitor/npm-config.png)

### <a name="configure-the-solution-with-a-default-network"></a>使用默认网络配置解决方案
在配置页上，会看到名为**默认**的单个网络。 尚未定义任何网络时，会将自动发现的所有子网置于默认网络中。

每当创建网络时，会向创建的网络中添加子网，并从默认网络中删除该子网。 删除某个网络时，会自动将其中的所有子网返回到默认网络。

换句话说，默认网络是未包含在用户定义的任何网络中的所有子网的容器。 无法编辑或删除默认网络。 它始终保留在系统中。 不过，可以根据需要创建任意数量的网络。

在大多数情况下，会将组织中的子网组织在一个以上的网络中，应创建至少一个网络以便对子网进行逻辑分组。

### <a name="create-new-networks"></a>新建网络
网络性能监视器中的网络是子网的容器。 可以创建根据你喜好命名的网络，然后将子网添加到该网络中。 例如，可以创建名为 *Building1* 的网络，然后添加子网；也可以创建名为 *DMZ* 的网络，然后将外围安全区域所属的全部子网添加到该网络。

#### <a name="to-create-a-new-network"></a>新建网络
1. 单击“添加网络”，然后键入网络名称和描述。
2. 选择一个或多个子网，然后单击“添加”。
3. 单击“保存”以保存配置。  
   ![添加网络](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>等待数据聚合
首次保存配置之后，该解决方案会开始收集已安装代理的节点之间的网络数据包丢失和延迟信息。 此过程可能需要一些时间，有时会超过 30 分钟。 在此状态下，概述页中的网络性能监视器磁贴会显示一条指示“正在进行数据聚合”的消息。

![正在进行数据聚合](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

当数据完成上传后，会看到网络性能监视器磁贴更新为正在显示数据。

![网络性能监视器磁贴](./media/log-analytics-network-performance-monitor/npm-tile.png)

单击该磁贴即可查看网络性能监视器仪表板。

![网络性能监视器仪表板](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>编辑子网的监视设置
配置页上的“子网”选项卡中将列出至少安装一个代理的所有子网。

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>启用或禁用对特定子网的监视
1. 选中或清除“子网 ID”旁边的框，然后确保已选中或已清除“用于监视”（视情况而定）。 可以选择或清除多个子网。 禁用时，不会监视子网，因为会更新代理以停止 ping 其他代理。
2. 通过以下方式选择想要监视的特定子网的节点：从列表中选择子网，然后在包含未监视节点的列表与包含已监视节点的列表之间移动所需节点。
   可以根据需要向子网添加自定义“描述”。
3. 单击“保存”以保存配置。  
   ![编辑子网](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>选择要监视的节点
所有已安装代理的节点会列在“节点”选项卡上。

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>启用或禁用对节点的监视
1. 选择要监视的节点或清除要停止监视的节点。
2. 单击“用于监视”，或清除它（视情况而定）。
3. 单击“保存” 。  
   ![启用节点监视](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>设置监视规则
突破阈值时，网络性能监视器会生成与一对节点、子网或网络链接之间的连接有关的运行状况事件。 系统可以自动获知这些阈值，也可以为它们配置自定义警报规则。

系统会创建*默认规则*，并且每当一对网络或子网链接之间的丢失或延迟突破系统获知的阈值时，它会生成运行状况事件。 可以选择禁用默认规则，并创建自定义监视规则

#### <a name="to-create-custom-monitoring-rules"></a>创建自定义监视规则
1. 在“监视器”选项卡中，单击“添加规则”，然后输入规则名称和描述。
2. 从列表中选择要监视的网络或子网链接对。
3. 首先从网络下拉列表中选择含有第一个感兴趣子网的网络，然后从相应的子网下拉列表中选择子网。
   如果想要监视网络链接中的所有子网，请选择“全部子网”。 同样，选择其他感兴趣的子网。 还可以单击“添加例外”从已选项中排除对特定子网链接的监视。
4. 如果不希望针对所选项生成运行状况事件，请清除“启用对此规则覆盖的链接进行运行状况监视”。
5. 选择监视条件。
   可以通过键入阈值，针对运行状况事件生成设置自定义阈值。 只要条件值超出其针对所选网络/子网对选择的阈值，就会生成运行状况事件。
6. 单击“保存”以保存配置。  
   ![创建自定义监视规则](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

## <a name="data-collection-details"></a>数据收集详细信息
网络性能监视器使用 TCP SYN-SYNACK-ACK 握手数据包收集丢失和延迟信息，还使用路由跟踪获取拓扑信息。

下表显示了数据收集方法，以及有关如何为网络性能监视器收集数据的其他详细信息。

| 平台 | 直接代理 | SCOM 代理 | Azure 存储空间 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![是](./media/log-analytics-network-performance-monitor/oms-bullet-green.png) |![是](./media/log-analytics-network-performance-monitor/oms-bullet-green.png) |![否](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |![否](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |![否](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |每隔 5 秒钟进行 TCP 握手，每隔 3 分钟发送数据 |

该解决方案利用综合事务来评估网络的运行状况。 网络中各个点安装的 OMS 代理会与另一个代理交换 TCP 数据包，并且在该过程中了解往返时间和数据包丢失（如果存在）。 每个代理还会定期对其他代理执行跟踪路由，以全部找出网络中必须测试的各种路由。 使用此数据，代理就可以推断出网络延迟和数据包丢失图。 代理每隔&5; 秒钟重复执行测试，聚合数据持续&3; 分钟，然后再将数据上传到 OMS。

> [!NOTE]
> 尽管代理相互频繁地通信，但它们在进行测试时并不会产生大量网络流量。 代理仅仅依靠 TCP SYN-SYNACK-ACK 握手数据包确定丢失和延迟 - 不会交换任何数据包。 在此过程中，代理只在需要时才会进行相互通信，并且会对代理通信拓扑进行优化以减少网络流量。
>
>

## <a name="using-the-solution"></a>使用解决方案
本部分介绍了所有仪表板函数及其使用方法。

### <a name="solution-overview-tile"></a>解决方案概述磁贴
在启用了网络性能监视器解决方案后，OMS 概述页中的解决方案磁贴将提供网络运行状况的快速浏览。 将显示一张环形图，呈现正常和不正常子网链接的数目。 单击该磁贴时，会打开解决方案仪表板。

![网络性能监视器磁贴](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>网络性能监视器解决方案仪表板
“网络摘要”边栏选项卡中会显示网络的摘要及其相对大小。 磁贴随后会显示系统中的网络链接、子网链接和路径（路径由代理的两个主机的 IP 地址以及这两个主机之间的跃点组成）的总数。

“排名靠前的网络运行状况事件”边栏选项卡中会提供系统中最新运行状况事件和警报列表以及事件发生后所经过的时间。 只要网络或子网链接的数据包丢失或延迟超过阈值，就会生成运行状况事件或警报。

“排名靠前的不正常的网络链接”边栏选项卡中会显示不正常的网络链接列表。 此类网络链接定义如下：当时这些网络链接中存在一个或多个不良运行状况事件。

“丢失最多、排名靠前的子网链接”和“延迟最多的子网链接”边栏选项卡中会分别显示按数据包丢失排列的排名靠前的子网链接和按延迟排列的排名靠前的子网链接。 某些网络链接中可能会发生高延迟或一定量的数据包丢失。 此类链接会显示在前十个列表中，但不会标记为不正常。

“常见查询”边栏选项卡中包含一组用于直接提取网络监视原始数据的搜索查询。 可以基于这些查询创建用于生成自定义报表的查询。

![网络性能监视器仪表板](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>深入了解
可以单击解决方案仪表板中的各个链接来进一步了解任何感兴趣部分。 例如，当看到警报或不正常的网络链接出现在仪表板上时，可以单击它以进一步进行调查。 会将你转到列出该特定网络链接的所有子网链接的页面。 将能够查看每个子网链接的丢失、延迟和运行状况状态，快速找出哪个子网链接导致了该问题。 可以单击“查看节点链接”查看不正常的子网链接的所有节点链接。 然后，可以查看个别节点到节点链接，找到不正常的节点链接。

可以单击“查看拓扑”查看源节点和目标节点之间路由的逐跳拓扑。 不正常的路由或跃点会显示为红色，以便你能够快速确定网络特定部分的问题。

![数据挖掘](./media/log-analytics-network-performance-monitor/npm-drill.png)

#### <a name="trend-charts"></a>趋势图
在进行挖掘的每个级别，都可以查看某个网络链接的丢失和延迟趋势。 趋势图也适用于子网和节点链接。 可以通过使用图表顶部的时间控制，更改图绘制的时间间隔。

趋势图从历史角度向你呈现了网络链接的性能。 某些网络问题本质上是瞬时发生的，仅通过查看该网络的当前状态难以捕获。 这是因为问题可能会快速浮现并在有人注意到之前就消失，只在之后的某个时间点才会再次出现。 此类瞬时问题对于应用程序管理员而言也比较棘手，因为这些问题常常表现为应用程序响应时间原因不明的增加，即使所有应用程序组件看起来都平稳地运行时也会如此。

可以通过查看趋势图（其中，问题会呈现为网络延迟或数据包丢失出现激增情况）轻松地检测到这些类型的问题。

![趋势图](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>逐跳拓扑图
网络性能监视器会向你呈现交互式拓扑图中两个节点之间路由的逐跳拓扑图。 可以通过以下方式查看拓扑图：选择某个节点链接，然后单击“查看拓扑”。 此外，也可以通过单击仪表板上的“路径”磁贴来查看拓扑图。 单击仪表板上的“路径”时，需要从左侧面板中选择源节点和目标节点，然后单击“绘图”即可绘制两个节点之间的路由。

拓扑图会显示两个节点之间存在多少个路由，以及数据包会采用哪条路径。 在拓扑图上，网络性能瓶颈会以红色标记。 可以通过查看拓扑图上标为红色的元素，定位到发生问题的网络连接或网络设备。

当在拓扑图上单击某个节点或将光标悬停在其上方时，会看到诸如 FQDN 和 IP 地址之类的节点属性。 单击跃点查看其 IP 地址。 可以通过以下方式突出显示特定路由：清除并仅选择要在该图上突出显示的路由。 可以通过使用鼠标滚轮来放大或缩小拓扑图。

请注意，图中所示的拓扑为第 3 层拓扑，不包含第 2 层的设备和连接。

![逐跳拓扑图](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>错误本地化
网络性能监视器无需连接到网络设备即可找到网络瓶颈。 基于从网络收集的数据以及通过在网络图上应用高级算法，网络性能监视器可大概估计最有可能是问题根源的网络部分。

当不可访问跃点时，此方法可用于确定网络瓶颈，因为不需要从网络设备（例如，路由器或交换机）收集任何数据。 当两个节点之间的跃点不在你的管理控制下时，此方法也非常有用。 例如，跃点可能是 ISP 路由器。

### <a name="log-analytics-search"></a>Log Analytics 搜索
通过网络性能监视器仪表板和挖掘页面以图形方式显示的所有数据也可以在 Log Analytics 搜索中以本地方式使用。 可以使用搜索查询语言查询数据，然后通过将数据导出到 Excel 或 PowerBI 创建自定义报表。 仪表板的“常见查询”边栏选项卡中有一些查询非常有用，可以基于这些查询创建自己的查询和报表。

![搜索查询](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>调查运行状况警报的根本原因
现在，你已对网络性能监视器有所了解，下面我们简单分析一下某个运行状况事件的根本原因。

1. 在“概述”页上，通过观察“网络性能监视器”磁贴即可快速获取网络的运行状况快照。 请注意，除受监视的 80 子网链接之外，43 不正常。 这需要调查。 单击该磁贴以查看解决方案仪表板。  
   ![网络性能监视器磁贴](./media/log-analytics-network-performance-monitor/npm-investigation01.png)
2. 在以下示例图中，会观察到当前存在 4 个运行状况事件以及 4 个不正常的网络链接。 若要调查该问题，请单击 **Sharepoint-Web** 网络链接以找出问题的根源。  
   ![不正常的网络链接示例](./media/log-analytics-network-performance-monitor/npm-investigation02.png)
3. “挖掘”页中会显示 **Sharepoint-Web** 网络链接中的所有子网链接。 你会注意到，这两个子网链接的延迟已超过阈值，使网络链接不正常。 还会看到这两个子网链接的延迟趋势。 图表中的时间选择控制可用于重点关注所需时间范围。 可以查看延迟达到其峰值时的当天时间。 若要调查问题，稍后也可以搜索日志来获取此时间段。 单击“查看节点链接”以进一步进行挖掘。  
   ![不正常的子网链接示例](./media/log-analytics-network-performance-monitor/npm-investigation03.png)
4. 与上一页类似，特定子网链接的挖掘页面会列出其构成节点链接。 可以在此处执行与上一步类似的操作。 单击“查看拓扑”即可查看 2 个节点之间的拓扑。  
   ![不正常的节点链接示例](./media/log-analytics-network-performance-monitor/npm-investigation04.png)
5. 2 个所选节点之间的所有路径绘制在拓扑图中。 可以在拓扑图上可视化两个节点之间路由的逐跳拓扑。 它清晰地呈现两个节点之间存在多少个路由，以及数据包会采用哪条路径。 网络性能瓶颈会标记为红色。 可以通过查看拓扑图上标为红色的元素，定位到发生问题的网络连接或网络设备。  
   ![不正常的拓扑视图示例](./media/log-analytics-network-performance-monitor/npm-investigation05.png)
6. 可以在“路径详细信息”窗格中查看每个路径中的丢失、延迟和跃点数。 在此示例中，可以看到存在 3 个不正常的路径，如窗格中所示。 滚动条可用于查看这些不正常的路径的详细信息。  使用复选框选择其中一个路径，以便只绘制这一个路径的拓扑。 可以使用鼠标滚轮放大或缩小拓扑图。

   在下图中，通过查看红色标记的路径和跃点，就可以清楚地了解到网络特定部分的问题区域的根本原因。 单击拓扑图中的节点会呈现该节点的属性，包括 FQDN 和 IP 地址。 单击跃点会显示该跃点的 IP 地址。  
   ![不正常的拓扑 - 路径详细信息示例](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看详细的网络性能数据记录。



<!--HONumber=Jan17_HO1-->


