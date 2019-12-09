---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b65cf26bcea628f784eb086d1b9c88febade25f6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829178"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 虚拟网络网关（VPN 网关）和 Azure 虚拟 WAN VPN 网关之间有什么区别？

虚拟 WAN 提供大规模站点到站点连接，在设计上考虑到了吞吐量、可伸缩性和易用性。 将站点连接到虚拟 WAN VPN 网关时，它不同于使用网关类型 “VPN”的常规虚拟网络网关。 同样，将 ExpressRoute 线路连接到虚拟 WAN 中心时，它对 ExpressRoute 网关使用的资源与对使用“ExpressRoute”网关类型的常规虚拟网络网关使用的资源不同。 对于 VPN 和 ExpressRoute，虚拟 WAN 最多支持 20 Gbps 聚合吞吐量。 虚拟 WAN 还实现了与 CPE 分支设备合作伙伴生态系统的连接自动化。 CPE 分支设备具有自动预配并连接到 Azure 虚拟 WAN 的内置自动化。 这些设备由一个不断扩张的 SD-WAN 和 VPN 合作伙伴生态系统提供。 请参阅[首选合作伙伴列表](../articles/virtual-wan/virtual-wan-locations-partners.md)。

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>虚拟 WAN 与 Azure 虚拟网络网关有何不同？

虚拟网络网关 VPN 限制为 30 个隧道。 对于连接，应当为大型 VPN 使用虚拟 WAN。 每个区域（虚拟中心）最多可以连接 1,000 个分支连接，每个中心有 20 Gbps 聚合。 连接是从本地 VPN 设备到虚拟中心的主动-主动隧道。 每个区域中可以有一个中心，这意味着你可以跨中心连接到 1,000 多个分支。

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>支持哪些设备提供商（虚拟 WAN 合作伙伴）？

目前，许多合作伙伴都支持全自动虚拟 WAN 体验。 有关详细信息，请参阅[虚拟 WAN 合作伙伴](../articles/virtual-wan/virtual-wan-locations-partners.md)。 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>虚拟 WAN 合作伙伴自动化步骤有哪些？

