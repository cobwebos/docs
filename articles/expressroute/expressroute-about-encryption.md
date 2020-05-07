---
title: Azure ExpressRoute：关于加密
description: 了解 ExpressRoute 加密。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: cherylmc
ms.openlocfilehash: 77755ab6bdbb3c1e6416475f5066b5dd463eb7f5
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838750"
---
# <a name="expressroute-encryption"></a>ExpressRoute 加密
 
ExpressRoute 支持多种加密技术，以确保数据在网络与 Microsoft 网络之间的机密性和完整性。

## <a name="point-to-point-encryption-by-macsec-faq"></a>MACsec 的点到点加密常见问题
MACsec 是[IEEE standard](https://1.ieee802.org/security/802-1ae/)。 它在媒体访问控制（MAC）级别或网络层2上加密数据。 当你通过[ExpressRoute Direct](expressroute-erdirect-about.md)连接到 microsoft 时，可以使用 MACsec 加密网络设备和 Microsoft 网络设备之间的物理链路。 默认情况下，ExpressRoute 直接端口上禁用 MACsec。 你将自己的 MACsec 密钥用于加密，并将其存储在[Azure Key Vault](../key-vault/general/overview.md)中。 你决定何时旋转该密钥。 请参阅下面的其他常见问题解答。
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>能否在 expressroute 提供商预配的 ExpressRoute 线路上启用 MACsec？
不是。 MACsec 使用一个实体（即 customer）拥有的密钥对物理链路上的所有流量进行加密。 因此，它仅可用于 ExpressRoute 直接访问。
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>能否在 ExpressRoute 直接端口上加密一些 ExpressRoute 线路，并在同一端口上不加密其他线路？ 
不是。 启用 MACsec 后，所有网络控制流量（例如，BGP 数据流量和客户数据流量）都将加密。 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>启用/禁用 MACsec 或更新 MACsec 密钥后，我的本地网络将失去通过 ExpressRoute 连接到 Microsoft？
可以。 对于 MACsec 配置，我们仅支持预共享密钥模式。 这意味着你需要在你的设备上和 Microsoft （通过我们的 API）上更新密钥。 此更改不是原子更改，因此，如果两个端之间存在密钥不匹配，则会失去连接。 我们强烈建议你计划一个维护时段来更改配置。 为了最大限度地减少停机时间，我们建议你在将网络流量切换到另一个链接后，一次更新一次 ExpressRoute 直接连接的配置。  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>如果设备和 Microsoft 之间的 MACsec 键不匹配，流量是否会继续流动？
不是。 如果配置了 MACsec 并发生了键不匹配问题，则将失去与 Microsoft 的连接。 换句话说，我们不会回退到未加密的连接，从而公开你的数据。 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>启用 ExpressRoute 直接的 MACsec 会使网络性能下降？
MACsec 加密和解密发生在我们使用的路由器上的硬件中。 这不会对这一方产生性能影响。 但是，你应该向网络供应商核实你使用的设备，并查看 MACsec 是否有任何性能含义。
### <a name="which-cipher-suites-are-supported-for-encryption"></a>加密支持哪些密码套件？
仅支持 AES128 和 AES256 的[扩展数据包编号](https://1.ieee802.org/security/802-1aebw/)版本。 此外，请在设备上的 MACsec 配置中禁用[安全通道标识符（科幻）](https://en.wikipedia.org/wiki/IEEE_802.1AE) 。 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>通过 IPsec 的端到端加密常见问题解答
IPsec 是一种[IETF 标准](https://tools.ietf.org/html/rfc6071)。 它在 Internet 协议（IP）级别或网络层3上加密数据。 可以使用 IPsec 在本地网络与 Azure 上的虚拟网络（VNET）之间加密端到端连接。 请参阅下面的其他常见问题解答。
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>除了 ExpressRoute 直接端口上的 MACsec，是否还可以启用 IPsec？
可以。 MACsec 保护你和 Microsoft 之间的物理连接。 IPsec 保护你和 Azure 上的虚拟网络之间的端到端连接。 您可以单独启用它们。 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>能否使用 Azure VPN 网关在我的本地网络和 Azure 虚拟网络之间设置 IPsec 隧道？
可以。 可以通过与 ExpressRoute 线路的 Microsoft 对等互连来设置此 IPsec 隧道。 遵循我们的[配置指南](site-to-site-vpn-over-microsoft-peering.md)。
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>能否使用 Azure VPN 网关通过 Azure 专用对等互连设置 IPsec 隧道？
如果采用 Azure 虚拟 WAN，则可以执行[以下步骤](../virtual-wan/vpn-over-expressroute.md)来加密端到端连接。 如果有常规的 Azure VNET，可以在 VNET 中部署第三方 VPN 网关，并在其与本地 VPN 网关之间建立 IPsec 隧道。
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>在 ExpressRoute 连接上启用 IPsec 后，我将获得的吞吐量是多少？
如果使用 Azure VPN 网关，请在[此处查看性能号](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 如果使用的是第三方 VPN 网关，请与供应商联系以获取性能号。

## <a name="next-steps"></a>后续步骤
有关 MACsec 配置的详细信息，请参阅[Configure MACsec](expressroute-howto-macsec.md) 。

有关 IPsec 配置的详细信息，请参阅[配置 ipsec](site-to-site-vpn-over-microsoft-peering.md) 。
