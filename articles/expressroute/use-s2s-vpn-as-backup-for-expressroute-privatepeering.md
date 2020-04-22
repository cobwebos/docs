---
title: 使用 S2S VPN 作为 Azure 快速路由专用对等互连的备份 |微软文档
description: 此页提供使用 S2S VPN 备份 Azure ExpressRoute 专用对等互连的体系结构建议。
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687829"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>使用 S2S VPN 作为 ExpressRoute 专用对等互连的备份

在题为"[使用 ExpressRoute 专用对等互连进行灾难恢复设计][DR-PP]"的文章中，我们讨论了为 ExpressRoute 专用对等互连连接提供备份连接解决方案的必要性，以及如何为此使用异地冗余 ExpressRoute 电路。 在本文中，让我们考虑如何利用和维护站点到站点 （S2S） VPN 作为 ExpressRoute 专用对等互连的背面。 

与异地冗余 ExpressRoute 电路不同，您只能在主动-被动模式下使用 ExpressRoute-VPN 灾难恢复组合。 在被动模式下使用任何备份网络连接的一个主要挑战是，被动连接通常与主连接同时失败。 被动连接故障的常见原因是缺少主动维护。 因此，在本文中，让我们重点介绍如何验证并主动维护正在备份 ExpressRoute 专用对等互连的 S2S VPN 连接。

>[!NOTE] 
>当通过 ExpressRoute 和 VPN 通告给定路由时，Azure 更喜欢路由而不是快速路由。  
>

在本文中，让我们看看如何从 Azure 角度和客户端网络边缘角度验证连接。 无论是否管理与 Microsoft 网络实体对等的客户端网络设备，从任一端进行验证都会有所帮助。 

## <a name="example-topology"></a>示例拓扑

在我们的设置中，我们通过 ExpressRoute 电路和 S2S VPN 连接将本地网络连接到 Azure 集线器 VNet。 Azure 中心 VNet 依次与分支 VNet 对等互连，如下图所示：

![1][1]

在设置中，ExpressRoute 电路在本地的一对"客户边缘"（CE） 路由器上终止。 本地 LAN 通过一对在领导者跟随模式下运行的防火墙连接到 CE 路由器。 S2S VPN 直接终止在防火墙上。

下表列出了拓扑的关键 IP 前缀：

| **实体** | **前缀** |
| --- | --- |
| 本地局域网 | 10.1.11.0/25 |
| Azure 中心 VNet | 10.17.11.0/25 |
| Azure 分支 VNet | 10.17.11.128/26 |
| 本地测试服务器 | 10.1.11.10 |
| 分支 VNet 测试 VM | 10.17.11.132 |
| 快速路由主连接 p2p 子网 | 192.168.11.16/30 |
| 快速路由辅助连接 p2p 子网 | 192.168.11.20/30 |
| VPN 网关主 BGP 对等 IP | 10.17.11.76 |
| VPN 网关辅助 BGP 对等 IP | 10.17.11.77 |
| 本地防火墙 VPN BGP 对等 IP | 192.168.11.88 |
| 主 CE 路由器 i/f 面向防火墙 IP | 192.168.11.0/31 |
| 面向主 CE 路由器 IP 的防火墙 i/f | 192.168.11.1/31 |
| 辅助 CE 路由器 i/f 面向防火墙 IP | 192.168.11.2/31 |
| 面向辅助 CE 路由器 IP 的防火墙 i/f | 192.168.11.3/31 |


下表列出了拓扑的 ASN：

| **自主系统** | **ASN** |
| --- | --- |
| 本地 | 65020 |
| 微软企业边缘 | 12076 |
| 虚拟网络 GW （ExR） | 65515 |
| 虚拟网络 GW （VPN） | 65515 |

## <a name="high-availability-without-asymmetricity"></a>高可用性，无不对称性

### <a name="configuring-for-high-availability"></a>配置，提供高可用性

[配置 ExpressRoute 和站点到站点共存的连接][Conf-CoExist]讨论如何配置共存的 ExpressRoute 电路和 S2S VPN 连接。 正如我们[在使用 ExpressRoute 设计高可用性][HA]时所讨论的，为了提高 ExpressRoute 的高可用性，我们的设置一直保持网络冗余（避免单点故障），一直保持到端点。 此外，ExpressRoute 电路的主连接和辅助连接配置为在主动-主动模式下运行，通过两个连接以相同的方式通告本地前缀。 

通过 ExpressRoute 电路的主连接的主 CE 路由器的本地路由通告如下所示（Junos 命令）：

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

通过 ExpressRoute 电路的辅助连接，辅助 CE 路由器的本地路由通告如下所示（Junos 命令）：

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

为了提高备份连接的高可用性，S2S VPN 也配置为主动-主动模式。 Azure VPN 网关配置如下所示。 请注意，作为 VPN 配置 VPN 的一部分，还列出了网关的 BGP 对等 IP 地址 -- 10.17.11.76 和 10.17.11.77。

![2][2]

防火墙通告到 VPN 网关的主数据库和辅助 BGP 对等方的本地路由。 路由播发如下所示（Junos）：

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>在主动-主动模式下配置 S2S VPN 不仅为您的灾难恢复备份网络连接提供高可用性，而且还为备份连接提供了更高的吞吐量。 换句话说，建议在主动-主动模式下配置 S2S VPN，因为它强制创建多个基础隧道。
>