有关合作伙伴自动化步骤，请参阅[虚拟 WAN 合作伙伴自动化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>是否需要使用首选的合作伙伴设备？

不是。 可以使用任何支持 VPN 且符合 Azure 对 IKEv2/IKEv1 IPsec 的支持要求的设备。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>虚拟 WAN 合作伙伴如何自动与 Azure 虚拟 WAN 建立连接？

软件定义的连接解决方案通常使用控制器或设备预配中心来管理其分支设备。 控制器可以使用 Azure API 自动与 Azure 虚拟 WAN 建立连接。 自动化包括上传分支信息、下载 Azure 配置、将 IPSec 隧道设置到 Azure 虚拟中心，以及自动设置从分支设备到 Azure 虚拟 WAN 的连接。 当有数百个分支时，可以轻松使用虚拟 WAN CPE 合作伙伴进行连接，因为载入体验无需设置、配置和管理大规模的 IPsec 连接。 有关详细信息，请参阅[虚拟 WAN 合作伙伴自动化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>是否需要使用首选的合作伙伴设备？

不是。 可以使用任何支持 VPN 且符合 Azure 对 IKEv2/IKEv1 IPsec 的支持要求的设备。

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>虚拟 WAN 如何支持 SD-WAN 设备？

虚拟 WAN 合作伙伴自动执行 Azure VPN 端点的 IPsec 连接。 如果虚拟 WAN 合作伙伴是 SD-WAN 提供商，则表示 SD-WAN 控制器管理到 Azure VPN 端点的自动化和 IPsec 连接。 如果 SD-WAN 设备需要自己的端点而不是 Azure VPN 来实现任何专有 SD-WAN 功能，则可以在 Azure VNet 中部署 SD-WAN 端点并与 Azure 虚拟 WAN 共存。

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>虚拟 WAN 是否会更改任何现有的连接功能？

不会对现有的 Azure 连接功能进行任何更改。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>虚拟 WAN 是否有新的可用资源管理器资源？
  
是的，虚拟 WAN 引入了新的资源管理器资源。 有关详细信息，请参阅[概述](../articles/virtual-wan/virtual-wan-about.md)。

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>允许多少 VPN 设备连接到单个中心？

每个虚拟中心最多支持 1,000 个连接。 每个连接包括四条链路，每条链路连接支持采用主动-主动配置的两个隧道。 隧道在 Azure 虚拟中心 vpngateway 中终止。

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>本地 VPN 设备是否可以连接到多个中心？

是的。 开始时的流量流将从本地设备发送到最近的 Microsoft 网络边缘，然后才发送到虚拟中心。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>是否可以在 Azure 虚拟 WAN 中部署和使用我偏爱的网络虚拟设备（在 NVA VNet 中）？

是的，可以将你偏爱的网络虚拟设备 (NVA) VNet 连接到 Azure Virtual WAN。 首先，使用中心虚拟网络连接将网络虚拟设备 VNet 连接到中心。 然后，使用指向虚拟设备的下一跃点创建一个虚拟中心路由。 可以将多个路由应用于虚拟中心路由表。 此外，连接到 NVA VNet 的任何辐射还必须连接到虚拟中心，以确保辐射 VNet 路由传播到本地系统。

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>可以在虚拟中心内创建网络虚拟设备吗？

无法在虚拟中心内部署网络虚拟设备 (NVA)。 但是，可以在连接到虚拟中心的辐射 VNet 中创建它，并允许中心内的路由通过 (NIC) NVA IP 地址定向目标 VNet 的流量。

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>辐射 VNet 是否可以包含虚拟网络网关？

不是。 如果辐射 VNet 已连接到虚拟中心，则不能包含虚拟网络网关。

### <a name="is-there-support-for-bgp"></a>是否支持 BGP？

是的，支持 BGP。 创建 VPN 站点时，可以在其中提供 BGP 参数。 这表示在 Azure 中为该站点创建的任何连接都将启用 BGP。 此外，如果 VNet 具有 NVA 且此 NVA VNet 已附加到虚拟 WAN 中心，为了确保适当地公布来自 NVA VNet 的路由，附加到 NVA VNet 的辐射必须禁用 BGP。 此外，请将这些辐射 VNet 连接到虚拟中心 VNet，以确保辐射 VNet 路由传播到本地系统。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>是否可以在虚拟中心使用 UDR 定向流量？

是的，可以使用虚拟中心路由表将流量定向到 VNet。 这样，可以通过特定的 IP 地址（通常为 NVA NIC）在 Azure 中设置目标 Vnet 的路由。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>虚拟 WAN 是否有任何许可或定价信息？

是的。 请参阅[定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页面。

### <a name="how-do-i-calculate-price-of-a-hub"></a>如何计算中心的价格？

* 根据使用的中心服务付费。 例如，你有 10 个需要连接到 Azure 虚拟 WAN 的分支或本地设备表示连接到中心的 VPN 端点。 假设这是 1 个缩放单元的 VPN，为 500 Mbps，费用为 0.361 美元/小时。 每个连接的费用为 0.05 美元/小时。 10 个连接的每小时服务总费用为 0.361 + 0.5 美元/小时。 离开 Azure 的数据流量也将收费。

* 还有其他的中心费用。 请参阅[定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页面。

* 如果由于 ExpressRoute 线路连接到虚拟中心而拥有 ExpressRoute 网关，则需支付缩放单元价格。 ER 中的每个缩放单元都是 2 Gbps，每个连接单元按与 VPN 连接单元相同的速率进行收费。

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>没有在启动合作伙伴列表中列出的新合作伙伴如何加入？

所有虚拟 WAN API 都是开放式 API。 可以重新查看文档，评估技术可行性。 如有任何问题，请向 azurevirtualwan@microsoft.com 发送电子邮件。 理想的合作伙伴具有可以预配 IKEv1 或 IKEv2 IPsec 连接的设备。

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>如果使用的设备不在虚拟 WAN 合作伙伴列表中，该怎么办？ 还可以用它来连接到 Azure 虚拟 WAN VPN 吗？

是的，只要设备支持 IPsec IKEv1 或 IKEv2 即可。 虚拟 WAN 合作伙伴自动执行设备到 Azure VPN 端点的连接。 这表示自动执行“分支信息上传”、“IPsec 和配置”以及“连接”等步骤。由于设备不是来自虚拟 WAN 合作伙伴生态系统，因此需要大量手动执行 Azure 配置和更新设备才能建立 IPsec 连接。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>是否可以使用资源管理器模板构造 Azure 虚拟 WAN？

可以使用[快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)创建具有单个中心和单个 VPN 站点的单个虚拟 WAN 的简单配置。 虚拟 WAN 从根本上来说是一种 REST 或门户驱动的服务。

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure 虚拟 WAN 是否支持全局 VNet 对等互连？ 

可以在与虚拟 WAN 不同的区域中连接 VNet。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>连接到虚拟中心的分支 VNet 是否能够相互通信？

是的。 标准虚拟 WAN 支持通过 VNet 所连接到的虚拟 WAN 中心建立 VNet 到 VNet 传输连接。 在虚拟 WAN 的术语中，这些路径称为“本地虚拟 WAN VNet 传输”（适用于已连接到单个区域中虚拟 WAN 中心的 VNet），或“全局虚拟 WAN VNet 传输”（适用于通过多个虚拟 WAN 中心跨两个或更多个区域连接的 VNet）。 公共预览版的 VNet 传输最多支持 3 Gbps 吞吐量。 全局传输推出正式版后，吞吐量将会提高。   

对于某些方案，除了使用本地或全局虚拟 WAN VNet 传输以外，还可以使用[虚拟网络对等互连](../articles/virtual-network/virtual-network-peering-overview.md)将各个辐射 VNet 直接对等互连。 在这种情况下，VNet 对等互连优先于通过虚拟 WAN 中心建立的传输连接。 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>到 Azure 虚拟 WAN 的分支连接是什么？

从分支设备到 Azure 虚拟 WAN 的连接最多支持四条链路。 链路是分支位置的物理连接链路（例如：ATT、Verizon 等）。 每个链路连接都包含两个主动/主动 IPsec 隧道。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>虚拟 WAN 中是否允许分支到分支连接？

是的，对于 VPN 和 VPN 到 ExpressRoute，分支到分支连接在虚拟 WAN 中可用。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>分支到分支流量是否可以通过 Azure 虚拟 WAN？

是的。

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>虚拟 WAN 是否要求每个站点中都有 ExpressRoute？

否，虚拟 WAN 不要求每个站点中都有 ExpressRoute。 它通过 Internet 链路使用从设备到 Azure 虚拟 WAN 中心的标准 IPsec 站点到站点连接。 可以使用 ExpressRoute 线路将站点连接到提供商网络。 对于使用虚拟中心内的 ExpressRoute 进行连接的站点，站点在 VPN 与 ExpressRoute 之间可能有分支到分支流量流。

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>使用 Azure 虚拟 WAN 时是否存在网络吞吐量限制？

分支数限制为每个中心/区域 1000 个连接，中心内总带宽为 20 Gbps。 每个区域可以有 1 个中心。

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>一个虚拟 WAN 中心支持多少个 VPN 连接？

一个 Azure 虚拟 WAN 中心可以同时支持最多 1,000 个 S2S 连接接、10,000 个 P2S 连接和 4 个 ExpressRoute 连接。

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>一个 VPN 隧道和一个连接的总 VPN 吞吐量是多少？

一个中心的总 VPN 吞吐量最多为 20 Gbps，具体取决于所选缩放单元。 吞吐量由所有现有连接共享。 连接中的每个隧道最多可以支持 1 Gbps。

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>我在门户中看不到虚拟中心的 20 Gbps 设置。 我该如何配置它？

导航到门户上中心内的 VPN 网关，并单击缩放单元，将其更改为适当的设置。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>虚拟 WAN 是否允许本地设备并行利用多个 ISP？亦或它始终为单个 VPN 隧道？

使用分支提供的链路与建立虚拟 WAN VPN 连接始终是主动-主动隧道（在同一中心/区域具有复原能力）。 此链接可以是本地分支的 ISP 链接。 虚拟 WAN“VPNSite”提供将链路信息添加到站点的能力。 如果分支有多个 ISP，并且每个 ISP 提供了一条链路，则可在 Azure 中的 VPN 站点信息中设置该信息。 但是，在分支机构之间管理跨 ISP 的故障转移完全是以分支为中心的路由操作。

### <a name="what-is-global-transit-architecture"></a>什么是全局传输体系结构？

有关全局传输体系结构的信息，请参阅[全局传输网络体系结构和虚拟 WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)。

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>流量在 Azure 主干网上是如何路由的？

流量遵循以下模式：分支设备 -> ISP -> Microsoft 网络边缘 -> Microsoft DC（中心 VNet）-> Microsoft 网络边缘 -> ISP -> 分支设备

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>在此模型中，需要在每个站点执行什么操作？ 只需要创建 Internet 连接？

是的。 支持 IPsec 的 Internet 连接和物理设备，最好是来自我们的集成[虚拟 WAN 合作伙伴](../articles/virtual-wan/virtual-wan-locations-partners.md)。 还可以从你偏爱的设备手动管理 Azure 的配置和连接。

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>如何在连接（VPN、ExpressRoute 或虚拟网络）中启用默认路由 (0.0.0.0/0)：

如果连接上的标志为“已启用”，则虚拟中心可将获知的默认路由传播到虚拟网络/站点到站点 VPN/ExpressRoute 连接。 当用户编辑虚拟网络连接、VPN 连接或 ExpressRoute 连接时，将显示此标志。 默认情况下，当站点或 ExpressRoute 线路连接到中心时，将禁用此标志。 如果添加虚拟网络连接以将 VNet 连接到虚拟中心，则默认情况下启用此功能。 默认路由不是源自虚拟 WAN 中心；只有当虚拟 WAN 中心由于在中心部署防火墙而获知默认路由或另一个连接的站点已启用强制隧道时，此标志才会将默认路由传播到连接。

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>虚拟 WAN 类型（基本和标准）之间的区别是什么？

“基本”WAN 类型允许创建基本中心（SKU = 基本）。 “标准”WAN 类型允许创建标准中心（SKU = 标准）。 基本中心只支持站点到站点 VPN 功能。 利用标准中心，可以通过中心使用 ExpressRoute、用户 VPN (P2S)、完整网格中心和 VNet 到 VNet 传输。 你需要为标准中心支付 0.25 美元/小时的基本费用，并为 VNet 到 VNet 的连接过程中通过中心进行的传输和中心到中心流量的数据处理支付数据处理费用。 有关详细信息，请参阅[基本和标准虚拟 WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard)。 如需了解定价，请参阅[定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页面。
