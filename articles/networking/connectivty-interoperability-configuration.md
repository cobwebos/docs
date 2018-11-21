---
title: Azure 后端连接功能中的互操作性：配置详细信息 | Microsoft Docs
description: 本文介绍可用于分析 ExpressRoute、站点到站点 VPN 与 Azure 中虚拟网络对等互连之间的互操作性的测试设置的配置详细信息。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: a460a8f4c652182a7916cba5aef0520834432909
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614804"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Azure 后端连接功能中的互操作性：测试配置详细信息

本文介绍[测试设置][Setup]的配置详细信息。 测试设置可帮助你分析 Azure 网络服务如何在控制平面级别和数据平面级别进行互操作。

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

下图显示了辐射虚拟网络 (VNet) 的 Azure 虚拟网络对等互连详细信息。 若要了解如何在两个 VNet 之间设置对等互连，请参阅[管理 VNet 对等互连][VNet-Config]。 如果希望辐射 VNet 使用连接到中心 VNet 的网关，请选择“使用远程网关”。

[![1]][1]

下图显示了中心 VNet 的 VNet 对等互连详细信息。 如果希望辐射 VNet 使用中心 VNet 网关，请选择“使用远程网关”。

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

使用 Azure VPN 网关中的 VPN 网关在中心与分支 VNet 之间建立站点到站点 VPN 连接。 默认情况下，VPN 网关和 Azure ExpressRoute 网关使用专用自治系统编号 (ASN) 值 **65515**。 可在 VPN 网关中更改 ASN 值。 在测试设置中，分支 VNet VPN 网关的 ASN 值将更改为 **65516**，以支持中心与分支 VNet 之间的 eBGP 路由。


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>使用 ExpressRoute 和站点到站点 VPN 的本地位置 1 连接

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1 配置详细信息

下图显示了本地位置 1 客户边缘 (CE) 路由器的 Azure 区域 1 ExpressRoute 线路配置：

[![4]][4]

下图显示了 ExpressRoute 1 线路与中心 VNet 之间的连接配置：

[![5]][5]

以下列表显示了 ExpressRoute 专用对等互连的主要 CE 路由器配置。 （Cisco ASR1000 路由器用作测试设置中的 CE 路由器。）当站点到站点 VPN 和 ExpressRoute 线路同时配置为将某个本地网络连接到 Azure 时，默认情况下，Azure 会优先使用 ExpressRoute 线路。 为了避免非对称路由，本地网络也应该优先使用 ExpressRoute 连接而不是站点到站点 VPN 连接。 以下配置使用 BGP **local-preference** 属性来建立优先级：

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

### <a name="site-to-site-vpn-configuration-details"></a>站点到站点 VPN 配置详细信息

以下列表显示了站点到站点 VPN 连接的主要 CE 路由器配置：

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

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>使用 ExpressRoute 的本地位置 2 连接

与本地位置 2 比较靠近的另一条 ExpressRoute 线路将本地位置 2 连接到中心 VNet。 下图显示了第二个 ExpressRoute 配置：

[![6]][6]

下图显示了第二条 ExpressRoute 线路与中心 VNet 之间的连接配置：

[![7]][7]

ExpressRoute 1 将中心 VNet 和本地位置 1 连接到不同 Azure 区域中的远程 VNet：

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>串联 ExpressRoute 和站点到站点 VPN 连接

###  <a name="site-to-site-vpn-over-expressroute"></a>基于 ExpressRoute 的站点到站点 VPN

可以使用 ExpressRoute Microsoft 对等互连配置站点到站点 VPN，在本地网络与 Azure VNet 之间以私密方式交换数据。 使用此配置可以在确保保密性、真实性和完整性的基础上交换数据。 这种数据交换还可以防重播。 有关如何使用 ExpressRoute Microsoft 对等互连在隧道中配置站点到站点 IPsec VPN 的详细信息，请参阅[基于 ExpressRoute Microsoft 对等互连的站点到站点 VPN][S2S-Over-ExR]。 

配置使用 Microsoft 对等互连的站点到站点 VPN 的主要限制是吞吐量。 基于 IPsec 隧道的吞吐量受限于 VPN 网关容量。 VPN 网关吞吐量低于 ExpressRoute 吞吐量。 在这种情况下，对高安全性流量使用 IPsec 隧道，并对其他所有流量使用专用对等互连，将有助于优化 ExpressRoute 带宽利用率。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>将站点到站点 VPN 用作 ExpressRoute 的安全故障转移路径

ExpressRoute 充当冗余的线路对，可确保高可用性。 可在不同的 Azure 区域配置异地冗余的 ExpressRoute 连接。 另外，如测试设置中所示，在 Azure 区域中，可以使用站点到站点 VPN 为 ExpressRoute 连接创建故障转移路径。 通过 ExpressRoute 和站点到站点 VPN 播发相同的前缀时，Azure 会优先使用 ExpressRoute。 为了避免 ExpressRoute 与站点到站点 VPN 之间的非对称路由，本地网络配置同样应该优先使用 ExpressRoute 连接，然后再使用站点到站点 VPN 连接。

有关如何配置 ExpressRoute 和站点到站点 VPN 共存连接的详细信息，请参阅 [ExpressRoute 和站点到站点共存][ExR-S2S-CoEx]。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>将后端连接扩展到辐射 VNet 和分支位置

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

中心辐射型 VNet 体系结构的使用非常广泛。 中心是 Azure 中的一个 VNet，充当辐射 VNet 与本地网络之间的连接中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 连接在本地数据中心与中心之间流动。 有关体系结构的详细信息，请参阅[在 Azure 中实现中心辐射型网络拓扑][Hub-n-Spoke]。

在区域内的 VNet 对等互连中，辐射 VNet 可以使用中心 VNet 网关（VPN 和 ExpressRoute 网关）来与远程网络通信。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

你可能希望位于不同区域中的分支 VNet 和本地网络通过中心 VNet 相互通信。 此配置的原生 Azure 解决方案是使用 VPN 建立站点到站点 VPN 连接。 替代方案是对中心内部的路由使用网络虚拟设备 (NVA)。

有关详细信息，请参阅[什么是 VPN 网关？][VPN]和[部署高度可用的 NVA][Deploy-NVA]。

## <a name="next-steps"></a>后续步骤

了解测试设置的[控制平面分析][Control-Analysis]，以及拓扑中不同 VNet 或 VLAN 的视图。

了解测试设置的[数据平面分析][Data-Analysis]，以及 Azure 网络监视功能视图。

参阅 [ExpressRoute 常见问题解答][ExR-FAQ]，了解：
-   可将多少条 ExpressRoute 线路连接到一个 ExpressRoute 网关。
-   可将多少个 ExpressRoute 网关连接到一条 ExpressRoute 线路。
-   ExpressRoute 的其他缩放限制。


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "辐射 VNet 的 VNet 对等互连"
[2]: ./media/backend-interoperability/HubVNet-peering.png "中心 VNet 的 VNet 对等互连"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "分支 VNet 的 VPN 网关配置"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 配置"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "ExpressRoute 1 到中心 VNet ExR 网关的连接配置"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 配置"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "ExpressRoute 2 到中心 VNet ExR 网关的连接配置"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "ExpressRoute 2 到远程 VNet ExR 网关的连接配置"

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


