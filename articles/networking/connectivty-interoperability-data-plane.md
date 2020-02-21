---
title: Azure 后端连接性功能的互操作性：数据平面分析 | Microsoft Docs
description: 本文提供测试设置的数据平面分析，可用于分析 Azure 中 ExpressRoute、站点到站点 VPN 和虚拟网络对等互连之间互操作性。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 815976c672272270e465610e17fef3aea79387f6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526631"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Azure 后端连接性功能的互操作性：数据平面分析

本文介绍了[测试设置][Setup]的数据平面分析。 您还可以查看测试设置的[测试设置配置][Configuration]和[控制平面分析][Control-Analysis]。

数据平面分析检查数据包从一个本地网络（LAN 或虚拟网络）遍历到拓扑中的另一个本地网络所采用的路径。 两个本地网络之间的数据路径不一定是对称的。 因此，本文将单独从反向路径的角度来分析从一个本地网络到另一个网络的正向路径。

## <a name="data-path-from-the-hub-vnet"></a>中心 VNet 中的数据路径

### <a name="path-to-the-spoke-vnet"></a>辐射 VNet 的路径

虚拟网络 (VNet) 对等互连模拟两个对等互连 VNet 之间的网桥功能。 下面列出了从中心 VNet 到辐射 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

下图显示了 Azure 网络观察程序中的 VNet 和辐射 VNet 的图形连接视图：


![1][1]

### <a name="path-to-the-branch-vnet"></a>分支 VNet 的路径

下面列出了从中心 VNet 到分支 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

在此跟踪路由中，第一个跃点是中心 VNet 的 Azure VPN 网关中的 VPN 网关。 第二个跃点是分支 VNet 的 VPN 网关。 对于分支 VNet 的 VPN 网关，其 IP 地址不会在中心 VNet 中播发。 第三个跃点是分支 VNet 中的 VM。

下图显示了网络观察程序中的中心 VNet 和分支 VNet 的图形连接视图：

![2][2]

对于相同的连接，下图显示了网络观察程序中的网格视图：

![3][3]

### <a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

下面列出了从中心 VNet 到本地位置 1 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

在此跟踪路由中，第一个跃点是 Azure ExpressRoute 网关隧道终结点到 Microsoft Enterprise Edge 路由器 (MSEE)。 第二个和第三个跃点为客户边缘 (CE) 路由器和本地位置 1 LAN IP。 这些 IP 地址不会在中心 VNet 中播发。 第四个跃点是本地位置 1 中的 VM。


### <a name="path-to-on-premises-location-2"></a>本地位置 2 的路径

下面列出了从中心 VNet 到本地位置 2 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

在此跟踪路由中，第一跃点是 MSEE 的 ExpressRoute 网关隧道终结点。 第二个和第三个跃点为 CE 路由器和本地位置 2 LAN IP。 这些 IP 地址不会在中心 VNet 中播发。 第四个跃点是本地位置 2 中的 VM。

### <a name="path-to-the-remote-vnet"></a>远程 VNet 的路径

下面列出了从中心 VNet 到远程 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

在此跟踪路由中，第一跃点是 MSEE 的 ExpressRoute 网关隧道终结点。 第二个跃点是远程 VNet 的网关 IP。 第二个跃点的 IP 范围不会在中心 VNet 中播发。 第三个跃点是远程 VNet 中的 VM。

## <a name="data-path-from-the-spoke-vnet"></a>辐射 VNet 中的数据路径

辐射 VNet 共享中心 VNet 的网络视图。 辐射 VNet 通过 VNet 对等互连使用中心 VNet 的远程网关连接，如同两者是直接连接的一样。

### <a name="path-to-the-hub-vnet"></a>中心 VNet 的路径

下面列出了从辐射 VNet 到中心 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>分支 VNet 的路径

下面列出了从辐射 VNet 到分支 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

在此跟踪路由中，第一个跃点是中心 VNet 的 VPN 网关。 第二个跃点是分支 VNet 的 VPN 网关。 分支 VNet 的 VPN 网关的 IP 地址不会在中心/辐射 VNet 中播发。 第三个跃点是分支 VNet 中的 VM。

### <a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

下面列出了从辐射 VNet 到本地位置 1 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

在此跟踪路由中，第一跃点是 MSEE 的 中心 VNet ExpressRoute 网关隧道终结点。 第二个和第三个跃点为 CE 路由器和本地位置 1 LAN IP。 这些 IP 地址不会在中心/辐射 VNet 中播发。 第四个跃点是本地位置 1 中的 VM。

### <a name="path-to-on-premises-location-2"></a>本地位置 2 的路径

下面列出了从辐射 VNet 到本地位置 2 中某个 VM 的跟踪路由输出：


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

在此跟踪路由中，第一跃点是 MSEE 的 中心 VNet ExpressRoute 网关隧道终结点。 第二个和第三个跃点为 CE 路由器和本地位置 2 LAN IP。 这些 IP 地址不会在中心/辐射 VNet 中播发。 第四个跃点是在本地位置 2 中的 VM。

