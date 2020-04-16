---
title: Azure 虚拟 WAN 概述 | Microsoft Docs
description: 了解有关虚拟 WAN 自动化可缩放的分支到分支连接、可用区域和合作伙伴的信息。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 927c09f61ce0847c72cefb51935116070e956861
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743105"
---
# <a name="about-azure-virtual-wan"></a>关于 Azure 虚拟 WAN

Azure 虚拟 WAN 是一个网络服务，其中整合了多种网络、安全和路由功能，提供单一操作界面。 这些功能包括分支连接（通过 SD-WAN 或 VPN CPE 等虚拟 WAN 合作伙伴设备的连接自动化）、站点到站点 VPN 连接、远程用户 VPN（点到站点）连接、专用 (ExpressRoute) 连接、云内部连接（虚拟网络的可中转连接）、VPN ExpressRoute 互连、路由、Azure 防火墙、专用连接加密等。无需所有这些用例即可开始使用虚拟 WAN。 只需准备一个用例并在网络不断变化时对网络进行调整，即可开始使用虚拟 WAN。 虚拟 WAN 体系结构是一种内置了规模和性能的中心辐射型体系结构，适用于分支（VPN/SD-WAN 设备）、用户（Azure VPN/OpenVPN/IKEv2 客户端）、ExpressRoute 线路和虚拟网络。 它支持[全球传输网络体系结构](virtual-wan-global-transit-network-architecture.md)，其中的云托管网络“中心”支持可能分布在不同“辐射”类型上的终结点之间的传输连接。

Azure 区域充当可供连接的中心。 所有中心均在标准虚拟 WAN 中以完整网格的形式进行连接，使得用户能够轻松地使用 Microsoft 主干进行任意分支到任意分支的连接。 对于包含 SD-WAN/VPN 设备的辐射连接，用户可以在 Azure 虚拟 WAN 中手动设置该连接，或者使用虚拟 WAN CPE (SD-WAN/VPN) 合作伙伴解决方案来设置与 Azure 的连接。 我们提供了一个列表，其中的合作伙伴支持使用 Azure 虚拟 WAN 实现连接自动化（能够将设备信息导出到 Azure、下载 Azure 配置和建立连接）。 有关详细信息，请参阅[虚拟 WAN 合作伙伴和位置](virtual-wan-locations-partners.md)一文。 

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwan1.png)

本文提供 Azure 虚拟 WAN 中网络连接的快速视图。 虚拟 WAN 提供以下优势：

* **中心和辐射中的集成式连接解决方案：** 在本地站点与 Azure 中心之间自动建立站点到站点配置和连接。
* **自动化的辐射设置和配置：** 将虚拟网络和工作负载无缝连接到 Azure 中心。
* **直观的疑难解答：** 可以查看 Azure 中的端到端流，并使用此信息来执行所需的操作。

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>基本和标准虚拟 WAN

虚拟 WAN 分为两种类型：“基本”和“标准”。 下表显示了每种类型的可用配置。

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

有关升级虚拟 WAN 的步骤，请参阅 [Upgrade a virtual WAN from Basic to Standard](upgrade-virtual-wan.md)（从基本虚拟 WAN 升级到标准虚拟 WAN）。

## <a name="architecture"></a><a name="architecture"></a>体系结构

有关虚拟 WAN 体系结构以及如何迁移到虚拟 WAN 的信息，请参阅以下文章：

