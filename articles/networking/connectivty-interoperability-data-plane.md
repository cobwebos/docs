---
title: ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 数据平面分析：Azure 后端连接功能的互操作性 | Microsoft Docs
description: 本页提供创建用来分析 ExpressRoute、站点到站点 VPN 和 VNet 对等互连功能互操作性的测试设置的数据平面分析。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946947"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 数据平面分析

本文介绍测试设置的数据平面分析。 若要了解测试设置，请参阅[测试设置][Setup]。 若要了解测试设置的配置详细信息，请参阅[测试设置配置][Configuration]。 若要了解测试设置的控制平面分析，请参阅[控制平面分析][Control-Analysis]。

数据平面分析检查数据包从一个本地网络 (LAN/VNet) 遍历到拓扑中的另一个本地网络所采用的路径。 两个本地网络之间的数据路径不一定是对称的。 因此，本文将单独从反向路径的角度来分析从一个本地网络到另一个本地网络的正向路径。

##<a name="data-path-from-hub-vnet"></a>中心 VNet 中的数据路径

###<a name="path-to-spoke-vnet"></a>辐射 VNet 的路径

VNet 对等互连模拟两个对等互连 VNet 之间的网桥功能。 下面列出了从中心 VNet 到辐射 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

以下屏幕剪辑是 Azure 网络观察程序显示的中心 VNet 和辐射 VNet 的图形连接视图：


[![1]][1]

###<a name="path-to-branch-vnet"></a>分支 VNet 的路径

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

在上述跟踪路由中，第一个跃点是中心 VNet 的 VPN 网关。 第二个跃点是分支 VNet 的 VPN 网关，其 IP 地址不会在中心 VNet 中播发。 第三个跃点是分支 VNet 中的 VM。

以下屏幕剪辑是 Azure 网络观察程序显示的中心 VNet 和分支 VNet 的图形连接视图：

[![2]][2]

以下屏幕剪辑是 Azure 网络观察程序显示的同一连接的网格视图：

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

在上述跟踪路由中，第一个跃点是 MSEE 的 ExpressRoute 网关隧道终结点。 第二个和第三个跃点分别是 CE 路由器和本地位置 1 LAN IP，这些 IP 地址不会在中心 VNet 中播发。 第四个跃点是本地位置 1 中的 VM。


###<a name="path-to-on-premises-location-2"></a>本地位置 2 的路径

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

在上述跟踪路由中，第一个跃点是 MSEE 的 ExpressRoute 网关隧道终结点。 第二个和第三个跃点分别是 CE 路由器和本地位置 2 LAN IP，这些 IP 地址不会在中心 VNet 中播发。 第四个跃点是本地位置 2 中的 VM。

###<a name="path-to-remote-vnet"></a>远程 VNet 的路径

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

在上述跟踪路由中，第一个跃点是 MSEE 的 ExpressRoute 网关隧道终结点。 第二个跃点是远程 VNet 的网关 IP。 第二个跃点的 IP 范围不会在中心 VNet 中播发。 第三个跃点是远程 VNet 中的 VM。

##<a name="data-path-from-spoke-vnet"></a>辐射 VNet 中的数据路径

回顾前文，辐射 VNet 共享中心 VNet 的网络视图。 辐射 VNet 通过 VNet 对等互连使用中心 VNet 的远程网关连接，如同两者是直接连接的一样。

###<a name="path-to-hub-vnet"></a>中心 VNet 的路径

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>分支 VNet 的路径

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

在上述跟踪路由中，第一个跃点是中心 VNet 的 VPN 网关。 第二个跃点是分支 VNet 的 VPN 网关，其 IP 地址不会在中心/辐射 VNet 中播发。 第三个跃点是分支 VNet 中的 VM。

###<a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

在上述跟踪路由中，第一个跃点是 MSEE 的中心 VNet ExpressRoute 网关隧道终结点。 第二个和第三个跃点分别是 CE 路由器和本地位置 1 LAN IP，这些 IP 地址不会在中心/辐射 VNet 中播发。 第四个跃点是本地位置 1 中的 VM。

###<a name="path-to-on-premises-location-2"></a>本地位置 2 的路径

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

