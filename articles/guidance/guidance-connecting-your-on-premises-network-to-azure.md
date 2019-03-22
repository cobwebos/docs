---
title: 你的本地网络连接到 Azure |Microsoft Docs
description: 介绍并比较用于连接到 Azure、 Office 365 和 Dynamics CRM Online 等 Microsoft 云服务可用的不同方法。
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115030"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>你的本地网络连接到 Azure
Microsoft 提供了几种类型的云服务。 虽然可以通过公共 Internet 连接到所有服务，你还可以连接到的某些服务使用虚拟专用网络 (VPN) 隧道通过 Internet 或通过直接的专用连接到 Microsoft。 本文可帮助您确定哪种连接选项最符合您的需要基于你使用的 Microsoft 云服务的类型。 大多数组织使用多个连接类型，如下所述。

## <a name="connecting-over-the-public-internet"></a>通过公共 Internet 连接
此连接类型直接通过 Internet，提供对 Microsoft 云服务的访问，如下所示。

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

此连接通常是用于连接到 Microsoft 云服务的第一个类型。 下表列出了此连接类型的优点和缺点。

| **优点** | **注意事项** |
| --- | --- |
| 需要到你的本地网络没有进行修改，前提是所有客户端设备具有无限制地的访问所有 IP 地址和在 Internet 上的端口。 |流量通常使用 HTTPS 进行加密，但它可以截获在传输过程中这是因为它会遍历公共 Internet。 |
| 可以连接到公共 Internet 上公开的所有 Microsoft 云服务。 |不可预测延迟因为连接遍历 Internet。 |
| 使用你现有的 Internet 连接。 | |
| 不需要任何连接设备的管理。 | |

此连接有没有连接或带宽成本，因为使用你现有的 Internet 连接。

## <a name="connecting-with-a-point-to-site-connection"></a>使用点到站点连接进行连接
此连接类型，如下所示，通过 Internet，提供对安全套接字隧道协议 (SSTP) 隧道通过一些 Microsoft 云服务的访问。

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "点到站点连接")

连接通过现有的 Internet 连接，但需要使用 Azure VPN 网关。 下表列出了此连接类型的优点和缺点。

| **优点** | **注意事项** |
| --- | --- |
| 需要到你的本地网络没有进行修改，前提是所有客户端设备具有无限制地的访问所有 IP 地址和在 Internet 上的端口。 |流量使用 IPSec 进行加密，但它可以截获在传输过程中这是因为它会遍历公共 Internet。 |
| 使用你现有的 Internet 连接。 |不可预测延迟因为连接遍历 Internet。 |
| 高达 200 Mb/秒每个网关的吞吐量。 |需要创建和管理单独的本地网络上每个设备和每个设备需要连接到每个网关之间的连接。 |
| 可用来连接到 Azure 服务可以连接到 Azure 虚拟网络 (VNet) 的 Azure 虚拟机和 Azure 云服务等。 |需要最少的日常管理的 Azure VPN 网关。 |
| 不能用于连接到 Microsoft Office 365 或 Dynamics CRM Online。 | |
| 不能用于连接到不能连接到 VNet 的 Azure 服务。 | |

详细了解如何[VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)服务，其[定价](https://azure.microsoft.com/pricing/details/vpn-gateway)，和出站数据传输[定价](https://azure.microsoft.com/pricing/details/data-transfers)。

## <a name="connecting-with-a-site-to-site-connection"></a>使用站点到站点连接进行连接
此连接类型，如下所示，通过 Internet，提供对通过 IPSec 隧道一些 Microsoft 云服务的访问。

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "站点到站点连接")

连接通过现有的 Internet 连接，但需要使用具有其相关的定价和出站数据传输定价的 Azure VPN 网关。 下表列出了此连接类型的优点和缺点。