### <a name="path-to-the-remote-vnet"></a>远程 VNet 的路径

下面列出了从辐射 VNet 到远程 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

在此跟踪路由中，第一跃点是 MSEE 的 中心 VNet ExpressRoute 网关隧道终结点。 第二个跃点是远程 VNet 的网关 IP。 第二个跃点的 IP 范围不会在中心/辐射 VNet 中播发。 第三个跃点是远程 VNet 中的 VM。

## <a name="data-path-from-the-branch-vnet"></a>分支 VNet 中的数据路径

### <a name="path-to-the-hub-vnet"></a>中心 VNet 的路径

下面列出了从分支 VNet 到中心 VNet 中某个 VM 的跟踪路由输出：

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

在此跟踪路由中，第一个跃点是分支 VNet 的 VPN 网关。 第二个跃点是中心 VNet 的 VPN 网关。 中心 VNet 的 VPN 网关的 IP 地址不会在远程 VNet 中播发。 第三个跃点是中心 VNet 中的 VM。

### <a name="path-to-the-spoke-vnet"></a>辐射 VNet 的路径

下面列出了从分支 VNet 到辐射 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

在此跟踪路由中，第一个跃点是分支 VNet 的 VPN 网关。 第二个跃点是中心 VNet 的 VPN 网关。 中心 VNet 的 VPN 网关的 IP 地址不会在远程 VNet 中播发。 第三个跃点是辐射 VNet 中的 VM。

### <a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

下面列出了从分支 VNet 到本地位置 1 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

在此跟踪路由中，第一个跃点是分支 VNet 的 VPN 网关。 第二个跃点是中心 VNet 的 VPN 网关。 中心 VNet 的 VPN 网关的 IP 地址不会在远程 VNet 中播发。 第三个跃点是主要 CE 路由器上的 VPN 隧道终结点。 第四个跃点是在本地位置 1 的内部 IP 地址。 此 LAN IP 地址不会在 CE 路由器外部播发。 第五个跃点是本地位置 1 中的目标 VM。

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>本地位置 2 和远程 VNet 的路径

如在控制平面分析中所述，根据网络配置，本地位置 2 和远程 VNet 都看不到分支 VNet。 以下 ping 结果确认了这一事实： 

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

## <a name="data-path-from-on-premises-location-1"></a>本地位置 1 中的数据路径

### <a name="path-to-the-hub-vnet"></a>中心 VNet 的路径

下面列出了从本地位置 1 到中心 VNet 中的 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

在跟踪路由中，前两个跃点属于本地网络。 第三个跃点是面向 CE 路由器的主要 MSEE 接口。 第四个跃点是中心 VNet 的 ExpressRoute 网关。 中心 VNet 的 ExpressRoute 网关的 IP 范围不会播发到本地网络。 第五个跃点是目标 VM。

网络观察程序仅提供以 Azure 为中心的视图。 在本地透视图中，我们将使用 Azure 网络性能监视器。 网络性能监视器提供可以安装在 Azure 外部网络中的服务器上以进行数据路径分析的代理。

下图显示本地位置 1 VM 通过 ExpressRoute 与中心 VNet 中的 VM 建立连接的拓扑视图：

![4][4]

如前文所述，测试设置使用站点到站点 VPN 作为本地位置 1 与中心 VNet 之间的备用 ExpressRoute 连接。 为了测试备份数据路径，让我们在本地位置 1 主要 CE 路由器和相应的 MSEE 之间引发一个 ExpressRoute 链接故障。 为引发 ExpressRoute 链接故障，请关闭面向 MSEE 的 CE 接口：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

下图显示当 ExpressRoute 连接断开时，本地位置 1 VM 通过站点到站点 VPN 连接与中心 VNet 中的 VM 建立连接的拓扑视图：

![5][5]

### <a name="path-to-the-spoke-vnet"></a>辐射 VNet 的路径

下面列出了从本地位置 1 到辐射 VNet 中的 VM 的跟踪路由输出：

让我们恢复 ExpressRoute 主要连接，以对辐射 VNet 执行数据路径分析：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

调出主要 ExpressRoute 1 连接，以执行剩余的数据路径分析。

### <a name="path-to-the-branch-vnet"></a>分支 VNet 的路径

下面列出了从本地位置 1 到分支 VNet 中的 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>本地位置 2 的路径

当我们在[控制平面分析][Control-Analysis]中讨论时，本地位置1对于每个网络配置无法查看本地位置2。 以下 ping 结果确认了这一事实： 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>远程 VNet 的路径

下面列出了从本地位置 1 到远程 VNet 中的 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>本地位置 2 中的数据路径

### <a name="path-to-the-hub-vnet"></a>中心 VNet 的路径

下面列出了从本地位置 2 到远程 VNet 中的 VM 的跟踪路由输出：

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>辐射 VNet 的路径

