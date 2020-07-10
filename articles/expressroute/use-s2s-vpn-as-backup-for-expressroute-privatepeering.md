---
title: 将 S2S VPN 用作 Azure ExpressRoute 专用对等互连的备用解决方案 | Microsoft Docs
description: 本页提供了有关将 S2S VPN 用作 Azure ExpressRoute 专用对等互连的备用解决方案的体系结构建议。
services: networking
author: rambk
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: df4108604c656cd6383bd57b462c0f12f31bdd7b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206876"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>将 S2S VPN 用作 Azure ExpressRoute 专用对等互连的备用解决方案

在标题为[使用 ExpressRoute 专用对等互连进行灾难恢复设计][DR-PP]的文章中，我们讨论了 ExpressRoute 专用对等互连对备用连接解决方案的需求，以及如何使用异地冗余的 ExpressRoute 线路来实现此目的。 本文将探讨如何利用并维护站点到站点 (S2S) VPN，以将其用作 ExpressRoute 专用对等互连的备用解决方案。 

与异地冗余的 ExpressRoute 线路不同，只能在主动-被动模式下使用 ExpressRoute-VPN 灾难恢复组合。 在被动模式下使用任何备用网络连接的一个主要难点在于，被动连接经常连同主要连接一起发生故障。 被动连接故障的常见原因是缺乏主动的维护。 因此，本文将重点介绍如何验证和主动维护充当 ExpressRoute 专用对等互连备用解决方案的 S2S VPN 连接。

>[!NOTE] 
>通过 ExpressRoute 和 VPN 播发给定的路由时，Azure 倾向于通过 ExpressRoute 进行路由。  
>

本文将介绍如何从 Azure 角度以及如何从客户方网络边缘角度验证连接。 无论你是否管理与 Microsoft 网络实体对等互连的客户方网络设备，能够通过上述任一方式进行验证都会有所帮助。 

## <a name="example-topology"></a>示例拓扑

在我们的设置中，有一个本地网络已通过 ExpressRoute 线路和 S2S VPN 连接与 Azure 中心 VNet 建立了连接。 该 Azure 中心 VNet 又与辐射 VNet 建立了对等互连，如下图所示：

![1][1]

在设置中，ExpressRoute 线路的终点是本地的一对“客户边缘”(CE) 路由器。 本地 LAN 通过一对以先导-后继模式运行的防火墙连接到 CE 路由器。 S2S VPN 直接以防火墙为终点。

下表列出了拓扑的关键 IP 前缀：

| **实体** | **前缀** |
| --- | --- |
| 本地 LAN | 10.1.11.0/25 |
| Azure 中心 VNet | 10.17.11.0/25 |
| Azure 辐射 VNet | 10.17.11.128/26 |
| 本地测试服务器 | 10.1.11.10 |
| 辐射 VNet 测试 VM | 10.17.11.132 |
| ExpressRoute 主要连接 P2P 子网 | 192.168.11.16/30 |
| ExpressRoute 次要连接 P2P 子网 | 192.168.11.20/30 |
| VPN 网关主要 BGP 对等方 IP | 10.17.11.76 |
| VPN 网关次要 BGP 对等方 IP | 10.17.11.77 |
| 本地防火墙 VPN BGP 对等方 IP | 192.168.11.88 |
| 与防火墙 IP 连接的主要 CE 路由器接口 | 192.168.11.0/31 |
| 与主要 CE 路由器 IP 连接的防火墙接口 | 192.168.11.1/31 |
| 与防火墙 IP 连接的次要 CE 路由器接口 | 192.168.11.2/31 |
| 与次要 CE 路由器 IP 连接的防火墙接口 | 192.168.11.3/31 |


下表列出了拓扑的 ASN：

