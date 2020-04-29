---
title: 使用 Azure 虚拟广域网和安全中心与中国互连
description: 了解有关虚拟 WAN 自动化可缩放的分支到分支连接、可用区域和合作伙伴的信息。
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985620"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>使用 Azure 虚拟广域网和安全中心与中国互连

在观看常见汽车、制造、后勤行业或其他院校（如 embassies）时，通常是有关如何改进与中国的互连的问题。 这些改进主要与使用云服务（如 Office 365、Azure 全局服务或中国内部的互连分支）结合使用，后者具有客户主干。

在大多数情况下，客户正在消耗高延迟、低带宽、不稳定的连接以及与中国境外连接的高成本（例如，欧洲或美国）。

这些由来已久的原因是 "中国的优秀防火墙"，它保护 Internet 中文部分并筛选到中国的流量。 从中国大陆到中国以外的所有流量几乎都是从中国大陆到中国以外的其他区域除外 通过香港和澳门运行的流量不会完全强制地达到出色的防火墙，而是由优秀防火墙的一个子集来处理。

![提供商互连](./media/interconnect-china/provider.png)

通过使用虚拟 WAN，客户可以与 Microsoft 云服务建立更高的性能和稳定的连接，并与企业网络建立连接，而无需打破网络安全定律。

## <a name="requirements-and-workflow"></a><a name="requirements"></a>要求和工作流

如果要遵守中国网络安全法，需要满足一组特定条件。

首先，需要与拥有适用于中国的 ICP （Internet 内容提供商）许可证的网络和 ISP 合作。 在大多数情况下，您将得到以下提供商之一：

* 中国电信全球有限公司。
* 中国 Mobile 有限公司。
* 中国 Unicom 有限公司。
* PCCW 全球有限公司
* 香港电信公司

根据提供程序和需求，你现在需要购买以下网络连接服务之一，以便在中国内互连你的分支。

* MPLS/IPVPN 网络 
* 软件定义的 WAN （SDWAN）
* 专用 Internet 访问

接下来，您需要同意该提供商才能向 Microsoft 全球网络及其边缘网络流入量香港特别行政区，而不是在北京或上海。 在这种情况下，香港特别行政区非常重要，因为它的物理连接和到中国的位置。

大多数客户认为使用新加坡进行互连是最佳方案，因为在查看地图时，这并不是真的。 按照网络光纤地图，几乎所有网络连接都会经历北京、上海和香港特别行政区。 这使得香港可以更好地在中国互连的位置选择。

根据提供程序的不同，可能会获得不同的服务产品。 下表根据编写本文时的信息显示了提供商和服务提供的服务的示例。

| 服务 | 提供程序示例 |
| --- | --- |
| MPLS/IPVPN 网络 |PCCW，中国电信全局 |
|SDWAN| PCCW，中国电信全局|
| 专用 Internet 访问 | PCCW、中国香港电信、中国 Mobil|

对于您的提供商，您可以同意使用以下两种解决方案中的哪一种来访问 Microsoft 全球骨干：

