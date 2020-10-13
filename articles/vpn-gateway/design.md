---
title: 关于 Azure VPN 网关设计
description: 了解如何设计用于连接到 Azure 虚拟网络的 VPN 网关拓扑。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 61732f66aef58f5a9edcb9e095782e19e8aaffdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397209"
---
# <a name="vpn-gateway-design"></a>VPN 网关设计

必须知道，VPN 网关连接可以使用不同的配置。 必须确定哪种配置最适合自己的需要。 在以下部分，可以查看有关下述 VPN 网关连接的设计信息和拓扑示意图。 使用图示和描述来帮助选择符合要求的连接拓扑。 这些示意图显示了主要的基准拓扑。但是，你也可以使用这些示意图作为指导来构建更复杂的配置。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>站点到站点和多站点（IPsec/IKE VPN 隧道）

### <a name="site-to-site"></a><a name="S2S"></a>站点到站点

站点到站点 (S2S) VPN 网关连接是通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道建立的连接。 S2S 连接可用于跨界和混合配置。 S2S 连接要求位于本地的 VPN 设备分配有一个公共 IP 地址。 若要了解如何选择 VPN 设备，请参阅 [VPN 网关常见问题解答 - VPN 设备](vpn-gateway-vpn-faq.md#s2s)。

![Azure VPN 网关站点到站点连接示例](./media/design/vpngateway-site-to-site-connection-diagram.png)

VPN 网关可使用一个公共 IP 以主动-备用模式配置，也可使用两个公共 IP 以主动-主动模式配置。 在主动-备用模式下，一个 IPsec 隧道处于活动状态，另一个处于备用状态。 在此设置中，流量流经活动隧道，如果此隧道出现问题，则流量将切换到备用隧道。 建议在主动-主动模式下设置 VPN 网关，此时两个 IPsec 隧道都处于活动状态，数据同时流经这两个隧道。 主动-主动模式的另一优点是，客户可处理更高的吞吐量。

### <a name="multi-site"></a><a name="Multi"></a>多站点

此类型的连接是站点到站点连接的变体。 从虚拟网络网关创建多个 VPN 连接，通常情况下连接到多个本地站点。 使用多个连接时，必须使用 RouteBased VPN 类型（使用经典 VNet 时称为动态网关）。 由于每个虚拟网络只能有一个 VPN 网关，因此通过该网关的所有连接都共享可用带宽。 此类连接通常称为“多站点”连接。

![Azure VPN 网关多站点连接示例](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>适用于站点到站点和多站点的部署模型和方法

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>点到站点 VPN

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 对于要从远程位置（例如从家里或会议室）连接到 Azure VNet 的远程工作者，此解决方案很有用。 如果只有一些客户端需要连接到 VNet，则还可以使用 P2S VPN 这一解决方案来代替 S2S VPN。

与 S2S 连接不同，P2S 连接不需要本地面向公众的 IP 地址或 VPN 设备。 可以通过同一 VPN 网关将 P2S 连接与 S2S 连接结合使用，前提是这两种连接的所有配置要求都兼容。 有关点到站点连接的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。

![Azure VPN 网关点到站点连接示例](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>适用于 P2S 的部署模型和方法

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>VNet 到 VNet 连接（IPsec/IKE VPN 隧道）

将一个虚拟网络连接到另一个虚拟网络（VNet 到 VNet）类似于将 VNet 连接到本地站点位置。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道。 甚至可以将 VNet 到 VNet 通信与多站点连接配置结合使用。 这样，便可以建立将跨界连接与虚拟网络间连接相结合的网络拓扑。

连接的 VNet 可以：

* 在相同或不同的区域中
* 在相同或不同订阅中 
* 在相同或不同部署模型中

![Azure VPN 网关 VNet 到 VNet 连接示例](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>部署模型之间的连接

Azure 当前具有两个部署模型：经典模型和 Resource Manager 模型。 如果 Azure 已经使用了一段时间，则 Azure VM 和实例角色可能是在经典 VNet 上运行。 而较新的 VM 和角色实例可能是在 Resource Manager 中创建的 VNet 上运行。 可以在 Vnet 之间创建连接，使其中一个 VNet 中的资源能够直接与另一个 VNet 中的资源通信。

### <a name="vnet-peering"></a>VNet 对等互连

只要虚拟网络符合特定要求，就能使用 VNet 对等互连来创建连接。 VNet 对等互连不使用虚拟网络网关。 有关详细信息，请参阅 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>适用于 VNet 到 VNet 的部署模型和方法

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute（专用连接）

使用 ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 通过 ExpressRoute，你可以与 Microsoft 云服务建立连接，例如 Microsoft Azure、Microsoft 365 和 CRM Online。 可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在场地租用设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。

ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。

ExpressRoute 连接使用虚拟网关作为其所需配置的一部分。 在 ExpressRoute 连接中，虚拟网络网关的网关类型配置为“ExpressRoute”而不是“Vpn”。 虽然经 ExpressRoute 线路传送的流量默认不加密，但可以创建一项解决方案，通过 ExpressRoute 线路来发送加密的流量。 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>站点到站点和 ExpressRoute 共存连接

ExpressRoute 是从 WAN （不通过公共 Internet）到 Microsoft 服务（包括 Azure）的直接专用连接。 站点到站点 VPN 流量以加密方式通过公共 Internet 传输。 能够为同一个虚拟网络配置站点到站点 VPN 和 ExpressRoute 连接可带来诸多好处。

可以将站点到站点 VPN 配置为 ExpressRoute 的安全故障转移路径，或者使用站点到站点 VPN 连接到不属于网络但却已通过 ExpressRoute 进行连接的站点。 请注意，对于同一虚拟网络，此配置需要两个虚拟网络网关，一个使用“Vpn”网关类型，另一个使用“ExpressRoute”网关类型。

![ExpressRoute 和 VPN 网关共存连接示例](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>适用于 S2S 和 ExpressRoute 的部署模型和方法共存

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>高可用连接

有关高可用连接的规划和设计，请参阅[高可用连接](vpn-gateway-highlyavailable.md)。

## <a name="next-steps"></a>后续步骤

* 有关更多信息，请查看 [VPN 网关常见问题](vpn-gateway-vpn-faq.md)。

* 详细了解 [VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。

* 有关 VPN 网关 BGP 的注意事项，请参阅[关于 BGP](vpn-gateway-bgp-overview.md)。

* 查看[订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。

* 了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。