| **优点** | **注意事项** |
| --- | --- |
| 在本地网络上的所有设备可与 Azure 服务，因此无需配置各个连接的每个设备连接到 VNet 都通信。 |流量使用 IPSec 进行加密，但它可以截获在传输过程中这是因为它会遍历公共 Internet。 |
| 使用你现有的 Internet 连接。 |不可预测延迟因为连接遍历 Internet。 |
| 可用于连接到可以连接到虚拟机等 VNet 的 Azure 服务和云服务。 |必须配置和管理已验证 VPN 设备 * 在本地。 |
| 高达 200 Mb/秒每个网关的吞吐量。 |需要最少的日常管理的 Azure VPN 网关。 |
| 可以强制出站流量通过本地网络以进行检查和日志记录使用用户定义的路由或边界网关协议 (BGP) 的云虚拟机从启动 * *。 |不能用于连接到 Microsoft Office 365 或 Dynamics CRM Online。 |
| 不能用于连接到不能连接到 VNet 的 Azure 服务。 | |
| 如果使用启动返回到的本地设备的连接的服务，并且你的安全策略需要它，您可能需要在本地网络与 Azure 之间的防火墙。 | |

* * 查看一系列[验证的 VPN 设备](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)。
* * * 了解有关使用详细[用户定义的路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)或[BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)以强制从 Azure Vnet 到本地设备的路由。

## <a name="connecting-with-a-dedicated-private-connection"></a>使用专用连接进行连接
此连接类型访问所有 Microsoft 云服务通过专用连接向 Microsoft 提供，不会遍历 Internet，如下所示。

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute 连接")

连接需要使用 ExpressRoute 服务和连接提供程序的连接。 下表列出了此连接类型的优点和缺点。

| **优点** | **注意事项** |
| --- | --- |
| 不能截获流量在传输过程中通过公共 Internet 由于使用通过服务提供商的专用的连接。 |需要在本地路由器管理。 |
| 高达每个 ExpressRoute 线路和吞吐量高达 2 Gb/秒到每个网关的 10 Gb/秒的带宽。 |需要连接提供程序的专用的连接。 |
| 可预测的延迟因为它是与 Microsoft 不会遍历 Internet 的专用的连接。 |（如果将线路连接到 Vnet），可能需要最小的一个或多个 Azure VPN 网关正在进行的管理。 |
| 如果需要，可以加密流量，但不需要加密的通信。 |如果您使用启动返回到的本地设备的连接的云服务，则可能需要在本地网络与 Azure 之间的防火墙。 |
| 可以直接连接到所有 Microsoft 云服务，使用几个异常 *。 |需要的服务，不能连接到 VNet.* * 输入的 Microsoft 数据中心的本地 IP 地址的网络地址转换 (NAT) |
| 可以强制通过本地网络以进行检查和日志记录使用 BGP 的云虚拟机从启动的出站流量。 | |

* * 查看[的服务列表](../expressroute/expressroute-faqs.md#supported-services)能与 ExpressRoute 一起使用。 必须批准你的 Azure 订阅连接到 Office 365。  请参阅[适用于 Office 365 的 Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1)一文，了解详细信息。
* * * 了解有关 ExpressRoute 的详细信息[NAT](../expressroute/expressroute-nat.md)要求。

详细了解如何[ExpressRoute](../expressroute/expressroute-introduction.md)，其关联[定价](https://azure.microsoft.com/pricing/details/expressroute)，并[连接提供商](../expressroute/expressroute-locations.md#locations)。

## <a name="additional-considerations"></a>其他注意事项
* 上面的选项的几个具有各种它们的 VNet 的连接、 网关连接和其他条件都可以支持的最大限制。 建议查看 Azure[网络限制](../azure-subscription-service-limits.md#networking-limits)若要了解是否其中任何一个会影响你选择使用的连接类型。
* 如果你打算将网关通过站点到站点 VPN 连接连接到同一 VNet ExpressRoute 网关，你应熟悉重要约束第一次。 请参阅[配置 ExpressRoute 和站点到站点并存连接](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations)一文，了解更多详细信息。

## <a name="next-steps"></a>后续步骤
以下资源介绍了如何实现本文中介绍的连接类型。

* [实现的点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [实现站点到站点连接](guidance-hybrid-network-vpn.md)
* [实现的专用连接](guidance-hybrid-network-expressroute.md)
* [实现高可用性的站点到站点连接使用的专用连接](guidance-hybrid-network-expressroute-vpn-failover.md)