在上述跟踪路由中，第一个跃点是 MSEE 的中心 VNet ExpressRoute 网关隧道终结点。 第二个和第三个跃点分别是 CE 路由器和本地位置 2 LAN IP，这些 IP 地址不会在中心/辐射 VNet 中播发。 第四个跃点是本地位置 2 中的 VM。

###<a name="path-to-remote-vnet"></a>远程 VNet 的路径

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

在上述跟踪路由中，第一个跃点是 MSEE 的中心 VNet ExpressRoute 网关隧道终结点。 第二个跃点是远程 VNet 的网关 IP。 第二个跃点的 IP 范围不会在中心/辐射 VNet 中播发。 第三个跃点是远程 VNet 中的 VM。

##<a name="data-path-from-branch-vnet"></a>分支 VNet 中的数据路径

###<a name="path-to-hub-vnet"></a>中心 VNet 的路径

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

在上述跟踪路由中，第一个跃点是分支 VNet 的 VPN 网关。 第二个跃点是中心 VNet 的 VPN 网关，其 IP 地址不会在远程 VNet 中播发。 第三个跃点是中心 VNet 中的 VM。

###<a name="path-to-spoke-vnet"></a>辐射 VNet 的路径

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

在上述跟踪路由中，第一个跃点是分支 VNet 的 VPN 网关。 第二个跃点是中心 VNet 的 VPN 网关，其 IP 地址不会在远程 VNet 中播发；第三个跃点是辐射 VNet 中的 VM。

###<a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

在上述跟踪路由中，第一个跃点是分支 VNet 的 VPN 网关。 第二个跃点是中心 VNet 的 VPN 网关，其 IP 地址不会在远程 VNet 中播发。 第三个跃点是主要 CE 路由器上的 VPN 隧道终结点。 第四个跃点是本地位置 1 LAN IP 地址的内部 IP 地址，不会在 CE 路由器外部播发。 第五个跃点是本地位置 1 中的目标 VM。

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>本地位置 2 和远程 VNet 的路径

如前面在控制平面分析中所述，根据网络配置，本地位置 2 和远程 VNet 都看不到分支 VNet。 以下 ping 结果确认了这一事实。 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

##<a name="data-path-from-on-premises-location-1"></a>本地位置 1 中的数据路径

###<a name="path-to-hub-vnet"></a>中心 VNet 的路径

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

在上述跟踪路由中，前两个跃点属于本地网络。 第三个跃点是面向 CE 路由器的主要 MSEE 接口。 第四个跃点是中心 VNet 的 ExpressRoute 网关，其 IP 范围不会播发到本地网络。 第五个跃点是目标 VM。

Azure 网络观察程序仅提供以 Azure 为中心的视图。 因此，对于以本地为中心的视图，我们使用了 Azure 网络性能监视器 (NPM)。 NPM 提供代理，这些代理可以是 Azure 外部的网络中安装的服务器，并可执行数据路径分析。

以下屏幕剪辑是本地位置 1 VM 通过 ExpressRoute 与中心 VNet 中的 VM 建立连接的拓扑视图。

[![4]][4]

回顾前文，测试设置使用站点到站点 VPN 作为本地位置 1 与中心 VNet 之间的备用 ExpressRoute 连接。 为了测试反向数据路径，让我们通过关闭面向 MSEE 的 CE 接口，在本地位置 1 主要 CE 路由器与相应 MSEE 之间造成一个 ExpressRoute 链接故障。

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

以下屏幕剪辑是 ExpressRoute 连接断开时，本地位置 1 VM 通过站点到站点 VPN 连接与中心 VNet 中的 VM 建立连接的拓扑视图。

[![5]][5]

###<a name="path-to-spoke-vnet"></a>辐射 VNet 的路径

让我们恢复 ExpressRoute 主要连接，以针对辐射 VNet 执行数据路径分析。

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

让我们打开主要 ExpressRoute-1 连接，以执行剩余的数据路径分析。

###<a name="path-to-branch-vnet"></a>分支 VNet 的路径

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>本地位置 2 的路径

如前面在控制平面分析中所述，根据网络配置，本地位置 2 看不到本地位置 1。 以下 ping 结果确认了这一事实。 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>远程 VNet 的路径

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>本地位置 2 中的数据路径

###<a name="path-to-hub-vnet"></a>中心 VNet 的路径

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>辐射 VNet 的路径

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>分支 VNet、本地位置 1 和远程 VNet 的路径

