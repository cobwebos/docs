---
title: Azure ExpressRoute：关于加密
description: 了解 ExpressRoute 加密。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: duau
ms.openlocfilehash: 46f0a0e86c5db612f440bcf631329d2800251dab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397791"
---
# <a name="expressroute-encryption"></a>ExpressRoute 加密
 
ExpressRoute 支持多种加密技术，可确保在你的网络和 Microsoft 网络之间传输的数据的机密性和完整性。

## <a name="point-to-point-encryption-by-macsec-faq"></a>通过 MACsec 进行的点对点加密常见问题解答
MACsec 是 [IEEE 标准](https://1.ieee802.org/security/802-1ae/)。 它在媒体访问控制 (MAC) 级别或网络层 2 上加密数据。 通过 [ ExpressRoute Direct ](expressroute-erdirect-about.md) 连接到 Microsoft 时，可以使用 MACsec 加密你的网络设备和 Microsoft 网络设备之间的物理链路。 默认情况下，ExpressRoute Direct 端口上的 MACsec 已禁用。 可以使用自带 MACsec 密钥进行加密，并将其存储在 [Azure Key Vault](../key-vault/general/overview.md) 中。 你决定何时轮换该密钥。 请参阅下面的其他常见问题解答。
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>能否在 ExpressRoute 提供商预配的 ExpressRoute 线路上启用 MACsec？
否。 MACsec 使用一个实体（即客户）拥有的密钥加密物理链路上的所有流量。 因此，它仅在 ExpressRoute Direct 上可用。
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>能否加密 ExpressRoute Direct 端口上的一些 ExpressRoute 线路，而不加密相同端口上的其他线路？ 
否。 启用 MACsec 后，所有网络控制流量（例如，BGP 数据流量和客户数据流量）都会被加密。 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>当我启用/禁用 MACsec 或更新 MACsec 密钥时，我的本地网络是否会失去通过 ExpressRoute 与 Microsoft 进行的连接？
是的。 对于 MACsec 配置，我们仅支持预共享密钥模式。 这意味着你需要在你的设备上和 Microsoft 的设备上（通过我们的 API）更新密钥。 此更改不是原子更改，因此，如果两端之间存在密钥不匹配的情况，则会失去连接。 我们强烈建议你安排一个维护时段进行配置更改。 为了最大限度地减少停机时间，我们建议你在将网络流量切换到另一个链路后，更新 ExpressRoute Direct 的一个链路的配置。  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>如果我的设备与 Microsoft 设备之间的 MACsec 密钥不匹配，是否仍会有流量？
否。 如果在配置 MACsec 之后发生了密钥不匹配问题，则会失去与 Microsoft 的连接。 换句话说，我们不会回退到未加密的连接，此类连接会公开你的数据。 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>在 ExpressRoute Direct 上启用 MACsec 是否会降低网络性能？
MACsec 加密和解密发生在我们使用的路由器的硬件中。 对我们来说没有任何性能影响。 但是，你应该向网络供应商咨询所使用的设备，并查看 MACsec 是否有性能影响。
### <a name="which-cipher-suites-are-supported-for-encryption"></a>加密支持哪些密码套件？
我们仅支持 AES128 和 AES256 的[扩展数据包编号](https://1.ieee802.org/security/802-1aebw/)版本。 此外，请在设备上的 MACsec 配置中禁用[安全通道标识符(SCI)](https://en.wikipedia.org/wiki/IEEE_802.1AE)。 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>通过 IPsec 进行的端到端加密常见问题解答
IPsec 是 [IETF 标准](https://tools.ietf.org/html/rfc6071)。 它在 Internet 协议 (IP) 级别或网络层 3 上加密数据。 可以使用 IPsec 加密本地网络与 Azure 上虚拟网络 (VNET) 之间的端到端连接。 请参阅下面的其他常见问题解答。
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>除了在 ExpressRoute Direct 端口上启用 MACsec 外，是否还可以启用 IPsec？
是的。 MACsec 会保护你与 Microsoft 之间的物理连接。 IPsec 会保护你与 Azure 上虚拟网络之间的端到端连接。 你可以单独启用它们。 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>能否使用 Azure VPN 网关在我的本地网络与 Azure 虚拟网络之间设置 IPsec 隧道？
是的。 可以通过 ExpressRoute 线路的 Microsoft 对等互连来设置此 IPsec 隧道。 请按照我们的[配置指南](site-to-site-vpn-over-microsoft-peering.md)进行操作。
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>能否使用 Azure VPN 网关通过 Azure 专用对等互连设置 IPsec 隧道？
如果采用 Azure 虚拟 WAN，则可以按照[这些步骤](../virtual-wan/vpn-over-expressroute.md)加密端到端连接。 如果有常规的 Azure VNET，可以在 VNET 中部署第三方 VPN 网关，并在它与本地 VPN 网关之间建立 IPsec 隧道。
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>在 ExpressRoute 连接上启用 IPsec 后，我将获得多少吞吐量？
如果使用 Azure VPN 网关，请检查[此处的性能数字](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 如果使用第三方 VPN 网关，请向供应商查询性能数字。

## <a name="next-steps"></a>后续步骤
有关 MACsec 配置的详细信息，请参阅[配置 MACsec](expressroute-howto-macsec.md)。

有关 IPsec 配置的详细信息，请参阅[配置 IPsec](site-to-site-vpn-over-microsoft-peering.md)。