### <a name="configuring-for-symmetric-traffic-flow"></a>配置对称流量流

我们注意到，当通过 ExpressRoute 和 S2S VPN 通告给定的本地路由时，Azure 更喜欢 ExpressRoute 路径。 要强制 Azure 首选 S2S VPN 路径而不是共存的 ExpressRoute，您需要通过 VPN 连接通告更具体的路由（具有较大子网掩码的较长前缀）。 我们在这里的目标是只使用 VPN 连接作为返回。 因此，Azure 的默认路径选择行为符合我们的目标。 

我们有责任确保从本地发送到 Azure 的流量也更喜欢快速路由路径，而不是 S2S VPN。 本地设置中 CE 路由器和防火墙的默认本地首选项为 100。 因此，通过配置通过 ExpressRoute 专用对等互连接收的路由的本地首选项大于 100（例如 150），我们可以使发送到 Azure 的流量选择处于稳定状态的 ExpressRoute 电路。

终止 ExpressRoute 电路主连接的主 CE 路由器的 BGP 配置如下所示。 请注意，在 iBGP 会话上通告的路由的本地首选项的值配置为 150。 同样，我们需要确保终止 ExpressRoute 电路的辅助连接的辅助 CE 路由器的本地首选项也配置为 150。

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

本地防火墙的路由表确认（如下所示），对于发往 Azure 的本地流量，首选路径在稳定状态下超过 ExpressRoute。

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

在上述路由表中，对于集线器和分支 VNet 路由 -10.17.11.0/25 和 10.17.11.128/26 -- 192.168.11.0 和 192.168.11.2 是面向 CE 路由器的防火墙接口上的 IP。

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>通过 S2S VPN 验证路由交换

在本文前面，我们验证了防火墙的本地路由播发到 VPN 网关的主数据库和辅助 BGP 对等体。 此外，让我们确认防火墙从 VPN 网关的主数据库和辅助 BGP 对等体接收的 Azure 路由。

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

同样，让我们验证 Azure VPN 网关收到的本地网络路由前缀。 

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

如上所述，VPN 网关具有 VPN 网关的主数据库和辅助 BGP 对等方接收的路由。 它还可查看通过主和辅助 ExpressRoute 连接接收的路由（AS 路径以 12076 预示的路线）。 要确认通过 VPN 连接接收的路由，我们需要了解连接的本地 BGP 对等 IP。 在所考虑的设置中，它是 192.168.11.88，我们确实看到了从它接收的路线。

接下来，让我们验证 Azure VPN 网关通告到本地防火墙 BGP 对等体 （192.168.11.88） 通告的路由。

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


看不到路由交换指示连接失败。 请参阅[故障排除：Azure 站点到站点 VPN 连接无法连接并停止工作][VPN Troubleshoot]以寻求对 VPN 连接进行故障排除的帮助。

## <a name="testing-failover"></a>测试故障转移

现在，我们已经确认通过 VPN 连接（控制平面）成功路由交换，我们设置为将流量（数据平面）从 ExpressRoute 连接切换到 VPN 连接。 

>[!NOTE] 
>在生产环境中，故障转移测试必须在计划网络维护工作窗口期间完成，因为它可能会造成服务中断。
>

在进行流量切换之前，让我们跟踪设置中的当前路径从本地测试服务器到分支 VNet 中的测试 VM。

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

我们设置的主和辅助 ExpressRoute 点对点连接子网分别为 192.168.11.16/30 和 192.168.11.20/30。 在上面的跟踪路线中，在步骤 3 中，我们看到我们达到 192.168.11.18，这是主 MSEE 的接口 IP。 MSEE 接口的存在证实，正如预期的那样，我们当前的路径位于 ExpressRoute 上。

如[重置 ExpressRoute 电路对等互连中][RST]所报告的那样，让我们使用以下电源壳命令来禁用 ExpressRoute 电路的主对等互连。

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

故障转移开关时间取决于 BGP 收敛时间。 在我们的设置中，故障转移开关需要几秒钟（少于 10）。 切换后，跟踪路径的重复显示以下路径：

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

跟踪路由结果确认通过 S2S VPN 的备份连接处于活动状态，并且如果主和辅助 ExpressRoute 连接都发生故障，则可以提供服务连续性。 要完成故障转移测试，让我们使用以下命令集重新启用 ExpressRoute 连接并规范化流量流。

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

要确认流量已切换回 ExpressRoute，请重复跟踪路由并确保它通过 ExpressRoute 专用对等互连。

## <a name="next-steps"></a>后续步骤

ExpressRoute 专为高可用性而设计，在 Microsoft 网络中没有单点故障。 ExpressRoute 电路仍局限于单个地理区域和服务提供商。 S2S VPN 是快速路由电路的良好灾难恢复被动备份解决方案。 对于可靠的被动备份连接解决方案，定期维护被动配置和定期验证连接非常重要。 至关重要的是，不要让 VPN 配置过时，并且定期（例如每个季度）在维护时段期间重复本文中描述的验证和故障转移测试步骤。

要基于 VPN 网关指标启用监视和警报，请参阅[在 VPN 网关指标上设置警报][VPN-alerts]。

要在快速路由故障后加快 BGP 收敛，[请通过 ExpressRoute 配置 BFD][BFD]。

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "正在考虑的拓扑"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW 配置"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



