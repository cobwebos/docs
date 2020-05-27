---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "67172771"
---
VNet 到 VNet 连接常见问题解答适用于 VPN 网关连接。 有关 VNet 对等互连的信息，请参阅[虚拟网络对等互连](../articles/virtual-network/virtual-network-peering-overview.md)。

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Azure 会对 VNet 之间的流量收费吗？

当使用 VPN 网关连接时，同一区域中的 VNet 到 VNet 流量双向均免费。 跨区域 VNet 到 VNet 传出流量根据源区域的出站 VNet 间数据传输费率收费。 有关详细信息，请参阅 [VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway/)。 如果你使用 VNet 对等互连而非 VPN 网关连接 VNet，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)。

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>VNet 到 VNet 流量是否流经 Internet？

不是。 VNet 到 VNet 流量通过 Microsoft Azure 主干而不是 Internet 传输。

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>是否可以跨 Azure Active Directory (AAD) 租户建立 VNet 到 VNet 连接？

是的。使用 Azure VPN 网关的 VNet 到 VNet 连接可以跨 AAD 租户工作。

### <a name="is-vnet-to-vnet-traffic-secure"></a>VNet 到 VNet 通信安全吗？

安全，它通过 IPsec/IKE 加密进行保护。

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>是否需要 VPN 设备将 VNet 连接到一起？

不是。 将多个 Azure 虚拟网络连接在一起不需要 VPN 设备，除非需要跨界连接。

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>我的 VNet 是否需要位于同一区域？

不是。 虚拟网络可以在相同或不同的 Azure 区域（位置）中。

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>如果 VNet 不在同一订阅中，订阅是否需要与同一 Active Directory 租户相关联？

不是。

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>能否在单独的 Azure 实例中使用 VNet 到 VNet 通信来连接虚拟网络？ 

不是。 VNet 到 VNet 通信支持在同一 Azure 实例中连接虚拟网络。 例如，不能在全球 Azure 和中国/德国/美国政府 Azure 实例之间创建连接。 对于上述情形，请考虑使用站点到站点 VPN 连接。

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>能否将 VNet 到 VNet 用于多站点连接？

是的。 虚拟网络连接可与多站点 VPN 同时使用。

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>一个虚拟网络可以连接到多少个本地站点和虚拟网络？

请参阅[网关要求](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)表。

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>能否使用 VNet 到 VNet 来连接 VNet 外部的 VM 或云服务？

不是。 VNet 到 VNet 通信支持连接虚拟网络。 它不支持连接不在虚拟网络中的虚拟机或云服务。

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>云服务或负载均衡终结点能否跨 VNet？

不是。 云服务或负载均衡终结点不能跨虚拟网络，即使它们连接在一起，也是如此。

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>能否将 PolicyBased VPN 类型用于 VNet 到 VNet 连接或多站点连接？

不是。 VNet 到 VNet 连接和多站点连接需要 RouteBased（以前称为动态路由）VPN 类型的 Azure VPN 网关。

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>是否可以将 RouteBased VPN 类型的 VNet 连接到另一个 PolicyBased VPN 类型的 VNet？

不能，两种虚拟网络都必须使用基于路由的（以前称为“动态路由”）VPN。

### <a name="do-vpn-tunnels-share-bandwidth"></a>VPN 隧道是否共享带宽？

是的。 虚拟网络的所有 VPN 隧道共享 Azure VPN 网关上的可用带宽，以及 Azure 中的相同 VPN 网关运行时间 SLA。

### <a name="are-redundant-tunnels-supported"></a>是否支持冗余隧道？

将一个虚拟网络网关配置为主动-主动模式时，支持在一对虚拟网络之间使用冗余隧道。

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>对于 VNet 到 VNet 配置，能否使用重叠地址空间？

不是。 不能有重叠的 IP 地址范围。

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>连接的虚拟网络与内部本地站点之间能否存在重叠的地址空间？

不是。 不能有重叠的 IP 地址范围。



