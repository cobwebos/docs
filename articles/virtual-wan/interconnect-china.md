---
title: 使用 Azure 虚拟 WAN 和安全集线器与中国互连
description: 了解有关虚拟 WAN 自动化可缩放的分支到分支连接、可用区域和合作伙伴的信息。
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985620"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>使用 Azure 虚拟 WAN 和安全集线器与中国互连

当观察常见的汽车、制造业、物流业或其他机构，如大使馆时，经常有如何改善与中国的互联的问题。 这些改进主要与使用云服务（如 Office 365、Azure 全球服务）或将中国境内的分支机构与客户骨干网连接相关。

在大多数情况下，客户都在为连接中国境外的高延迟、低带宽、不稳定的连接和高昂的成本而苦苦挣扎（例如，欧洲或美国）。

这些斗争的一个原因是"中国长城防火墙"，它保护了中国互联网的一部分，并过滤到中国的流量。 除了香港和澳门等特别行政区外，几乎所有从中国大陆到境外的交通都通过长城。 贯穿香港和澳门的流量没有完全撞上长城防火墙，由长城防火墙的一部分处理。

![提供程序互连](./media/interconnect-china/provider.png)

使用虚拟 WAN，客户可以建立更性能、更稳定的连接到 Microsoft 云服务，并在不违反中国网络安全法的情况下建立与其企业网络的连接。

## <a name="requirements-and-workflow"></a><a name="requirements"></a>要求和工作流

如果您想遵守中国网络安全法，您需要满足一系列特定条件。

首先，您需要与拥有中国 ICP（互联网内容提供商）许可证的网络和 ISP 合作。 在大多数情况下，您将最终拥有以下提供程序之一：

* 中国电信全球有限公司
* 中国移动有限公司
* 中国联通有限公司
* 电讯盈科全球有限公司
* 香港电讯有限公司

根据提供商和您的需求，您现在需要购买以下网络连接服务之一，以互连您在中国境内的分支机构。

* MPLS/IPVPN网络 
* 软件定义的 WAN （SDWAN）
* 专用互联网接入

接下来，您需要与该提供商达成一致，以便突破微软全球网络及其在香港的边缘网络，而不是在北京或上海。 在这种情况下，香港非常重要，因为它与中国有物理联系和位置。

虽然大多数客户认为使用新加坡进行互连是最好的例子，因为它在地图上看起来离中国更近，但事实并非如此。 当您按照网络光纤地图操作时，几乎所有的网络连接都经过北京、上海和香港。 这使得香港成为与中国互联的更好位置选择。

根据提供商的不同，您可能会获得不同的服务产品。 下表根据撰写本文时的信息，显示了提供程序及其提供的服务的示例。

| 服务 | 提供程序示例 |
| --- | --- |
| MPLS/IPVPN网络 |电讯盈科，中国电信全球 |
|SDWAN| 电讯盈科，中国电信全球|
| 专用互联网接入 | 电讯盈科、香港电讯、中国美孚|

通过您的提供商，您可以商定以下两种解决方案中的哪一种可用于访问 Microsoft 全球主干：

