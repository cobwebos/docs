---
title: Azure 快速路由：关于加密
description: 了解快速路由加密。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461457"
---
# <a name="expressroute-encryption"></a>ExpressRoute 加密
 
ExpressRoute 支持一些加密技术，以确保网络和 Microsoft 网络之间遍历数据的机密性和完整性。

## <a name="point-to-point-encryption-by-macsec-faq"></a>由 MACsec 常见问题解答进行点对点加密
MACsec 是[IEEE 标准](https://1.ieee802.org/security/802-1ae/)。 它在媒体访问控制 （MAC） 级别或网络层 2 加密数据。 当您通过[ExpressRoute Direct](expressroute-erdirect-about.md)连接到 Microsoft 时，您可以使用 MACsec 加密网络设备和微软网络设备之间的物理链接。 默认情况下，在 ExpressRoute Direct 端口上禁用 MACsec。 您自带 MACsec 密钥进行加密，并将其存储在[Azure 密钥保管库中](../key-vault/general/overview.md)。 您决定何时轮换密钥。 请参阅下面的其他常见问题解答。
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>我能否在由快递提供商预配的 ExpressRoute 电路上启用 MACsec？
不是。 MACsec 使用一个实体（即客户）拥有的密钥对物理链路上的所有流量进行加密。 因此，它仅在 ExpressRoute Direct 上可用。
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>是否可以加密 ExpressRoute Direct 端口上的一些 ExpressRoute 电路，并在同一端口上保留其他电路未加密？ 
不是。 启用 MACsec 后，所有网络控制流量（例如，BGP 数据流量和客户数据流量）将被加密。 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>当我启用/禁用 MACsec 或更新 MACsec 密钥时，我的本地网络是否会通过 ExpressRoute 失去与 Microsoft 的连接？
是的。 对于 MACsec 配置，我们仅支持预共享密钥模式。 这意味着您需要在您的设备和 Microsoft 上（通过我们的 API）更新密钥。 此更改不是原子的，因此当两侧存在关键不匹配时，您将失去连接。 我们强烈建议您为配置更改安排维护窗口。 为了尽量减少停机时间，我们建议您在将网络流量切换到另一个链路后，一次更新 ExpressRoute Direct 的一个链路上的配置。  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>如果我的设备与 Microsoft 的 MACsec 密钥不匹配，流量是否会继续流动？
不是。 如果 MACsec 已配置，并且发生密钥不匹配，您将失去与 Microsoft 的连接。 换句话说，我们不会回到未加密的连接，公开您的数据。 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>在 ExpressRoute Direct 上启用 MACsec 是否会降低网络性能？
MACsec 加密和解密发生在我们使用的路由器上的硬件中。 对我们这边没有性能影响。 但是，您应该与网络供应商联系您使用的设备，并查看 MACsec 是否有任何性能影响。
### <a name="which-cipher-suites-are-supported-for-encryption"></a>加密支持哪些密码套件？
我们支持 AES128 和 AES256。

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec 常见问题解答的端到端加密
IPsec 是[IETF 标准](https://tools.ietf.org/html/rfc6071)。 它在 Internet 协议 （IP） 级别或网络层 3 对数据进行加密。 可以使用 IPsec 加密本地网络和 Azure 上的虚拟网络 （VNET） 之间的端到端连接。 请参阅下面的其他常见问题解答。
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>除了在快速路由直接端口上启用 MACsec 之外，我是否可以启用 IPsec？
是的。 MACsec 保护您和 Microsoft 之间的物理连接。 IPsec 保护您与 Azure 上的虚拟网络之间的端到端连接。 您可以独立启用它们。 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>我能否使用 Azure VPN 网关在本地网络和 Azure 虚拟网络之间设置 IPsec 隧道？
是的。 您可以在 ExpressRoute 电路的 Microsoft 对等互连上设置此 IPsec 隧道。 请遵循我们的[配置指南](site-to-site-vpn-over-microsoft-peering.md)。
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>我能否使用 Azure VPN 网关在 Azure 专用对等互连上设置 IPsec 隧道？
如果采用 Azure 虚拟 WAN，可以按照[以下步骤](../virtual-wan/vpn-over-expressroute.md)加密端到端连接。 如果您有常规的 Azure VNET，则可以在 VNET 中部署第三方 VPN 网关，并在它和本地 VPN 网关之间建立 IPsec 隧道。
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>在 ExpressRoute 连接上启用 IPsec 后，您将获得哪些吞吐量？
如果使用 Azure VPN 网关，请[在此处检查性能编号](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 如果使用第三方 VPN 网关，请与供应商联系以获取性能编号。

## <a name="next-steps"></a>后续步骤
有关 MACsec 配置的详细信息，请参阅[配置 MACsec。](expressroute-howto-macsec.md)

有关 IPsec 配置的详细信息，请参阅[配置 IPsec。](site-to-site-vpn-over-microsoft-peering.md)
