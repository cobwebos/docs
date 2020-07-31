---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dececd066597682e240e737727d3bcaf8f8f3619
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375386"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>用户是否需要将中心辐射型拓扑与 SD-WAN/VPN 设备配合使用才能使用 Azure 虚拟 WAN？

虚拟 WAN 提供了许多内置于单个窗格中的功能，例如站点/站点到站点 VPN 连接、用户/P2S 连接、ExpressRoute 连接、虚拟网络连接、VPN ExpressRoute 互连、VNet 到 VNet 可传递连接、集中路由、Azure 防火墙和防火墙管理器安全性、监视、ExpressRoute 加密以及许多其他功能。 无需所有这些用例即可开始使用虚拟 WAN。 仅需一个用例即可开始使用。 虚拟 WAN 体系结构是一种内置了规模和性能的中心辐射型体系结构，其中的分支（VPN/SD-WAN 设备）、用户（Azure VPN 客户端、openVPN 或 IKEv2 客户端）、ExpressRoute 线路和虚拟网络充当虚拟中心的辐条。 所有中心均在标准虚拟 WAN 中以完整网格的形式进行连接，使得用户能够轻松地使用 Microsoft 主干进行任意分支到任意分支的连接。 对于包含 SD-WAN/VPN 设备的中心辐射型体系结构，用户可以在 Azure 虚拟 WAN 门户中手动设置该它，也可以使用虚拟 WAN 合作伙伴 CPE (SD-WAN/VPN) 来设置与 Azure 的连接。 虚拟 WAN 合作伙伴提供自动进行连接的功能：将设备信息导出到 Azure 中，下载 Azure 配置，然后建立与 Azure 虚拟 WAN 中心的连接。 对于点到站点/用户 VPN 连接，我们支持 [Azure VPN 客户端](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN 或 IKEv2 客户端。 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>可否在虚拟 WAN 中禁用全网格式中心？

虚拟 WAN 有两种形式：“基本”和“标准”。 在基本虚拟 WAN 中，中心不是网格式的。 在标准虚拟 WAN 中，中心是网格式的，并且可在首次设置虚拟 WAN 后自动连接。 用户无需执行任何特定操作， 也无需禁用或启用获取网格式中心的功能。 虚拟 WAN 提供了许多路由选项，用于引导任何分支（VNet、VPN 或 ExpressRoute）之间的通信。 它兼具全网格式中心的易用性和根据需求路由流量的灵活性。 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>虚拟 WAN 中如何处理可用性区域和复原？

虚拟 WAN 是中心以及其中提供的服务的集合。 用户可以根据需要拥有任意数量的虚拟 WAN。 虚拟 WAN 中心内包含多个服务，例如 VPN、ExpressRoute 等。其中每个服务（Azure 防火墙除外）均部署在“可用性区域”区域中（如果该区域支持可用性区域）。 如果在中心进行初始部署后，某个区域成为可用性区域，则用户可以重新创建网关，这将触发可用性区域部署。 所有网关在中心内都预配为主动-主动，这意味着中心内置了复原能力。 如果用户需要跨区域的复原能力，可以连接到多个中心。 尽管虚拟 WAN 的概念是全球性的，但实际的虚拟 WAN 资源基于资源管理器，并且按区域进行部署。 如果虚拟 WAN 区域本身遇到问题，则该虚拟 WAN 中的所有中心都将继续按原样运行，但在虚拟 WAN 区域可用之前，用户无法创建新的中心。

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Azure 虚拟 WAN 用户 VPN（点到站点）支持什么客户端？

虚拟 WAN 支持 [Azure VPN 客户端](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN 客户端或任何 IKEv2 客户端。 Azure VPN 客户端支持 Azure AD 身份验证。至少需要 Windows 10 客户端 OS 17763.0 或更高版本。  OpenVPN 客户端可以支持基于证书的身份验证。 在网关上选择基于证书的身份验证后，可看到需下载到设备的 .ovpn 文件。 IKEv2 支持证书和 RADIUS 身份验证。 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>就用户 VPN（点到站点）来说，为什么将 P2S 客户端池拆分为两个路由？

每个网关都有两个实例，进行拆分是为了使每个网关实例可以独立地为连接的客户端分配客户端 IP，并将来自虚拟网络的流量路由回正确的网关实例，避免网关间的实例跃点。

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>如何为 P2S 客户端添加 DNS 服务器？

可以通过两个选项为 P2S 客户端添加 DNS 服务器。 首选第一种方法，因为它将自定义 DNS 服务器添加到网关而不是客户端。

1. 使用以下 PowerShell 脚本添加自定义 DNS 服务器。 将值替换为你环境的值。

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. 或者，如果你使用的是适用于 Windows 10 的 Azure VPN 客户端，则可修改下载的 XML 配置文件，在导入该文件之前添加 \<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers> 标记。

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>就用户 VPN（点到站点）来说，支持多少个客户端？

每个用户 VPN P2S 网关都有两个实例，每个实例支持的用户数会随缩放单元的变化而变化，并有一个上限。 缩放单元 1-3 支持 500 个连接，缩放单元 4-6 支持 1000 个连接，缩放单元 7-12 支持 5000 个连接，缩放单元 13-20 支持最多 10,000 个连接。 

例如，假设用户选择 1 个缩放单元。 每个缩放单元的存在都意味着已部署主动-主动网关，并且每个实例（在本例中为 2 个）都支持最多 500 个连接。 每个网关可以获得 500 * 2 个连接，但这并不意味着你要为此缩放单元的 1000 个（而不是 500 个）连接做规划。 系统可能需要为实例提供服务，而在服务期间，当你超过建议的连接数时，系统可能会中断这额外的 500 个连接。 此外，如果决定在缩放单元上纵向扩展或缩减，或者在 VPN 网关上更改点到站点配置，请确保计划停机时间。

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 虚拟网络网关（VPN 网关）和 Azure 虚拟 WAN VPN 网关之间有什么区别？

虚拟 WAN 提供大规模站点到站点连接，在设计上考虑到了吞吐量、可伸缩性和易用性。 将站点连接到虚拟 WAN VPN 网关时，它不同于使用网关类型 “VPN”的常规虚拟网络网关。 同样，将 ExpressRoute 线路连接到虚拟 WAN 中心时，它对 ExpressRoute 网关使用的资源与对使用“ExpressRoute”网关类型的常规虚拟网络网关使用的资源不同。 

对于 VPN 和 ExpressRoute，虚拟 WAN 最多支持 20 Gbps 聚合吞吐量。 虚拟 WAN 还实现了与 CPE 分支设备合作伙伴生态系统的连接自动化。 CPE 分支设备具有自动预配并连接到 Azure 虚拟 WAN 的内置自动化。 这些设备由一个不断扩张的 SD-WAN 和 VPN 合作伙伴生态系统提供。 请参阅[首选合作伙伴列表](../articles/virtual-wan/virtual-wan-locations-partners.md)。

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>虚拟 WAN 与 Azure 虚拟网络网关有何不同？

虚拟网络网关 VPN 限制为 30 个隧道。 对于连接，应当为大型 VPN 使用虚拟 WAN。 每个区域（虚拟中心）最多可以连接 1,000 个分支连接，每个中心有 20 Gbps 聚合。 连接是从本地 VPN 设备到虚拟中心的主动-主动隧道。 每个区域中可以有一个中心，这意味着你可以跨中心连接到 1,000 多个分支。

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>什么是虚拟 WAN 网关缩放单元

根据定义，缩放单元是用于选择虚拟中心内网关的聚合吞吐量的单位。 1 个缩放单元的 VPN 为 500 Mbps。 1 个缩放单元的 ExpressRoute 为 2 Gbps。 示例：10 个缩放单元的 VPN：500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>支持哪些设备提供商（虚拟 WAN 合作伙伴）？

目前，许多合作伙伴都支持全自动虚拟 WAN 体验。 有关详细信息，请参阅[虚拟 WAN 合作伙伴](../articles/virtual-wan/virtual-wan-locations-partners.md)。

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>虚拟 WAN 合作伙伴自动化步骤有哪些？

有关合作伙伴自动化步骤，请参阅[虚拟 WAN 合作伙伴自动化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>是否需要使用首选的合作伙伴设备？

否。 可以使用任何支持 VPN 且符合 Azure 对 IKEv2/IKEv1 IPsec 的支持要求的设备。 虚拟 WAN 还具有 CPE 合作伙伴解决方案，该解决方案可自动连接到 Azure 虚拟 WAN，使大规模设置 IPsec VPN 连接变得更加容易。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>虚拟 WAN 合作伙伴如何自动与 Azure 虚拟 WAN 建立连接？

软件定义的连接解决方案通常使用控制器或设备预配中心来管理其分支设备。 控制器可以使用 Azure API 自动与 Azure 虚拟 WAN 建立连接。 自动化包括上传分支信息、下载 Azure 配置、将 IPSec 隧道设置到 Azure 虚拟中心，以及自动设置从分支设备到 Azure 虚拟 WAN 的连接。 当有数百个分支时，可以轻松使用虚拟 WAN CPE 合作伙伴进行连接，因为载入体验无需设置、配置和管理大规模的 IPsec 连接。 有关详细信息，请参阅[虚拟 WAN 合作伙伴自动化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>如果使用的设备不在虚拟 WAN 合作伙伴列表中，该怎么办？ 还可以用它来连接到 Azure 虚拟 WAN VPN 吗？

是的，只要设备支持 IPsec IKEv1 或 IKEv2 即可。 虚拟 WAN 合作伙伴自动执行设备到 Azure VPN 端点的连接。 这表示自动执行“分支信息上传”、“IPsec 和配置”以及“连接”等步骤。由于设备不是来自虚拟 WAN 合作伙伴生态系统，因此需要大量手动执行 Azure 配置和更新设备才能建立 IPsec 连接。

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>没有在启动合作伙伴列表中列出的新合作伙伴如何加入？

所有虚拟 WAN API 都是开放式 API。 可以重新查看文档[虚拟 WAN 合作伙伴自动化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)，评估技术可行性。 理想的合作伙伴具有可以预配 IKEv1 或 IKEv2 IPsec 连接的设备。 在公司根据上面提供的自动化指导原则完成其 CPE 设备的自动化工作后，你可以联系 azurevirtualwan@microsoft.com，以便将自己纳入[通过合作伙伴建立连接]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners)列表中。 如果你是一位客户，并且希望将某个公司的解决方案列为虚拟 WAN 合作伙伴，请让该公司通过向 azurevirtualwan@microsoft.com 发送电子邮件联系虚拟 WAN。

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>虚拟 WAN 如何支持 SD-WAN 设备？

虚拟 WAN 合作伙伴自动执行 Azure VPN 端点的 IPsec 连接。 如果虚拟 WAN 合作伙伴是 SD-WAN 提供商，则表示 SD-WAN 控制器管理到 Azure VPN 端点的自动化和 IPsec 连接。 如果 SD-WAN 设备需要自己的端点而不是 Azure VPN 来实现任何专有 SD-WAN 功能，则可以在 Azure VNet 中部署 SD-WAN 端点并与 Azure 虚拟 WAN 共存。

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>允许多少 VPN 设备连接到单个中心？

每个虚拟中心最多支持 1,000 个连接。 每个连接包括四条链路，每条链路连接支持采用主动-主动配置的两个隧道。 隧道在 Azure 虚拟中心 VPN 网关中终止。 链接表示分支/VPN 设备上的物理 ISP 链接。

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>到 Azure 虚拟 WAN 的分支连接是什么？

从分支或 VPN 设备到 Azure 虚拟 WAN 的连接只是一个 VPN 连接，它实际上连接了虚拟中心内的 VPN 站点和 Azure VPN 网关。

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>本地 VPN 设备是否可以连接到多个中心？

是的。 开始时的流量流将从本地设备发送到最近的 Microsoft 网络边缘，然后才发送到虚拟中心。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>虚拟 WAN 是否有新的可用资源管理器资源？
  
是的，虚拟 WAN 有新的资源管理器资源。 有关详细信息，请参阅[概述](../articles/virtual-wan/virtual-wan-about.md)。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>是否可以在 Azure 虚拟 WAN 中部署和使用我偏爱的网络虚拟设备（在 NVA VNet 中）？

是的，可以将你偏爱的网络虚拟设备 (NVA) VNet 连接到 Azure Virtual WAN。

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>可以在虚拟中心内创建网络虚拟设备吗？

无法在虚拟中心内部署网络虚拟设备 (NVA)。 但是，可以在连接到虚拟中心的分支 VNet 中创建它，并根据需要启用适当的路由定向流量。

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>辐射 VNet 是否可以包含虚拟网络网关？

否。 如果辐射 VNet 已连接到虚拟中心，则不能包含虚拟网络网关。

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>VPN 连接是否支持 BGP？

是的，支持 BGP。 创建 VPN 站点时，可以在其中提供 BGP 参数。 这表示在 Azure 中为该站点创建的任何连接都将启用 BGP。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>虚拟 WAN 是否有任何许可或定价信息？

是的。 请参阅[定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页面。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>是否可以使用资源管理器模板构造 Azure 虚拟 WAN？

可以使用[快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)创建具有单个中心和单个 VPN 站点的单个虚拟 WAN 的简单配置。 虚拟 WAN 从根本上来说是一种 REST 或门户驱动的服务。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>连接到虚拟中心的分支 VNet 能否相互通信（V2V 传输）？

是的。 标准虚拟 WAN 支持通过 VNet 所连接到的虚拟 WAN 中心建立 VNet 到 VNet 可传递连接。 在虚拟 WAN 的术语中，这些路径称为“本地虚拟 WAN VNet 传输”（适用于已连接到单个区域中虚拟 WAN 中心的 VNet），或“全局虚拟 WAN VNet 传输”（适用于通过多个虚拟 WAN 中心跨两个或更多个区域连接的 VNet）。 对于某些方案，除了使用本地或全局虚拟 WAN VNet 传输以外，还可以使用[虚拟网络对等互连](../articles/virtual-network/virtual-network-peering-overview.md)将各个分支 VNet 直接对等互连。 在这种情况下，VNet 对等互连优先于通过虚拟 WAN 中心建立的可传递连接。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>虚拟 WAN 中是否允许分支到分支连接？

是的，分支到分支连接在虚拟 WAN 中可用。 分支在概念上适用于 VPN 站点、ExpressRoute 线路或点到站点/用户 VPN 用户。 默认情况下，“启用分支到分支”处于启用状态，你可在 WAN 配置设置中找到它。 这使得 VPN 分支/用户能够连接到其他 VPN 分支，并在 VPN 和 ExpressRoute 用户之间启用传输连接。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>分支到分支流量是否可以通过 Azure 虚拟 WAN？

是的。

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>虚拟 WAN 是否要求每个站点中都有 ExpressRoute？

不是。 虚拟 WAN 不要求每个站点中都有 ExpressRoute。 可以使用 ExpressRoute 线路将站点连接到提供商网络。 对于使用 ExpressRoute 连接到虚拟中心以及将 IPsec VPN 连接到同一中心的站点，虚拟中心提供了 VPN 和 ExpressRoute 用户之间的传输连接。

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>使用 Azure 虚拟 WAN 时是否存在网络吞吐量或连接限制？

网络吞吐量基于虚拟 WAN 中心的每项服务。 尽管你可以根据需要拥有任意数量的虚拟 WAN，但是每个虚拟 WAN 只允许每个区域有 1 个中心。 在每个中心，VPN 聚合吞吐量最高可达 20 Gbps，ExpressRoute 聚合吞吐量最高可达 20 Gbps，用户 VPN/点到站点 VPN 聚合吞吐量最高可达 20 Gbps。 虚拟中心的路由器最多支持 50 Gbps 的 VNet 到 VNet 流量流，并假设虚拟 WAN 中心的所有 VNet 总共有 2000 个 VM 工作负载。

当 VPN 站点连接到中心时，它们通过连接来完成此操作。 对于每个虚拟中心，虚拟 WAN 最多支持 1000 个连接或 2000 个 IPsec 隧道。 当远程用户连接到虚拟中心时，他们将连接到 P2S VPN 网关，该网关最多支持 10000 位用户，具体取决于为虚拟中心的 P2S VPN 网关选择的缩放单元（带宽）。

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>一个 VPN 隧道和一个连接的总 VPN 吞吐量是多少？

一个中心的总 VPN 吞吐量最多为 20 Gbps，具体取决于为 VPN 网关选择的缩放单元。 吞吐量由所有现有连接共享。 连接中的每个隧道最多可以支持 1 Gbps。

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>我在门户中看不到虚拟中心的 20 Gbps 设置。 我该如何配置它？

导航到门户上中心内的 VPN 网关，并单击缩放单元，将其更改为适当的设置。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>虚拟 WAN 是否允许本地设备并行利用多个 ISP？亦或它始终为单个 VPN 隧道？

本地设备解决方案可以应用流量策略，以引导流量通过多个隧道进入 Azure 虚拟 WAN 中心（虚拟中心的 VPN 网关）。

### <a name="what-is-global-transit-architecture"></a>什么是全局传输体系结构？

有关全局传输体系结构的信息，请参阅[全局传输网络体系结构和虚拟 WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)。

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>流量在 Azure 主干网上是如何路由的？

流量遵循以下模式：分支设备 -> ISP -> Microsoft 网络边缘 -> Microsoft DC（中心 VNet）-> Microsoft 网络边缘 -> ISP -> 分支设备

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>在此模型中，需要在每个站点执行什么操作？ 只需要创建 Internet 连接？

是的。 支持 IPsec 的 Internet 连接和物理设备，最好是来自我们的集成[虚拟 WAN 合作伙伴](../articles/virtual-wan/virtual-wan-locations-partners.md)。 还可以从你偏爱的设备手动管理 Azure 的配置和连接。

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>如何在连接（VPN、ExpressRoute 或虚拟网络）中启用默认路由 (0.0.0.0/0)：

如果连接上的标志为“已启用”，则虚拟中心可将获知的默认路由传播到虚拟网络/站点到站点 VPN/ExpressRoute 连接。 当用户编辑虚拟网络连接、VPN 连接或 ExpressRoute 连接时，将显示此标志。 默认情况下，当站点或 ExpressRoute 线路连接到中心时，将禁用此标志。 如果添加虚拟网络连接以将 VNet 连接到虚拟中心，则默认情况下启用此功能。 默认路由不是源自虚拟 WAN 中心；只有当虚拟 WAN 中心由于在中心部署防火墙而获知默认路由或另一个连接的站点已启用强制隧道时，此标志才会将默认路由传播到连接。

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>虚拟 WAN 中的虚拟中心如何从多个中心选择路由的最佳路径

如果某个虚拟中心从多个远程中心获知同一路由，则其决定顺序如下所示：

1. 最长前缀匹配。
2. 本地路由优先于中心间路由。
3. 静态路由优先于 BGP 路由：这与虚拟中心路由器做出的决定有关。 但是，如果决策者是其中的站点通过 BGP 播发路由的 VPN 网关，或者是提供静态地址前缀的 VPN 网关，则静态路由可能优先于 BGP 路由。
4. ExpressRoute (ER) 优先于 VPN：在本地中心，ER 优先于 VPN。 ExpressRoute 线路之间的传输连接只能通过 Global Reach 提供。 因此，当有一个 ExpressRoute 线路连接到一个中心，并且有另一个 ExpressRoute 线路通过 VPN 连接连接到其他中心时，对于中心间路由方案，VPN 可能是首选项。
5. AS 路径长度。

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>虚拟 WAN 中心是否允许 ExpressRoute 线路之间的连接。

ER 到 ER 之间的传输始终通过 Global Reach 进行。 虚拟中心网关部署在 DC 或 Azure 区域中。 当两条 ExpressRoute 线路通过 Global Reach 连接时，不需要流量完全从边缘路由器传递到虚拟中心 DC。

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Azure 虚拟 WAN ExpressRoute 线路或 VPN 连接中是否存在权重的概念

当多个 ExpressRoute 线路连接到一个虚拟中心时，该连接上的路由权重为虚拟中心的 ExpressRoute 提供了选择优先线路的机制。 没有用于在 VPN 连接上设置权重的机制。 与单个中心内，Azure 始终优先选择 ExpressRoute 连接而非 VPN 连接。

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>对于传出 Azure 的流量，虚拟 WAN 是否优先选择 ExpressRoute 而非 VPN

是 

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-prefered-over-expressroute"></a>当虚拟 WAN 中心具有 ExpressRoute 线路和连接到该线路的 VPN 站点时，什么原因会导致优先选择 VPN 连接而非 ExpressRoute？

当 ExpressRoute 线路连接到虚拟中心时，Microsoft 边缘路由器是在本地与 Azure 之间进行通信的第一个节点。 这些边缘路由器与虚拟 WAN ExpressRoute 网关通信，而这些网关从控制虚拟 WAN 中任何网关之间的所有路由的虚拟中心路由器获知路由。 Microsoft 边缘路由器优先处理虚拟中心 ExpressRoute 路由而非从本地获知的路由。 如果 VPN 连接因任何原因而成为虚拟中心获知路由的主要媒介（例如 ExpressRoute 和 VPN 之间的故障转移方案），那么除非 VPN 站点具有较长的 AS 路径长度，否则虚拟中心将继续与 ExpressRoute 网关分享获知的 VPN 路由，这将导致 Microsoft 边缘路由器优先处理 VPN 路由而非本地路由。 

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>如果连接了两个中心（中心 1 和 2），并且有一条 ExpressRoute 线路以蝴蝶结的形式连接到这两个中心，那么连接到中心 1 的 VNet 通过什么路径到达中心 2 中连接的 VNet？

对于 VNet 到 VNet 连接，当前行为优先选择 ExpressRoute 线路路径而非中心到中心。 但是，不建议在虚拟 WAN 设置中进行此选择。 虚拟 WAN 团队正在研究修补程序，以支持优先选择中心到中心而非 ExpressRoute 路径。 对于区域间流量流，建议将多个 ExpressRoute 线路（不同提供商）连接到一个中心，并使用虚拟 WAN 提供的中心到中心连接。

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>虚拟 WAN 是否支持 IPv6？

虚拟 WAN 中心及其网关不支持 IPv6。 如果你有一个支持 IPv6 的 VNet，并且要将 VNet 连接到虚拟 WAN，则当前不支持此方案。

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionality-"></a>对于自动完成多种虚拟 WAN 功能的脚本，建议其使用哪个 API 版本？

所需的最低版本为 05-01-2020（2020 年 5 月 1 日）。 

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>虚拟 WAN 类型（基本和标准）之间的区别是什么？

请参阅[基本和标准虚拟 WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard)。 如需了解定价，请参阅[定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页面。