* 在香港终止 Microsoft Azure ExpressRoute。 使用MPLS/IPVPN就是这种情况。 目前，只有只有ICP牌照提供商的快递路由香港是中国电信全球。 但是，如果他们利用云交换提供商（如兆端口或 InterCloud），他们也可以与其他提供商交谈。 有关详细信息，请参阅[ExpressRoute 连接提供程序](../expressroute/expressroute-locations-providers.md#partners)。

* 直接在以下互联网交换点之一使用专用 Internet 访问，或使用专用网络互连。

以下列表显示香港可能进行互联网交易所：

* AMS-IX 香港
* BBIX 香港
* Equinix 香港
* 香港IX

使用此连接时，Microsoft 服务的下一个 BGP 跃点必须是 Microsoft 自治系统编号 （AS#） 8075。 如果使用单个位置或 SDWAN 解决方案，则是连接的选择。

无论哪种方式，我们仍然建议你有第二个和定期的互联网突破进入中国大陆。 这是为了将企业流量拆分为云服务（如 Microsoft 365 和 Azure）以及受法律监管的 Internet 流量之间的流量。

中国境内合规的网络架构可能类似于以下示例：

![多个分支](./media/interconnect-china/multi-branch.png)

在此示例中，与香港的 Microsoft 全球网络进行互连，现在可以开始利用[Azure 虚拟广域网全球传输体系结构](virtual-wan-global-transit-network-architecture.md)和其他服务（如 Azure 安全虚拟广域网中心）来使用服务并互连中国境外的分支机构和数据中心。

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>枢纽到集线器通信

在本节中，我们使用虚拟 WAN 集线器到集线器通信进行互连。 在这种情况下，您将创建新的虚拟广域网中心资源以连接到香港的虚拟广域网中心、您喜欢的其他地区、已具有 Azure 资源的区域或要连接的区域。

示例体系结构可能类似于以下示例：

![示例 WAN](./media/interconnect-china/sample.png)

在此示例中，中国分支通过使用 VPN 或 MPLS 连接连接到 Azure 云中国并相互连接。 需要连接到全球服务的分支机构使用直接连接到香港的 MPLS 或基于互联网的服务。 如果要在香港和其他区域使用 ExpressRoute，则需要配置[ExpressRoute 全球覆盖，](../expressroute/expressroute-global-reach.md)以互连两个 ExpressRoute 电路。

某些地区不提供 ExpressRoute 全球覆盖。 例如，如果您需要与巴西或印度进行互连，则需要利用[云交换提供商](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)提供路由服务。

下图显示了此方案的两个示例。

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Office 365 的安全互联网突破

另一个考虑因素是网络安全以及登录中国和虚拟广域网建立骨干组件与客户骨干网之间的入口点。 在大多数情况下，需要突破香港互联网，直接到达微软边缘网络，并因此，Azure 前门服务器用于微软365服务。

对于具有虚拟 WAN 的两种方案，您将利用[Azure 虚拟 WAN 安全集线器](../firewall-manager/secured-virtual-hub.md)。 使用 Azure 防火墙管理器，可以将常规虚拟 WAN 中心更改为安全中心，然后在该中心内部署和管理 Azure 防火墙。

下图显示了此方案的示例：

![网络和微软服务流量的互联网突破](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>体系结构和流量流

根据您对与香港连接的选择，整体架构可能会稍有改变。 本节显示了三个可用的体系结构，与 VPN 或 SDWAN 和/或 ExpressRoute 不同组合。

所有这些选项都利用 Azure 虚拟 WAN 安全集线器进行香港直接 M365 连接。 这些体系结构还支持[Office 365 多地理位置](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo)的合规性要求，并将流量保留在下一个 Office 365 前门位置附近。 因此，这也是微软365在中国使用的改进。

将 Azure 虚拟 WAN 与 Internet 连接结合使用时，每个连接都可以受益于其他服务，如[Microsoft Azure 对等服务 （MAPS）。](https://docs.microsoft.com/azure/peering-service/about) MAPS 旨在优化来自第三方互联网服务提供商的 Microsoft 全球网络流量。

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>选项 1：SDWAN 或 VPN

本节讨论一种设计，该设计使用 SDWAN 或 VPN 到香港和其他分支机构。 此选项显示在虚拟 WAN 主干的两个站点上使用纯 Internet 连接时的使用和流量流。 在这种情况下，连接通过专用互联网接入或 ICP 提供商 SDWAN 解决方案带到香港。 其他分支机构也使用纯互联网或SDWAN解决方案。

![中国到香港的交通](./media/interconnect-china/china-traffic.png)

在此体系结构中，每个站点都使用 VPN 和 Azure 虚拟 WAN 连接到 Microsoft 全球网络。 网站与香港之间的流量在微软网络传输，最后一英里只使用常规的互联网连接。

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>选项 2：快速路由和 SDWAN 或 VPN

本节讨论在香港使用 ExpressRoute 和其他具有 VPN/SDWAN 分支机构的分支机构的设计。 此选项显示在香港终止的和 ExpressRoute 的使用以及通过 SDWAN 或 VPN 连接的其他分支机构。 香港的 ExpressRoute 目前仅限于提供商的简短列表，您可以在[快速路线合作伙伴](../expressroute/expressroute-locations-providers.md#global-commercial-azure)列表中找到该列表。

![中国至香港交通快线](./media/interconnect-china/expressroute.png)

也有选择终止从中国的ExpressRoute，例如在韩国或日本。 但是，鉴于合规、监管和延迟，香港目前是最佳选择。

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>选项 3：仅限快速路由

本节讨论用于香港和其他分支机构的 ExpressRoute 的设计。 此选项在两端使用 ExpressRoute 显示互连。 在这里，您有一个不同于其他流量的流量。 Microsoft 365 流量将流向 Azure 虚拟 WAN 安全集线器，并从那里流向 Microsoft 边缘网络和 Internet。

到互联分支机构或从它们到中国位置的流量将遵循该体系结构中的不同方法。 目前虚拟广域网不支持快速路由到快速路由传输。 流量将利用 ExpressRoute 全球覆盖或第三方互连，而无需通过虚拟广域网中心。 它将直接从一个 Microsoft 企业边缘 （MSEE） 流向另一个。

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

目前，ExpressRoute 全球覆盖/地区并非在每个国家/地区都可用，但您可以使用 Azure 虚拟 WAN 配置解决方案。

例如，您可以使用 Microsoft 对等互连配置 ExpressRoute，并通过该对等互连连接到 Azure 虚拟 WAN 的 VPN 隧道。 现在，您再次启用了 VPN 和 ExpressRoute 之间的传输，没有全球覆盖和第三方提供商和服务（如兆端口云）。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

* [使用 Azure 虚拟 WAN 的全球传输网络架构](virtual-wan-global-transit-network-architecture.md)

* [创建虚拟广域网中心](virtual-wan-site-to-site-portal.md)

* [配置虚拟广域网安全集线器](../firewall-manager/secure-cloud-network.md)

* [Azure 对等服务预览概述](https://docs.microsoft.com/azure/peering-service/about)
