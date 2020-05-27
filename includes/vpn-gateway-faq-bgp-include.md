---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 608b148dc3929065df44530da65e695df19be03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79486062"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP 是否在所有 Azure VPN 网关 SKU 上受支持？
除基本 SKU 外，所有 Azure VPN 网关 SKU 都支持 BGP。

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>能否将 BGP 用于基于 Azure Policy 的 VPN 网关？
否，只有基于路由的 VPN 网关支持 BGP。

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>能否使用专用 ASN（自治系统编号）？
能，可以将自己的公共 ASN 或专用 ASN 同时用于本地网络和 Azure 虚拟网络。

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>能否使用 32 位（4 字节）ASN（自治系统编号）？
能，Azure VPN 网关现在支持 32 位（4 字节）ASN。 请使用 PowerShell/CLI/SDK 配置使用十进制格式的 ASN。

### <a name="are-there-asns-reserved-by-azure"></a>是否存在由 Azure 保留的 ASN？
是，Azure 保留了以下 ASN 用于内部和外部的对等互连：

* 公用 ASN：8074、8075、12076
* 专用 ASN：65515、65517、65518、65519、65520

连接到 Azure VPN 网关时，不能为本地 VPN 设备指定这些 ASN。

### <a name="are-there-any-other-asns-that-i-cant-use"></a>是否有任何其他我不能使用的 ASN？
是的。以下 ASN 是 [IANA 保留](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)的，不能在 Azure VPN 网关上配置：

23456、64496-64511、65535-65551 和 429496729

### <a name="what-private-asns-can-i-use"></a>我可以使用哪些专用 ASN？
可使用的专用 ASN 的可用范围如下：

* 64512-65514、65521-65534

IANA 或 Azure 不保留这些 ASN 以供使用，因此可用于分配给 Azure VPN 网关。

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>能否将同一个 ASN 同时用于本地 VPN 网络和 Azure VNet？
否，必须在本地网络和 Azure VNet 之间分配不同 ASN（如果要使用 BGP 将它们连接在一起）。 无论是否为跨界连接启用了 BGP，都会为 Azure VPN 网关分配默认 ASN（即 65515）。 可以通过在创建 VPN 网关时分配不同 ASN，或者在创建网关后更改 ASN 来覆盖此默认值。 需要将本地 ASN 分配给相应的 Azure 本地网关。

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN 网关将播发给我哪些地址前缀？
Azure VPN 网关会将以下路由播发到本地 BGP 设备：

* VNet 地址前缀
* 已连接到 Azure VPN 网关的每个本地网关的地址前缀
* 从连接到 Azure VPN 网关的其他 BGP 对等会话获知的路由， **默认路由或与任何 VNet 前缀重叠的路由除外**。

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>我可以向 Azure VPN 网关发布多少个前缀？
我们支持最多 4000 个前缀。 如果前缀数目超过此限制，将丢弃 BGP 会话。

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>能否将默认路由 (0.0.0.0/0) 播发给 Azure VPN 网关？
是的。

请注意，这样会强制所有的 VNet 出口流量流向本地站点，并且会阻止 VNet VM 接受直接来自 Internet 的公共通信，例如从 Internet 发往 VM 的 RDP 或 SSH。

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>能否播发与虚拟网络前缀完全相同的前缀？

不能，Azure 平台将阻止播发与任一虚拟网络地址前缀相同的前缀或对其进行筛选。 但是，可播发属于虚拟网络内所拥有内容超集的前缀。 

例如，如果虚拟网络使用了地址空间 10.0.0.0/16，则可以播发 10.0.0.0/8， 但无法播发 10.0.0.0/16 或 10.0.0.0/24。

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>能否将 BGP 用于 VNet 到 VNet 连接？
能，可以将 BGP 同时用于跨界连接和 VNet 到 VNet 连接。

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>能否将 BGP 连接与非 BGP 连接混合用于 Azure VPN 网关？
能，可以将 BGP 连接和非 BGP 连接混合用于同一 Azure VPN 网关。

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN 网关是否支持 BGP 传输路由？
是，支持 BGP 传输路由，但例外是 Azure VPN 网关 **不** 会将默认路由播发到其他 BGP 对等节点。 若要启用跨多个 Azure VPN 网关的传输路由，必须在所有中间 VNet 到 VNet 连接上启用 BGP。 有关详细信息，请参阅[关于 BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)。

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>在 Azure VPN 网关和我的本地网络之间能否有多个隧道？
能，可以在 Azure VPN 网关和本地网络之间建立多个 S2S VPN 隧道。 请注意，所有这些隧道都将计入 Azure VPN 网关的隧道总数，必须在两个隧道上启用 BGP。

例如，如果在 Azure VPN 网关与一个本地网络之间有两个冗余隧道，它们将占用 Azure VPN 网关的总配额（标准为 10 个，高性能为 30 个）中的 2 个隧道。

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>在两个使用 BGP 的 Azure VNet 之间能否有多个隧道？
是，但必须至少有一个虚拟网络网关采用主动-主动配置。

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>能否在 ExpressRoute/S2S VPN 共存配置中对 S2S VPN 使用 BGP？
是的。 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN 网关将哪个地址用于 BGP 对等节点 IP？
Azure VPN 网关将从 GatewaySubnet 范围中为主动-待机 VPN 网关分配一个 IP 地址，或为主动-主动 VPN 网关分配两个 IP 地址。 你可以使用 PowerShell （AzVirtualNetworkGateway、查找 "bgpPeeringAddress" 属性）或 Azure 门户（在 "网关配置" 页上的 "配置 BGP ASN" 属性下）来获取分配的实际 BGP IP 地址。

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN 设备上的 BGP 对等节点 IP 地址的要求是什么？
本地 BGP 对等节点地址**不能**与 VPN 到设备的公共 IP 地址或 VPN 网关的 Vnet 地址空间相同。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是一个分配给设备上环回接口的地址，但请注意，它不能是 APIPA (169.254.x.x) 地址。 在表示该位置的相应本地网关中指定此地址。

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>使用 BGP 时应将什么指定为本地网关的地址前缀？
Azure 本地网关为本地网络指定初始地址前缀。 使用 BGP 时，必须分配 BGP 对等节点 IP 地址的主机前缀（/32 前缀）作为本地网络的地址空间。 如果 BGP 对等节点 IP 为 10.52.255.254，则应指定“10.52.255.254/32”作为表示此本地网络的本地网关的 localNetworkAddressSpace。 这是为了确保 Azure VPN 网关通过 S2S VPN 隧道建立 BGP 会话。

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>应为 BGP 对等会话添加到本地 VPN 设备什么内容？
应在指向 IPsec S2S VPN 隧道的 VPN 设备上添加 Azure BGP 对等节点 IP 地址的主机路由。 例如，如果 Azure VPN 对等节点 IP 为“10.12.255.30”，则应在 VPN 设备上添加“10.12.255.30”的主机路由（包含匹配的 IPsec 隧道接口的下一跃点接口）。