下面列出了从本地位置 2 到辐射 VNet 中的 VM 的跟踪路由输出：

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>分支 VNet、本地位置 1 和远程 VNet 的路径

正如我们在[控制平面分析][Control-Analysis]中讨论的那样，本地位置1对于分支 VNet 没有任何可见性，对于本地位置1，或每个网络配置到远程 VNet。 

## <a name="data-path-from-the-remote-vnet"></a>远程 VNet 中的数据路径

### <a name="path-to-the-hub-vnet"></a>中心 VNet 的路径

下面列出了从远程 VNet 到中心 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>辐射 VNet 的路径

下面列出了从远程 VNet 到辐射 VNet 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>分支 VNet 和本地位置 2 的路径

当我们在[控制平面分析][Control-Analysis]中讨论时，远程 VNet 对于分支 VNet 或每个网络配置的本地位置2都不可见。 

### <a name="path-to-on-premises-location-1"></a>本地位置 1 的路径

下面列出了从远程 VNet 到本地位置 1 中某个 VM 的跟踪路由输出：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>串联 ExpressRoute 和站点到站点 VPN 连接

###  <a name="site-to-site-vpn-over-expressroute"></a>基于 ExpressRoute 的站点到站点 VPN

可以使用 ExpressRoute Microsoft 对等互连配置站点到站点 VPN，在本地网络与 Azure VNet 之间以私密方式交换数据。 使用此配置可以在确保保密性、真实性和完整性的基础上交换数据。 这种数据交换还可以防重播。 有关如何使用 ExpressRoute Microsoft 对等互连在隧道模式下配置站点到站点 IPsec VPN 的详细信息，请参阅[通过 Expressroute microsoft 对等互连建立的站点到站点 vpn][S2S-Over-ExR]。 

配置使用 Microsoft 对等互连的站点到站点 VPN 的主要限制是吞吐量。 基于 IPsec 隧道的吞吐量受限于 VPN 网关容量。 VPN 网关吞吐量低于 ExpressRoute 吞吐量。 在这种情况下，对高安全性流量使用 IPsec 隧道，并对其他所有流量使用专用对等互连，将有助于优化 ExpressRoute 带宽利用率。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>将站点到站点 VPN 用作 ExpressRoute 的安全故障转移路径

ExpressRoute 充当冗余的线路对，可确保高可用性。 可在不同的 Azure 区域配置异地冗余的 ExpressRoute 连接。 另外，如测试设置中所示，在 Azure 区域中，可以使用站点到站点 VPN 为 ExpressRoute 连接创建故障转移路径。 通过 ExpressRoute 和站点到站点 VPN 播发相同的前缀时，Azure 会优先使用 ExpressRoute。 为了避免 ExpressRoute 与站点到站点 VPN 之间的非对称路由，本地网络配置同样应该优先使用 ExpressRoute 连接，然后再使用站点到站点 VPN 连接。

有关如何配置 ExpressRoute 连接和站点到站点 VPN 的详细信息，请参阅[expressroute 和站点到站点共存][ExR-S2S-CoEx]。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>将后端连接扩展到辐射 VNet 和分支位置

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

中心辐射型 VNet 体系结构的使用非常广泛。 中心是 Azure 中的一个 VNet，充当辐射 VNet 与本地网络之间的连接中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 连接在本地数据中心与中心之间流动。 有关体系结构的详细信息，请参阅[在 Azure 中实现中心辐射型网络拓扑][Hub-n-Spoke]。

在区域内的 VNet 对等互连中，辐射 VNet 可以使用中心 VNet 网关（VPN 和 ExpressRoute 网关）来与远程网络通信。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

你可能想让位于不同区域中的分支 VNet 和本地网络通过中心 VNet 相互通信。 此配置的本机 Azure 解决方案是使用 VPN 建立站点到站点 VPN 连接。 替代方案是对中心内部的路由使用网络虚拟设备 (NVA)。

有关详细信息，请参阅[什么是 VPN 网关？][VPN]和[部署高度可用的 NVA][Deploy-NVA]。


## <a name="next-steps"></a>后续步骤

请参阅[EXPRESSROUTE 常见问题解答][ExR-FAQ]，了解：
-   了解可将多少条 ExpressRoute 线路连接到一个 ExpressRoute 网关。
-   了解可将多少个 ExpressRoute 网关连接到一条 ExpressRoute 线路。
-   了解 ExpressRoute 的其他缩放限制。


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "从集线器 VNet 到分支 VNet 的连接的网络观察程序视图"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "从集线器 VNet 到分支 VNet 的连接的网络观察程序视图"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "从中心 VNet 连接到分支 VNet 的网络观察程序网格视图"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "通过 ExpressRoute 1 将从位置 1 VM 到中心 VNet 的连接网络性能监视器视图"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "通过站点到站点 VPN 网络性能监视器从位置 1 VM 连接到集线器 VNet 的连接视图"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


