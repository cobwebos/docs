---
title: ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 配置详细信息：Azure 后端连接功能的互操作性 | Microsoft Docs
description: 本页提供用于分析 ExpressRoute、站点到站点 VPN 和 VNet 对等互连功能互操作性的测试设置的配置详细信息。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946946"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 测试配置详细信息

本文介绍测试设置的配置详细信息。 若要了解测试设置，请参阅[测试设置][Setup]。 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

以下 Azure 门户屏幕截图显示辐射 VNet 的 VNet 对等互连详细信息。 有关在两个虚拟网络之间配置 VNet 对等互连的分步指导，请参阅[管理 VNet 对等互连][VNet-Config]。 如果希望辐射 VNet 使用连接到中心 VNet 的网关，需要选中“使用远程网关”。

[![1]][1]

以下 Azure 门户屏幕截图显示中心 VNet 的 VNet 对等互连详细信息。 如果希望中心 VNet 允许辐射 VNet 使用其网关，需要选中“使用远程网关”。

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

中心和分支 VNet 之间的站点到站点 VPN 连接是使用 VPN 网关配置的。 默认情况下，VPN 和 ExpressRoute 网关上配置了专用 ASN 值 65515。 VPN 网关允许更改 ASN 值。 如以下 Azure 门户屏幕截图中所示，在测试设置中，分支 VNet VPN 网关的 ASN 值已更改为 65516，这可以在中心与分支 VNet 之间实现 eBGP 路由。


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>使用 ExpressRoute 和站点到站点 VPN 的位置 1 本地连接

###<a name="expressroute1-configuration-details"></a>ExpressRoute1 配置详细信息

以下门户屏幕截图显示位置 1 本地 CE 路由器的 Azure 区域 1 ExpressRoute 线路配置。

[![4]][4]

以下门户屏幕截图显示 ExpressRoute1 线路与中心 VNet 之间的连接配置。

[![5]][5]

以下配置是与 ExpressRoute 专用对等互连相关的主要 CE 路由器（在测试设置中，Cisco ASR1000 路由器用作 CE 路由器）配置列表。 当站点到站点 VPN 和 ExpressRoute 线路同时配置为将某个本地网络连接到 Azure 时，默认情况下，Azure 会优先使用 ExpressRoute 线路。 为了避免非对称路由，本地网络也应该优先使用 ExpressRoute 而不是站点到站点 VPN 来处理通过 ExpressRoute 和站点到站点 VPN 收到的路由。 可在以下配置中使用 BGP 本地优先顺序属性来实现此目的。 

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

###<a name="site-to-site-vpn-configuration-details"></a>站点到站点 VPN 配置详细信息

下面是与站点到站点 VPN 连接相关的主要 CE 路由器配置列表：

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>使用 ExpressRoute 的位置 2 本地连接

与本地位置 2 比较靠近的第二个 ExpressRoute 线路将本地位置 2 连接到中心 VNet。 以下门户屏幕截图显示第二个 ExpressRoute 配置。

[![6]][6]

以下门户屏幕截图显示第二个 ExpressRoute 线路与中心 VNet 之间的连接配置。

[![7]][7]

ExpressRoute1 将中心 VNet 和本地位置 1 连接到不同 Azure 区域中的远程 VNet。

[![8]][8]

## <a name="further-reading"></a>延伸阅读

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>串联使用 ExpressRoute 和站点到站点 VPN 连接

#### <a name="site-to-site-vpn-over-expressroute"></a>基于 ExpressRoute 的站点到站点 VPN

可以基于 ExpressRoute Microsoft 对等互连配置站点到站点 VPN，专门用于在本地网络与 Azure VNet 之间交换数据，同时确保保密性、防重播能力、真实性和完整性。 有关如何基于 ExpressRoute Microsoft 对等互连以隧道模式配置站点到站点 IPSec VPN 的详细信息，请参阅[基于 ExpressRoute Microsoft 对等互连的站点到站点 VPN][S2S-Over-ExR]。 

基于 Microsoft 对等互连配置 S2S VPN 的主要限制是吞吐量。 基于 IPSec 隧道的吞吐量受限于 VPN 网关容量。 与 ExpressRoute 吞吐量相比，VPN 网关吞吐量更低。 在这种情况下，对高安全性流量使用 IPSec 隧道，并对其他所有流量使用专用对等互连，将有助于优化 ExpressRoute 带宽利用率。

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>将站点到站点 VPN 用作 ExpressRoute 的安全故障转移路径
ExpressRoute 以冗余线路对的形式提供，以确保高可用性。 可在不同的 Azure 区域配置异地冗余的 ExpressRoute 连接。 如同在测试设置中所做的那样，在给定的 Azure 区域中，若要对 ExpressRoute 连接使用故障转移路径，可以使用站点到站点 VPN 实现此目的。 通过 ExpressRoute 和 S2S VPN 播发相同的前缀时，Azure 会优先使用 ExpressRoute 而不是 S2S VPN。 为了避免 ExpressRoute 与 S2S VPN 之间的非对称路由，本地网络配置同样应该优先使用 ExpressRoute 而不是 S2S VPN 连接。

有关如何配置 ExpressRoute 和站点到站点 VPN 共存连接的详细信息，请参阅 [ExpressRoute 和站点到站点共存][ExR-S2S-CoEx]。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>将后端连接扩展到辐射 VNet 和分支位置

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

中心辐射型 VNet 体系结构的使用非常广泛。 中心是 Azure 中的一个虚拟网络 (VNet)，充当辐射 VNet 与本地网络之间的连接中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 连接在本地数据中心与中心之间流动。 有关体系结构的详细信息，请参阅[中心辐射型体系结构][Hub-n-Spoke]

区域中的 VNet 对等互连可让辐射 VNet 使用中心 VNet 网关（VPN 和 ExpressRoute 网关）来与远程网络通信。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

如果希望分支 VNet（在不同区域中）和本地网络通过中心 VNet 相互通信，原生的 Azure 解决方案是使用 VPN 建立站点到站点 VPN 连接。 替代做法是对中心内部的路由使用 NVA。

有关如何配置 VPN 网关，请参阅[配置 VPN 网关][VPN]。 有关如何部署可用性 NVA，请参阅[部署高可用性 NVA][Deploy-NVA]。

## <a name="next-steps"></a>后续步骤

有关测试设置的控制平面分析以及拓扑的不同 VNet/VLAN 视图，请参阅[控制平面分析][Control-Analysis]。

有关测试设置的数据平面分析以及 Azure 网络监视功能视图，请参阅[数据平面分析][Data-Analysis]。

若要了解可将多少个 ExpressRoute 线路连接到一个 ExpressRoute 网关，或者可将多少个 ExpressRoute 网关连接到一个 ExpressRoute 线路，或者要了解 ExpressRoute 的其他缩放限制，请参阅 [ExpressRoute 常见问题解答][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "辐射 VNet 的 VNet 对等互连"
[2]: ./media/backend-interoperability/HubVNet-peering.png "中心 VNet 的 VNet 对等互连"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "分支 VNet 的 VPN 网关配置"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute1 配置"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "ExpressRoute1 到中心 VNet ExR 网关的连接配置"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute2 配置"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "ExpressRoute2 到中心 VNet ExR 网关的连接配置"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "ExpressRoute2 到远程 VNet ExR 网关的连接配置"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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