* [虚拟 WAN 体系结构](migrate-from-hub-spoke-topology.md)
* [全局传输网络体系结构](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>虚拟 WAN 资源

若要配置端到端虚拟 WAN，请创建以下资源：

* **virtualWAN：** virtualWAN 资源表示 Azure 网络的虚拟覆盖，并且是多个资源的集合。 它包含要部署到虚拟 WAN 中的所有虚拟中心的链接。 虚拟 WAN 资源相互隔离，不能包含公用中心。 跨虚拟 WAN 的虚拟中心不会相互通信。

* **中心：** 虚拟中心是 Microsoft 托管的虚拟网络。 中心包含用于建立连接的不同服务终结点。 从本地网络 (vpnsite)，你可以连接到虚拟中心内的 VPN 网关，将 ExpressRoute 线路连接到虚拟中心，甚至可以将移动用户连接到虚拟中心中的点到站点网关。 中心是区域中网络的核心。 每个 Azure 区域只能有一个中心。

  中心网关与用于 ExpressRoute 和 VPN 网关的虚拟网络网关不同。 例如，使用虚拟 WAN 时，不要直接从本地站点创建与 VNet 的站点到站点连接。 而应与中心建立站点到站点连接。 流量始终通过中心网关。 这意味着，VNet 不需要自身的虚拟网络网关。 虚拟 WAN 可让 VNet 通过虚拟中心和虚拟中心网关轻松利用缩放功能。

* **中心虚拟网络连接：** 中心虚拟网络连接资源用于将中心无缝连接到虚拟网络。

* **（预览）中心到中心连接** - 中心都在虚拟 WAN 中彼此连接。 这意味着连接到本地中心的分支、用户或 VNet 可以使用连接中心的完整网格体系结构与另一个分支或 VNet 通信。 还可以使用中心到中心连接框架连接通过虚拟中心传输的中心内的 VNet，以及跨中心的 VNet。

* **中心路由表：** 可以创建一个虚拟中心路由，并将该路由应用于虚拟中心路由表。 可以将多个路由应用于虚拟中心路由表。

**其他虚拟 WAN 资源**

  * **站点：** 此资源仅用于站点到站点连接。 站点资源为 vpnsite  。 它表示本地 VPN 设备及其设置。 可以通过与虚拟 WAN 合作伙伴合作，使用一个内置的解决方案自动将此信息导出到 Azure。

## <a name="types-of-connectivity"></a><a name="connectivity"></a>连接类型

虚拟 WAN 允许以下类型的连接：站点到站点 VPN、用户 VPN（点到站点）和 ExpressRoute。

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>站点到站点 VPN 连接

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwan.png)

创建虚拟 WAN 站点到站点连接时，可与可用的合作伙伴协作。 如果不想使用合作伙伴，则可以手动配置连接。 有关详细信息，请参阅[使用虚拟 WAN 创建站点到站点连接](virtual-wan-site-to-site-portal.md)。

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>虚拟 WAN 合作伙伴工作流

与虚拟 WAN 合作伙伴合作时，此工作流包括：

1. 分支设备 (VPN/SDWAN) 控制器经过身份验证后，使用 [Azure 服务主体](../active-directory/develop/howto-create-service-principal-portal.md)将站点导向型信息导出到 Azure。
2. 分支设备 (VPN/SDWAN) 控制器获取 Azure 连接配置并更新本地设备。 这样可以自动完成配置下载，以及本地 VPN 设备的编辑和更新。
3. 设备获得正确的 Azure 配置后，将与 Azure WAN 建立站点到站点连接（两个活动隧道）。 Azure 同时支持 IKEv1 和 IKEv2。 BGP 是可选的。

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>站点到站点虚拟 WAN 连接的合作伙伴

有关可用的合作伙伴和位置的列表，请参阅[虚拟 WAN 合作伙伴和位置](virtual-wan-locations-partners.md)一文。

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>用户 VPN（点到站点）连接

通过 IPsec/IKE (IKEv2) 或 OpenVPN 连接可以连接到 Azure 中的资源。 此类连接要求在客户端计算机上配置一个 VPN 客户端。 有关详细信息，请参阅[创建点到站点连接](virtual-wan-point-to-site-portal.md)。

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute 连接
ExpressRoute 允许通过专用连接将本地网络连接到 Azure。 要创建连接，请参阅[使用虚拟 WAN 创建 ExpressRoute 连接](virtual-wan-expressroute-portal.md)。

## <a name="locations"></a><a name="locations"></a>位置

有关位置信息，请参阅[虚拟 WAN 合作伙伴和位置](virtual-wan-locations-partners.md)一文。

## <a name="faq"></a><a name="faq"></a>常见问题解答

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>后续步骤

[使用虚拟 WAN 创建站点到站点连接](virtual-wan-site-to-site-portal.md)