* 以香港的 Microsoft Azure ExpressRoute 终止。 这就是使用 MPLS/IPVPN 的情况。 目前，只有具有 ExpressRoute 的唯一 ICP 许可证提供商才是中国电信全局。 但是，如果他们利用云交换提供商（如 Megaport 或 InterCloud），他们也可以与其他提供商通信。 有关详细信息，请参阅[ExpressRoute 连接提供商](../expressroute/expressroute-locations-providers.md#partners)。

* 直接在以下某个 Internet 交换点使用专用 Internet 访问，或使用专用网络互连。

以下列表显示了香港特别行政区的 Internet 交换：

* AMS-IX 香港特别行政区
* BBIX 香港特别行政区
* Equinix 香港特别行政区
* HKIX

使用此连接时，Microsoft 服务的下一个 BGP 跃点必须是 Microsoft 自治系统编号（如 #）8075。 如果使用单个 location 或 SDWAN 解决方案，则可以选择 "连接"。

无论采用哪种方式，我们仍然建议你将第二个常规 Internet 分类为中国大陆。 这是为了将企业流量之间的流量拆分为云服务（如 Microsoft 365 和 Azure）以及按法律管控的 Internet 流量。

中国内符合标准的网络体系结构可能类似于以下示例：

![多个分支](./media/interconnect-china/multi-branch.png)

在此示例中，与 Microsoft Global 网络流入量中国香港地区的互连，现在可以开始利用[Azure 虚拟 Wan 全局传输体系结构](virtual-wan-global-transit-network-architecture.md)和其他服务（例如 azure 安全的虚拟 wan 集线器）来使用中国以外的分支和数据中心的服务和互连。

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>集线器到中心通信

在本部分中，我们将使用虚拟 WAN 集线器到中心通信进行互连。 在此方案中，你将创建新的虚拟 WAN 集线器资源以连接到香港特别行政区的虚拟 WAN 中心、你喜欢的其他区域、你已有 Azure 资源的区域或要连接的位置。

示例结构可能如下例所示：

![示例 WAN](./media/interconnect-china/sample.png)

在此示例中，中国分支通过使用 VPN 或 MPLS 连接连接到 Azure 云中国。 需要连接到全局服务的分支使用与直接连接到香港特别行政区的 MPLS 或基于 Internet 的服务。 如果要在香港和其他区域使用 ExpressRoute，则需要将[expressroute Global Reach](../expressroute/expressroute-global-reach.md)配置为互连这两种 ExpressRoute 线路。

ExpressRoute Global Reach 在某些区域中不可用。 例如，如果需要与巴西或印度互连，则需要利用[云 Exchange 提供商](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)提供路由服务。

下图显示了这种情况的示例。

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>适用于 Office 365 的安全 Internet 分类

另一个注意事项是网络安全，并记录中国和虚拟 WAN 建立的主干组件以及客户主干之间的入口点。 在大多数情况下，需要将香港网络中的 Internet 分类为直接访问 Microsoft Edge 网络，使用 Azure 前门服务器 Microsoft 365 服务。

对于使用虚拟 WAN 的两种方案，都可以利用[Azure 虚拟 wan 保护中心](../firewall-manager/secured-virtual-hub.md)。 使用 Azure 防火墙管理器，你可以将常规虚拟 WAN 集线器更改为受保护的中心，然后在该中心内部署和管理 Azure 防火墙。

下图显示了此方案的示例：

![Web 和 Microsoft 服务流量的 Internet 分类](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>体系结构和流量流

根据你的选择，如果与中国香港的连接相同，则整体体系结构可能会略有变化。 本部分介绍了与 VPN 或 SDWAN 和/或 ExpressRoute 不同组合的三个可用体系结构。

所有这些选项都使用 Azure 虚拟广域网保护的集线器在中国香港 M365 连接。 这些体系结构还支持[Office 365 多地理](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo)位置的符合性要求，并在下一个 Office 365 前门位置附近保存该流量。 因此，它也是对中国 Microsoft 365 的使用的改进。

将 Azure 虚拟 WAN 与 Internet 连接一起使用时，每个连接都可以受益于其他服务，例如[Microsoft Azure 对等互连服务（MAPS）](https://docs.microsoft.com/azure/peering-service/about)。 构建映射是为了优化来自第三方 Internet 服务提供商的、来自 Microsoft 全球网络的流量。

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>选项1： SDWAN 或 VPN

本部分讨论了使用 SDWAN 或 VPN 到香港和其他分支的设计。 此选项显示在虚拟 WAN 主干的两个站点上使用纯 Internet 连接时的使用和流量流。 在这种情况下，将使用专用的 Internet 访问或 ICP 提供商 SDWAN 解决方案将连接置于香港特别行政区。 其他分支也使用纯 Internet 或 SDWAN 解决方案。

![中国到香港的流量](./media/interconnect-china/china-traffic.png)

在此体系结构中，每个站点都通过使用 VPN 和 Azure 虚拟广域网连接到 Microsoft 全球网络。 站点和香港特别行政区之间的流量将在 Microsoft 网络通过传输，并在最后一英里只使用常规 Internet 连接。

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>选项2： ExpressRoute 和 SDWAN 或 VPN

本部分讨论了在中国香港使用 ExpressRoute 的设计以及使用 VPN/SDWAN 分支的其他分支。 此选项显示在香港和通过 SDWAN 或 VPN 连接的其他分支中终止的使用和 ExpressRoute。 中国香港的 ExpressRoute 目前仅限于提供商的简短列表，你可以在[快速路由伙伴](../expressroute/expressroute-locations-providers.md#global-commercial-azure)列表中找到该列表。

![中国到中国香港的流量 ExpressRoute](./media/interconnect-china/expressroute.png)

还提供了用于从中国终止 ExpressRoute 的选项，例如，韩国或日本。 但鉴于法规遵从性、法规和延迟，香港特别行政区目前是最佳选择。

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>选项3：仅 ExpressRoute

本部分介绍了一个设计，其中 ExpressRoute 用于香港和其他分支。 此选项显示在两端使用 ExpressRoute 的互连。 这里有不同的流量流。 Microsoft 365 流量将流向 Azure 虚拟 WAN 安全中心，并从该处流向 Microsoft Edge 网络和 Internet。

转到相互连接的分支或从其连接到中国地区的流量将在该体系结构中采用不同的方法。 目前，虚拟 WAN 不支持 ExpressRoute 到 ExpressRoute 传输。 流量将利用 ExpressRoute Global Reach 或第三方互连，无需传递虚拟 WAN 集线器。 它将直接从一个 Microsoft 企业边缘（MSEE）流动到另一个 Microsoft 企业边缘（）。

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

当前 ExpressRoute Global Reach 在每个国家/地区不可用，但你可以使用 Azure 虚拟 WAN 配置解决方案。

例如，你可以使用 Microsoft 对等互连配置 ExpressRoute，并将 VPN 隧道通过该对等互连连接到 Azure 虚拟 WAN。 现在，你已再次启用了 VPN 和 ExpressRoute 之间的传输，而没有 Global Reach 和第三方提供商和服务，例如 Megaport Cloud。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

* [Azure 虚拟 WAN 的全局传输网络体系结构](virtual-wan-global-transit-network-architecture.md)

* [创建虚拟 WAN 集线器](virtual-wan-site-to-site-portal.md)

* [配置虚拟广域网集线器](../firewall-manager/secure-cloud-network.md)

* [Azure 对等互连服务预览版概述](https://docs.microsoft.com/azure/peering-service/about)