如前面在控制平面分析中所述，根据网络配置，分支 VNet、本地位置 1 和远程 VNet 看不到本地位置 1。 

##<a name="data-path-from-remote-vnet"></a>远程 VNet 中的数据路径

###<a name="path-to-hub-vnet"></a>中心 VNet 的路径

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>辐射 VNet 的路径

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>分支 VNet 和本地位置 2 的路径

如前面在控制平面分析中所述，根据网络配置，分支 VNet 和本地位置 2 看不到远程 VNet。 


### <a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>延伸阅读

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>串联使用 ExpressRoute 和站点到站点 VPN 连接

####<a name="site-to-site-vpn-over-expressroute"></a>基于 ExpressRoute 的站点到站点 VPN

可以基于 ExpressRoute Microsoft 对等互连配置站点到站点 VPN，专门用于在本地网络与 Azure VNet 之间交换数据，同时确保保密性、防重播能力、真实性和完整性。 有关如何基于 ExpressRoute Microsoft 对等互连以隧道模式配置站点到站点 IPSec VPN 的详细信息，请参阅[基于 ExpressRoute Microsoft 对等互连的站点到站点 VPN][S2S-Over-ExR]。 

基于 Microsoft 对等互连配置 S2S VPN 的主要限制是吞吐量。 基于 IPSec 隧道的吞吐量受限于 VPN 网关容量。 与 ExpressRoute 吞吐量相比，VPN 网关吞吐量更低。 在这种情况下，对高安全性流量使用 IPSec 隧道，并对其他所有流量使用专用对等互连，将有助于优化 ExpressRoute 带宽利用率。

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>将站点到站点 VPN 用作 ExpressRoute 的安全故障转移路径
ExpressRoute 以冗余线路对的形式提供，以确保高可用性。 可在不同的 Azure 区域配置异地冗余的 ExpressRoute 连接。 如同在测试设置中所做的那样，在给定的 Azure 区域中，若要对 ExpressRoute 连接使用故障转移路径，可以使用站点到站点 VPN 实现此目的。 通过 ExpressRoute 和 S2S VPN 播发相同的前缀时，Azure 会优先使用 ExpressRoute 而不是 S2S VPN。 为了避免 ExpressRoute 与 S2S VPN 之间的非对称路由，本地网络配置同样应该优先使用 ExpressRoute 而不是 S2S VPN 连接。

有关如何配置 ExpressRoute 和站点到站点 VPN 共存连接的详细信息，请参阅 [ExpressRoute 和站点到站点共存][ExR-S2S-CoEx]。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>将后端连接扩展到辐射 VNet 和分支位置

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

中心辐射型 VNet 体系结构的使用非常广泛。 中心是 Azure 中的一个虚拟网络 (VNet)，充当辐射 VNet 与本地网络之间的连接中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 连接在本地数据中心与中心之间流动。 有关体系结构的更多详细信息，请参阅[中心辐射型体系结构][Hub-n-Spoke]

区域中的 VNet 对等互连可让辐射 VNet 使用中心 VNet 网关（VPN 和 ExpressRoute 网关）来与远程网络通信。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

如果希望分支 VNet（在不同区域中）和本地网络通过中心 VNet 相互通信，原生的 Azure 解决方案是使用 VPN 建立站点到站点 VPN 连接。 替代做法是对中心内部的路由使用 NVA。

有关如何配置 VPN 网关，请参阅[配置 VPN 网关][VPN]。 有关如何部署可用性 NVA，请参阅[部署高可用性 NVA][Deploy-NVA]。

## <a name="next-steps"></a>后续步骤

若要了解可将多少个 ExpressRoute 线路连接到一个 ExpressRoute 网关，或者可将多少个 ExpressRoute 网关连接到一个 ExpressRoute 线路，或者要了解 ExpressRoute 的其他缩放限制，请参阅 [ExpressRoute 常见问题解答][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "网络观察程序中从中心 VNet 到辐射 VNet 的连接视图"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "网络观察程序中从中心 VNet 到分支 VNet 的连接视图"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "网络观察程序中从中心 VNet 到分支 VNet 的连接网格视图"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "网络性能监视器中通过 ExpressRoute 1 从位置 1 VM 连接到中心 VNet 的视图"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "网络性能监视器中通过 S2S VPN 从位置 1 VM 连接到中心 VNet 的视图"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




