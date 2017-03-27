---
title: "Azure 政府版网络 |Microsoft Docs"
description: "这提供了 e Government 专业连接的功能和指南比较"
services: azure-government
cloud: gov
documentationcenter: 
author: jawalte
manager: zakramer
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/24/2017
ms.author: jawalte
translationtype: Human Translation
ms.sourcegitcommit: 8288ca2e85ec0cd1a1b53862b2d60fcb88e7be4b
ms.openlocfilehash: c6b8c27299c1bfceb045ee7d82981654f3a446d8


---
# <a name="azure-government-networking"></a>Azure Government 网络
## <a name="expressroute-private-connectivity"></a>ExpressRoute（专用连接）
ExpressRoute 已在 Azure Government 中正式发布。 有关详细信息（包括合作伙伴和对等位置），请参阅 [ ExpressRoute 公共文档 ](../expressroute/index.md)。

### <a name="variations"></a>变体
ExpressRoute 已在 Azure Government 中正式发布 (GA)。 

* Government 客户通过专用的 Azure Government (Gov) ExpressRoute (ER) 连接连接到实际独立的容量
* Azure Gov 通过利用多个位于最近 500 英里之外的区域对提高了可用性和持久性 
* 默认情况下，所有 Azure Gov ER 连接通过支持爆发配置主动-主动冗余，并提供高达 10G 的线路容量（最小为 50MB）
* Azure Gov ER 位置为客户和 Azure Gov 地域冗余区域提供优化的路径（最短跃点、低延迟、高性能等）
* Azure Gov ER 专用连接不使用、遍历或依赖 Internet
* Azure Gov 物理和逻辑基础结构实际上是专用且分隔的，并且仅限美国客户访问
* Microsoft 拥有并操作 Azure Gov 区域和 Azure Gov ER Meet-Me 位置之间的所有光纤基础结构
* Azure Gov ER 提供与 Microsoft Azure、O365 和 CRM 云服务的连接

### <a name="considerations"></a>注意事项
有两个基本服务提供到 Azure Government 的专用网络连接：VPN（典型组织的站点到站点）和 ExpressRoute。

Azure ExpressRoute 用于在 Azure Government 数据中心和你的本地基础结构或共置环境之间创建专有连接。 ExpressRoute 连接并不绕过公共 Internet—与典型的 Internet 连接相比，它的可靠性更高、速度更快且延迟时间更短。 在某些情况下，使用 ExpressRoute 连接在本地系统和 Azure 之间传输数据可以带来显著的成本效益。   

使用 ExpressRoute，你可以在 ExpressRoute 位置（如 Exchange 提供商设施）建立与 Azure 的连接，或从现有 WAN 网络（例如多协议标签交换 (MPLS) VPN）直接连接到 Azure。

![替换文字](./media/azure-government-capability-private-connectivity-options.PNG)  ![替换文字](./media/government-capability-expressroute.PNG)  

对于支持 Azure Government 客户应用程序和解决方案的网络服务，强烈建议实现 ExpressRoute（专用连接）以连接到 Azure Government。 如果使用 VPN 连接，则要考虑以下事项：

* 客户应联系其授权官方/机构以确定是否需要专用连接或其他安全连接机制，并确定要考虑的任意其他限制。
* 客户应决定是否要强制将站点到站点 VPN 通过专用连接区域路由。
* 客户应使用授权的专用连接访问提供程序来获取 MPLS 线路或 VPN。

所有使用专用连接体系结构的客户都应验证已建立相应的实施，并为到 Azure Government 的网关网络 /Internet (GN/I) 边缘路由器分界点的客户连接进行维护。 同样，你的组织必须在 Azure Government 的本地环境和网关网络/客户 (GN/C) 边缘路由器分界点之间建立网络连接。

## <a name="support-for-bgp-communities"></a>BGP 社区支持
本部分概述如何在 AzureGov 中配合 ExpressRoute 使用 BGP 社区。 Microsoft 将播发公共和 Microsoft 对等互连路径中的路由并为路由标记适当的社区值。 下面将会介绍这种方案的理由以及有关社区值的详细信息。 但是，Microsoft 不遵循向 Microsoft 播发的路由的任何标记社区值。

如果要在 AzureGov 区域内的任何一个对等互连位置通过 ExpressRoute 连接到 Microsoft，就必须能够访问该政府边界内所有区域中的所有 Microsoft 云服务。 

例如，如果要通过 ExpressRoute 连接到华盛顿特区的 Microsoft， 则必须能够访问在 AzureGov 中托管的所有 Microsoft 云服务。

请参阅 [ExpressRoute 公共文档](../expressroute/index.md)上的“概述”选项卡，了解有关位置和合作伙伴的详细信息，并获取针对 AzureGov 对等互连位置的 ExpressRoute 的详细列表。

可以购买多个 ExpressRoute 线路。 如果拥有多个连接，则可以从异地冗余中获得明显的高可用性优势。 如果你多条 ExpressRoute 线路，将从 Microsoft 收到同一组公共对等互连和 Microsoft 对等互连路径的前缀。 这意味着你可以使用多个路径从你的网络接入 Microsoft。 这可能会导致在网络中做出欠佳的路由决策。 因此，你可能会在不同的服务上遇到欠佳的连接体验。 

Microsoft 使用适当的 BGP 社区值（表示托管前缀的区域）来标记通过公共对等互连和 Microsoft 对等互连播发的前缀。 可以依赖社区值做出适当的路由决策，以向客户提供最佳路由。  如需其他详细信息，请参阅 [ExpressRoute 公共文档](../expressroute/index.md)上的“入门”选项卡，然后单击“优化路由”。

| **国家云 Azure 区域**| **BGP 社区值** |
| --- | --- |
| **美国政府** |  |
| 美国政府爱荷华州 | 12076:51109 |
| 美国政府弗吉尼亚州 | 12076:51105 |

所有 Microsoft 播发的路由都标有适当的社区值。 

除了上述各项，Microsoft 还将根据其所属的服务加上标记及前缀。 这只适用于 Microsoft 对等互连。 下表提供了服务与 BGP 社区值之间的映射。

| **国家云中的服务** | **BGP 社区值** |
| --- | --- |
| **美国政府** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype For Business Online |12076:5130 |
| CRM Online |12076:5140 |
| 其他 Office 365 Online 服务 |12076:5200 |

> [!NOTE]
> Microsoft 不遵循你在播发到 Microsoft 的路由上设置的任何 BGP 社区值。

## <a name="support-for-load-balancer"></a>对负载均衡器的支持
负载均衡器已在 Azure Government 中正式发布。 有关详细信息，请参阅[负载均衡器公共文档](../load-balancer/load-balancer-overview.md)。 

## <a name="support-for-traffic-manger"></a>对流量管理器的支持
流量管理器已在 Azure Government 中正式发布。 有关详细信息，请参阅[流量管理器公共文档](../traffic-manager/traffic-manager-overview.md)。 

## <a name="support-for-vnet-peering"></a>对 VNet 对等互连的支持 
VNet 对等互连已在 Azure 政府版中正式发布。 有关详细信息，请参阅 [VNet 对等互连公共文档](../virtual-network/virtual-network-peering-overview.md)。 

## <a name="support-for-vpn-gateway"></a>对 VPN 网关的支持 
VPN 网关已在 Azure 政府版中正式发布。 有关详细信息，请参阅 [VPN 网关公共文档](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府博客</a>。




<!--HONumber=Jan17_HO5-->


