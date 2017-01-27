---
title: "虚拟网络 VPN 网关常见问题 | Microsoft Docs"
description: "VPN 网关常见问题。 Microsoft Azure 虚拟网络跨界连接、混合配置连接和 VPN 网关的常见问题。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2dda1cd384cf365504811a260872703f2c5c484e
ms.openlocfilehash: ccb0dc6172b234412558b9175f3872d690d4ea3a


---
# <a name="vpn-gateway-faq"></a>VPN 网关常见问题
## <a name="connecting-to-virtual-networks"></a>连接到虚拟网络
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>能否连接不同 Azure 区域中的虚拟网络？
是的。 事实上，没有任何区域约束。 一个虚拟网络可以连接到同一区域中的其他虚拟网络，也可以连接到其他 Azure 区域中的其他虚拟网络。 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>能否连接不同订阅中的虚拟网络？
是的。

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>能否从一个虚拟网络连接到多个站点？
你可以使用 Windows PowerShell 和 Azure REST API 连接到多个站点。 请参阅 [多站点与 VNet 到 VNet 连接](#multi-site-and-vnet-to-vnet-connectivity) 的“常见问题”部分。

## <a name="what-are-my-cross-premises-connection-options"></a>我的跨界连接选项有哪些？
支持以下跨界连接：

* [站点到站点](vpn-gateway-site-to-site-create.md) – 基于 IPsec（IKE v1 和 IKE v2）的 VPN 连接。 此类型的连接需要 VPN 设备或 RRAS。
* [点到站点](vpn-gateway-point-to-site-create.md) – 基于 SSTP（安全套接字隧道协议）的 VPN 连接。 此连接不需要 VPN 设备。
* [VNet 到 VNet](virtual-networks-configure-vnet-to-vnet-connection.md) - 此类连接与站点到站点配置相同。 VNet 到 VNet 是一种基于 IPsec（IKE v1 和 IKE v2）的 VPN 连接。 它不需要 VPN 设备。
* [多站点](vpn-gateway-multi-site.md) - 这是站点到站点配置的变体，可以让你将多个本地站点连接到虚拟网络。
* [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute 可以从你的 WAN 直接连接到 Azure，不需要通过公共 Internet。 有关详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)和 [ExpressRoute 常见问题](../expressroute/expressroute-faqs.md)。

有关连接的详细信息，请参阅 [关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>站点到站点连接和点到站点连接的区别是什么？
**站点到站点** 连接允许你将任何本地计算机连接到虚拟网络中的任何虚拟机或角色实例，具体取决于你如何选择路由的配置。 它对于需要始终可用的跨界连接来说是一个极佳的选项，很适合混合配置。 此类连接依赖于 IPsec VPN 设备（硬件或软件设备），该设备必须部署在网络边缘。 若要创建此类连接，必须具有必需的 VPN 硬件和面向外部的 IPv4 地址。

**点到站点** 连接允许你从任何位置的单台计算机连接到虚拟网络中的任何内容。 它使用 Windows 内置的 VPN 客户端。 在进行点到站点配置时，你需要安装证书和 VPN 客户端配置包，其中包含的设置允许你的计算机连接到虚拟网络中的任何虚拟机或角色实例。 此连接适用于需要连接到虚拟网络但该虚拟网络不在本地的情况。 当你无法访问 VPN 硬件或面向外部的 IPv4 地址（二者是进行站点到站点连接所必需的）时，它也是一个很好的选项。

你可以将虚拟网络配置为同时使用站点到站点连接和点到站点连接，前提是使用基于路由的 VPN 类型为网关创建站点到站点连接。 在经典部署模型中，基于路由的 VPN 类型称为动态网关。

### <a name="what-is-expressroute"></a>什么是 ExpressRoute？
使用 ExpressRoute，可在 Azure 数据中心与你的本地环境或共同租用环境中的基础结构之间创建专用连接。 使用 ExpressRoute，可以在 ExpressRoute 合作伙伴共同租用的设施中建立与 Azure 的连接，也可以直接从现有的 WAN 网络（如网络服务提供商提供的 MPLS VPN）连接到 Microsoft 云服务（例如 Microsoft Azure 和 Office 365）。

与通过 Internet 的典型连接相比，ExpressRoute 连接提供更好的安全性、更多的可靠性、更高的带宽和更少的延迟。 在某些情况下，使用 ExpressRoute 连接在本地网络和 Azure 之间传输数据还可以产生显著的成本效益。 如果你已创建从本地网络到 Azure 的跨界连接，则可以在不改动虚拟网络的情况下迁移到 ExpressRoute 连接。

有关详细信息，请参阅 [ExpressRoute 常见问题](../expressroute/expressroute-faqs.md) 。

## <a name="site-to-site-connections-and-vpn-devices"></a>站点到站点连接和 VPN 设备
### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>选择 VPN 设备时应考虑什么？
我们在与设备供应商合作的过程中验证了一系列的标准站点到站点 VPN 设备。 可在 [此处](vpn-gateway-about-vpn-devices.md)找到已知兼容的 VPN 设备及其相应的配置说明/示例和设备规范的列表。 设备系列中列为已知兼容设备的所有设备都应适用于虚拟网络。 若要获取配置 VPN 设备的帮助，请参考对应于各设备系列的设备配置示例或链接。

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>如果已知兼容设备的列表中没有我的 VPN 设备，该怎么办？
如果没有看到你的设备列为已知兼容 VPN 设备，但想使用该设备进行 VPN 连接，则需要确认它符合 [此处](vpn-gateway-about-vpn-devices.md)列出的受支持 IPsec/IKE 配置选项和参数。 满足最低要求的设备应该兼容 VPN 网关。 请联系设备制造商，了解更多支持和配置说明。

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>在流量处于空闲状态时，为何我那基于策略的 VPN 隧道会关闭？
对于基于策略（也称为静态路由）的 VPN 网关来说，这是预期的行为。 当经过隧道的流量处于空闲状态 5 分钟以上时，将销毁该隧道。 当流量朝任一方向开始流动时，该隧道将立刻重新建立。

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>我可以使用软件 VPN 连接到 Azure 吗？
我们支持将 Windows Server 2012 路由和远程访问 (RRAS) 服务器用于站点到站点跨界配置。

其他软件 VPN 解决方案只要遵循行业标准 IPsec 实现，就会与我们的网关兼容。 有关配置和支持说明，请与该软件的供应商联系。

## <a name="point-to-site-connections"></a>点到站点连接
### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>点到站点连接允许使用哪些操作系统？
支持以下操作系统：

* Windows 7（32 位和 64 位）
* Windows Server 2008 R2（仅 64 位）
* Windows 8（32 位和 64 位）
* Windows 8.1（32 位和 64 位）
* Windows Server 2012（仅 64 位）
* Windows Server 2012 R2（仅 64 位）
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>能否使用任何支持 SSTP 的点到站点软件 VPN 客户端？
否。 仅支持上面列出的 Windows 操作系统版本。

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>在我的点到站点配置中，可以有多少 VPN 客户端终结点？
我们最多支持 128 个 VPN 客户端可同时连接到一个虚拟网络。

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>能否将我自己的内部 PKI 根 CA 用于点到站点连接？
是的。 以前只可使用自签名根证书。 你仍可上载 20 个根证书。

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>能否使用点到站点功能穿越代理和防火墙？
是的。 我们使用 SSTP（安全套接字隧道协议）作为隧道穿越防火墙。 此隧道将显示为 HTTPS 连接。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>如果重新启动进行过点到站点配置的客户端计算机，是否会自动重新连接 VPN？
默认情况下，客户端计算机将不自动重新建立 VPN 连接。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>点到站点在 VPN 客户端上是否支持自动重新连接和 DDNS？
点到站点 VPN 中当前不支持自动重新连接和 DDNS。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>对于同一虚拟网络，站点到站点和点到站点配置能否共存？
是的。 如果你网关使用 RouteBased VPN 类型，这两种解决方案都可行。 对于经典部署模型，需要一个动态网关。 不支持对静态路由 VPN 网关或使用 `-VpnType PolicyBased` cmdlet 的网关使用点到站点连接。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>能否将点到站点客户端配置为同时连接到多个虚拟网络？
是的，可以这样做。 但虚拟网络的 IP 前缀不得重叠，并且点到站点地址空间在虚拟网络之间不得重叠。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>预计通过站点到站点连接或点到站点连接的吞吐量有多少？
很难维持 VPN 隧道的准确吞吐量。 IPsec 和 SSTP 是重重加密的 VPN 协议。 本地网络与 Internet 之间的延迟和带宽也限制了吞吐量。

## <a name="gateways"></a>网关
### <a name="what-is-a-policy-based-static-routing-gateway"></a>什么是基于策略的（静态路由）网关？
基于策略的网关实施基于策略的 VPN。 基于策略的 VPN 会根据本地网络和 Azure VNet 之间的地址前缀的各种组合，加密数据包并引导其通过 IPsec 隧道。 通常会在 VPN 配置中将策略（或流量选择器）定义为访问列表。

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>什么是基于路由的（动态路由）网关？
基于路由的网关可实施基于路由的 VPN。 基于路由的 VPN 使用 IP 转发或路由表中的“路由”将数据包引导到相应的隧道接口中。 然后，隧道接口会加密或解密出入隧道的数据包。 基于路由的 VPN 的策略或流量选择器配置为任意到任意（或通配符）。

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>能否先获得 VPN 网关 IP 地址，然后再创建网关？
否。 必须先创建网关，然后才能获得 IP 地址。 如果删除再重新创建 VPN 网关，IP 地址将更改。

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>VPN 隧道如何进行身份验证？
Azure VPN 使用 PSK（预共享密钥）身份验证。 我们在创建 VPN 网关时生成一个预共享密钥 (PSK)。 你可以使用设置预共享密钥 PowerShell cmdlet 或 REST API 将自动生成的 PSK 更改成你自己的 PSK。

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>能否使用“设置预共享密钥 API”来配置基于策略的（静态路由）网关 VPN？
可以，“设置预共享密钥 API”和 PowerShell cmdlet 可用于配置 Azure 基于策略的（静态）VPN 和基于路由的（动态）路由 VPN。

### <a name="can-i-use-other-authentication-options"></a>能否使用其他身份验证选项？
我们仅限使用预共享密钥 (PSK) 进行身份验证。

### <a name="what-is-the-gatewaysubnet-and-why-is-it-needed"></a>什么是“GatewaySubnet”？为何需要它？
我们有一个网关服务，运行它即可启用跨界连接。

若要配置 VPN 网关，需要为 VNet 创建网关子网。 所有网关子网都必须命名为 GatewaySubnet 才能正常工作。 不要对网关子网使用其他名称。 此外，不要在网关子网中部署 VM 或其他组件。

网关子网的最小大小完全取决于你要创建的配置。 尽管某些配置的网关子网最小可以创建为 /29，但建议创建 /28 或更大（/28、/27、/26 等）的网关子网。

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>是否可以将虚拟机或角色实例部署到网关子网？
否。

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>如何指定通过 VPN 网关的流量？

####<a name="resource-manager-deployment-model"></a>Resource Manager 部署模型
* PowerShell：使用“AddressPrefix”指定本地网络网关的流量。
* Azure 门户：导航到“本地网络网关”>“配置”>“地址空间”。

####<a name="classic-deployment-model"></a>经典部署模型
* Azure 门户：导航到“经典虚拟网络”>“VPN 连接”>“站点到站点 VPN 连接”>“本地站点名称”>“本地站点”>“客户端地址空间”。 
* 经典门户：在“本地网络”下的“网络”页上为虚拟网络添加要通过网关发送的每个范围。 

### <a name="can-i-configure-forced-tunneling"></a>能否配置强制隧道？
是的。 请参阅 [配置强制隧道](vpn-gateway-about-forced-tunneling.md)。

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>能否在 Azure 中设置自己的 VPN 服务器，然后使用该服务器连接到本地网络？
能。你可以在 Azure 中部署你自己的 VPN 网关或服务器，可以从 Azure 应用商店部署，也可以通过创建你自己的 VPN 路由器来部署。 需要在虚拟网络中配置用户定义的路由，确保流量在本地网络和虚拟网络子网之间正确路由。

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>我的 VPN 网关上的某些端口为何处于打开状态？
这些端口是进行 Azure 基础结构通信所必需的。 它们受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体（包括这些网关的客户）将无法对这些终结点施加任何影响。

VPN 网关基本上是一个多宿主设备，其中一个 NIC 进入客户专用网络，另一个 NIC 面向公共网络。 因合规性原因，Azure 基础结构实体无法进入客户专用网络，因此需利用公共终结点进行基础结构通信。 Azure 安全审核会定期扫描公共终结点。

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>有关网关类型、要求和吞吐量的详细信息
有关详细信息，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md)。

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>多站点与 VNet 到 VNet 连接
### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>哪种类型的网关可以支持多站点连接和 VNet 到 VNet 连接？
仅限基于路由的（动态路由）VPN。

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>是否可以将 RouteBased VPN 类型的 VNet 连接到另一个 PolicyBased VPN 类型的 VNet？
不能，两个虚拟网络都必须使用基于路由的（动态路由）VPN。

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>VNet 到 VNet 通信安全吗？
安全，它通过 IPsec/IKE 加密进行保护。

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>VNet 到 VNet 流量是否会流经 Azure 主干？
是的，此流量遍历 Azure 主干。 它不会通过 Internet。

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>一个虚拟网络可以连接到多少个本地站点和虚拟网络？
最大 基本和标准动态路由网关合起来最多 10 个；高性能 VPN 网关最多 30 个。

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>能否将点到站点 VPN 用于具有多个 VPN 隧道的虚拟网络？
能，可以将点到站点 (P2S) VPN 用于连接到多个本地站点的 VPN 网关和其他虚拟网络。

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>能否使用多站点 VPN 在我的虚拟网络和本地站点之间配置多个隧道？
是，但必须在两个隧道上配置与同一位置之间的 BGP。

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>连接的虚拟网络与内部本地站点之间能否存在重叠的地址空间？
否。 重叠的地址空间将导致网络配置文件上载或“创建虚拟网络”失败。

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>使用更多站点到站点 VPN 我会为单个虚拟网络获取更多带宽吗？
不会，所有 VPN 隧道（包括点到站点 VPN）共享同一 Azure VPN 网关和可用带宽。

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>能否使用 Azure VPN 网关在我的本地站点之间传输流量或将流量传输到其他虚拟网络？

####<a name="resource-manager-deployment-model"></a>Resource Manager 部署模型
是的。 相关详细信息，请参阅 [BGP](#bgp) 部分。

####<a name="classic-deployment-model"></a>经典部署模型
使用经典部署模型通过 Azure VPN 网关传输流量是可行的，但需要依赖网络配置文件中静态定义的地址空间。 使用经典部署模型的 Azure 虚拟网络和 VPN 网关尚不支持 BGP。 没有 BGP，手动定义传输地址空间很容易出错，不建议这样做。<br>

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure 会为同一虚拟网络的所有 VPN 连接生成同一 IPsec/IKE 预共享密钥吗？
不会。默认情况下，Azure 会为不同 VPN 连接生成不同的预共享密钥。 但是，你可以使用设置 VPN 网关密钥 REST API 或 PowerShell cmdlet 设置你想要的密钥值。 该密钥必须是长度介于 1 到 128 个字符之间的字母数字字符串。

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure 会对虚拟网络之间的流量收费吗？
对于不同 Azure 虚拟网络之间的流量，Azure 只对从一个 Azure 区域跨越到另一个 Azure 区域的流量收费。 费率列在 Azure [VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway/) 页。

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>能否将使用 IPsec VPN 的虚拟网络连接到我的 ExpressRoute 线路？
能，系统支持该操作。 有关详细信息，请参阅 [配置可共存的 ExpressRoute 连接和站点到站点 VPN 连接](../expressroute/expressroute-howto-coexist-classic.md)。

## <a name="a-namebgpabgp"></a><a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a>跨界连接和 VM
### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>如果虚拟机位于虚拟网络中，而连接是跨界连接，应如何连接到该 VM？
有几个选择。 如果你启用了 RDP 并且创建了终结点，则可以使用 VIP 连接到虚拟机。 在这种情况下，你需要指定要连接到的 VIP 和端口。 你需要配置用于流量的虚拟机端口。 通常，你会转到 Azure 经典门户，并保存与你的计算机建立的 RDP 连接的设置。 这些设置包含必要的连接信息。

如果你有一个配置了跨界连接的虚拟网络，则可以通过使用内部 DIP 或专用 IP 地址连接到你的虚拟机。 你也可以使用位于同一虚拟网络中的另一个虚拟机的内部 DIP 连接到你的虚拟机。 如果要从虚拟网络外部的位置进行连接，则无法使用 DIP RDP 到你的虚拟机。 例如，如果你配置了点到站点虚拟网络，并且未从计算机建立连接，则无法通过 DIP 连接到虚拟机。

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>如果我的虚拟机位于使用跨界连接的虚拟网络中，从我的 VM 流出的所有流量是否都会经过该连接？
否。 只有其目标 IP 包含在指定虚拟网络本地网络 IP 地址范围内的流量才会通过虚拟网络网关。 流量有一个目标 IP 位于虚拟网络中，并将保留在虚拟网络中。 其他流量通过负载平衡器发送到公共网络，或者在使用强制隧道的情况下通过 Azure VPN 网关发送。 如果你要进行故障排除，则重要的是确保列出本地网络中你要通过网关发送的所有范围。 确保本地网络地址范围没有与虚拟网络中的任何地址范围重叠。 另外，还需确保所使用的 DNS 服务器会将名称解析成正确的 IP 地址。

## <a name="virtual-network-faq"></a>虚拟网络常见问题解答
请在 [虚拟网络常见问题](../virtual-network/virtual-networks-faq.md)中查看更多虚拟网络信息。



<!--HONumber=Jan17_HO2-->