| **自治系统** | **ASN** |
| --- | --- |
| 本地 | 65020 |
| Microsoft Enterprise Edge | 12076 |
| 虚拟网络网关 (ExR) | 65515 |
| 虚拟网络网关 (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>全面、均衡地提供高可用性

### <a name="configuring-for-high-availability"></a>高可用性配置

[配置 ExpressRoute 和站点到站点共存连接][Conf-CoExist]介绍了如何配置 ExpressRoute 线路和 S2S VPN 共存的连接。 如[使用 ExpressRoute 进行高可用性设计][HA]中所述，为了提高 ExpressRoute 的高可用性，我们的设置一直保持网络冗余（避免单一故障点），直至终结点。 另外，ExpressRoute 线路的主要和次要连接配置为以主动-主动模式运行，将以相同的方式通过这两个连接来播发本地前缀。 

下面显示了如何通过 ExpressRoute 线路的主要连接播发主要 CE 路由器的本地路由（Junos 命令）：

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

下面显示了如何通过 ExpressRoute 线路的次要连接播发次要 CE 路由器的本地路由（Junos 命令）：

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

为了提高备用连接的高可用性，S2S VPN 也配置为采用主动-主动模式。 Azure VPN 网关配置如下所示。 请注意，网关的 BGP 对等方 IP 地址 10.17.11.76 和 10.17.11.77 也作为 VPN 配置的一部分列出。

![2][2]

防火墙将本地路由播发到 VPN 网关的主要和次要 BGP 对等方。 下面显示了路由播发 (Junos)：

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>将 S2S VPN 配置为主动-主动模式不仅能够为灾难恢复备用网络连接提供高可用性，而且还能为备用连接提供更高的吞吐量。 换言之，我们建议将 S2S VPN 配置为主动-主动模式，因为这会强制创建多个基础隧道。
>

### <a name="configuring-for-symmetric-traffic-flow"></a>配置对称流量流

我们注意到，在通过 ExpressRoute 和 S2S VPN 播发给定的本地路由时，Azure 会优先采用 ExpressRoute 路径。 若要强制 Azure 优先采用 S2S VPN 路径而不是采用共存的 ExpressRoute，需要通过 VPN 连接播发更具体的路由（子网掩码更大的更长前缀）。 此处，我们的目标是仅将 VPN 连接用作备用连接。 因此，Azure 的默认路径选择行为与我们的目标相符。 

我们需要负责确保从本地发往 Azure 的流量也优先采用 ExpressRoute 路径而不是 S2S VPN。 本地设置中的 CE 路由器和防火墙的默认本地首选项为 100。 因此，如果将通过 ExpressRoute 专用对等互连接收的路由的本地首选项配置为大于 100（例如 150），可使发往 Azure 的流量优先采用处于稳定状态的 ExpressRoute 线路。

下面显示了作为 ExpressRoute 线路主要连接的终点的主要 CE 路由器的 BGP 配置。 请注意，通过 iBGP 会话播发的路由的本地首选项值配置为 150。 同样，我们需要确保作为 ExpressRoute 线路次要连接的终结点的次要 CE 路由器的本地首选项也配置为 150。

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

本地防火墙的路由表确认以下事项（如下所示）：对于发往 Azure 的本地流量，优先采用的路径是处于稳定状态的 ExpressRoute。

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

在上面的路由表中，对于中心和辐射 VNet 路由 -- 10.17.11.0/25 和 10.17.11.128/26 -- 我们可以看到，优先采用的是 ExpressRoute 线路而不是 VPN 连接。 192.168.11.0 和 192.168.11.2 是连接 CE 路由器的防火墙接口上的 IP。

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>验证通过 S2S VPN 进行的路由交换

在上文中，我们已验证了将防火墙的本地路由播发到 VPN 网关的主要和次要 BGP 对等方。 在此基础上，让我们确认防火墙从 VPN 网关的主要和次要 BGP 对等方接收的 Azure 路由。

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

同样，让我们验证 Azure VPN 网关接收的本地网络路由前缀。 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

从上文可以看出，VPN 网关包含其主要和次要 BGP 对等方接收的路由。 它还显示通过主要和次要 ExpressRoute 连接接收的路由（AS 路径前面附带了 12076 的路由）。 若要确认通过 VPN 连接接收的路由，需要知道这些连接的本地 BGP 对等方 IP。 在我们的演示设置中，该 IP 为 192.168.11.88，我们确实看到了通过该 IP 接收的路由。

接下来，让我们验证由 Azure VPN 网关播发到本地防火墙 BGP 对等方 (192.168.11.88) 的路由。

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

看不到路由交换即意味着连接失败。 请参阅[故障排除：Azure 站点到站点 VPN 连接无法建立连接并停止工作][VPN Troubleshoot]，以获取有关排查 VPN 连接问题的帮助。

## <a name="testing-failover"></a>测试故障转移

确认能够通过 VPN 连接（控制平面）成功进行路由交换后，我们已准备好将流量（数据平面）从 ExpressRoute 连接切换到 VPN 连接。 

>[!NOTE] 
>在生产环境中，必须在计划的网络维护工作时段执行故障转移测试，因为它可能会造成服务中断。
>

在执行流量切换之前，让我们对设置中的、从本地测试服务器到辐射 VNet 中测试 VM 的当前路径进行跟踪路由。

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

设置的主要和次要 ExpressRoute 点到点连接子网分别为 192.168.11.16/30 和 192.168.11.20/30。 在上面的跟踪路由中，步骤 3 显示我们连接了 192.168.11.18，它是主要 MSEE 的接口 IP。 存在 MSEE 接口即可确认当前路径是通过 ExpressRoute 建立的，这符合预期。

让我们根据[重置 ExpressRoute 线路对等互连][RST]中的报告，使用以下 PowerShell 命令禁用 ExpressRoute 线路的主要和次要对等互连。

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

故障转移切换时间取决于 BGP 融合时间。 在我们的设置中，故障转移切换花费了几秒钟时间（小于 10 秒）。 切换后，重复运行跟踪路由会显示以下路径：

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

跟踪路由结果确认：通过 S2S VPN 建立的备用连接是主动的，可以在主要和次要 ExpressRoute 连接都失败时提供服务连续性。 为了完成故障转移测试，让我们使用以下命令集来重新启用 ExpressRoute 连接并规范化流量流。

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

若要确认流量是否已切换回到 ExpressRoute，请重复运行跟踪路由，并确保流量通过 ExpressRoute 专用对等互连传送。

## <a name="next-steps"></a>后续步骤

ExpressRoute 设计用于实现高可用性，且不会在 Microsoft 网络中造成单一故障点。 不过，ExpressRoute 线路仍然局限于单个地理区域和某个服务提供商。 S2S VPN 可以很好地充当 ExpressRoute 线路的灾难恢复被动式备用解决方案。 要使被动式备用连接解决方案可靠，定期维护被动配置并定期验证连接非常重要。 不要让 VPN 配置过时，并在维护时段定期（例如每个季度）重复执行本文中所述的验证和故障转移测试步骤，这一点至关重要。

若要启用基于 VPN 网关指标的监视和警报，请参阅[基于 VPN 网关指标设置警报][VPN-alerts]。

若要在发生 ExpressRoute 故障后加速 BGP 融合，请[配置基于 ExpressRoute 的 BFD][BFD]。

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "考虑的拓扑"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN 网关配置"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



