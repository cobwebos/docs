---
title: "ExpressRoute 概述：通过专用连接将本地网络扩展到 Azure | Microsoft Docs"
description: "此 ExpressRoute 技术概述介绍了如何使用 ExpressRoute 连接，以便用户通过专用连接将本地网络扩展到 Azure。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: cherylmc
ms.openlocfilehash: 7390462d79506e63989baadac2b2cee00eef325d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="expressroute-overview"></a>ExpressRoute 概述
Microsoft Azure ExpressRoute，你将在本地网络扩展到 Microsoft 云，通过专用连接的连接提供商可帮助实现。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。

可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在共置设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。 ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。 要了解如何使用 ExpressRoute 将网络连接到 Microsoft，请参阅 [ExpressRoute 连接模型](expressroute-connectivity-models.md)。

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>主要优点

* 通过连接服务提供商在本地网络与 Microsoft 云之间建立第 3 层连接。 可以从任意位置之间的 (IPVPN) 网络、点到点以太网，或通过以太网交换经由虚拟交叉连接来建立这种连接。
* 跨地缘政治区域中的所有区域连接到 Microsoft 云服务。
* 通过 ExpressRoute 高级版附加组件从全球连接到所有区域的 Microsoft 服务。
* 通过行业标准协议 (BGP) 在网络与 Microsoft 之间进行动态路由。
* 在每个对等位置提供内置冗余以提高可靠性。
* 连接运行时间 [SLA](https://azure.microsoft.com/support/legal/sla/)。
* Skype for Business 的 QoS 支持。

有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

## <a name="features"></a>功能

### <a name="layer-3-connectivity"></a>第 3 层连接
Microsoft 采用行业标准动态路由协议 (BGP)，在本地网络、Azure 中的实例和 Microsoft 公共地址之间交换路由。  我们根据不同的流量配置文件来与网络建立多个 BGP 会话。 有关详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md) 一文。

### <a name="redundancy"></a>冗余
每个 ExpressRoute 线路有两道连接，用于从连接服务提供商/网络边缘连接到两个 Microsoft 企业边缘路由器 (MSEE)。 Microsoft 要求从连接服务提供商/一端建立双重 BGP 连接 – 各自连接到每个 MSEE。 可以选择不要在一端部署冗余设备/以太网路线。 但是，连接服务提供商会使用冗余设备，确保以冗余方式将连接移交给 Microsoft。 冗余的第 3 层连接配置是 Microsoft [SLA](https://azure.microsoft.com/support/legal/sla/) 生效的条件。

### <a name="connectivity-to-microsoft-cloud-services"></a>与 Microsoft 云服务建立连接
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

通过 ExpressRoute 连接可访问以下服务：

* Microsoft Azure 服务
* Microsoft Office 365 服务
* Microsoft Dynamics 365

可以访问 [ExpressRoute 常见问题](expressroute-faqs.md) 页，以获取通过 ExpressRoute 支持的服务的详细列表。

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>与地缘政治区域中的所有区域建立连接
可以在我们的某个 [对等位置](expressroute-locations.md) 连接到 Microsoft，并访问该地缘政治区域中的所有区域。 

例如，如果在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft，则就能够访问在欧洲北部和欧洲西部托管的所有 Microsoft 云服务。 有关地缘政治地区、关联的 Microsoft 云区域和对应的 ExpressRoute 对等位置的概述，请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>使用 ExpressRoute 高级版附加组件建立全球连接
可以启用 ExpressRoute 高级版附加功能，将连接扩展为跨越地缘政治边界。 例如，如果在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft，则就能够访问全球所有区域托管的所有 Microsoft 云服务（不包括国家/地区云）。 就像访问欧洲北部和西部区域一样，还可以访问部署在南美洲或澳大利亚的服务。

### <a name="rich-connectivity-partner-ecosystem"></a>丰富的连接合作伙伴生态系统
ExpressRoute 的连接服务提供商和 SI 合作伙伴生态系统不断发展。 有关最新信息，请参阅 [ExpressRoute 提供商和位置](expressroute-locations.md) 一文。

### <a name="connectivity-to-national-clouds"></a>与国家/地区云建立连接
Microsoft 为特殊的地缘政治地区和客户群提供隔离的云环境。 有关国家/地区云和提供商的列表，请参阅 [ExpressRoute 提供商和位置](expressroute-locations.md) 页。

### <a name="bandwidth-options"></a>带宽选项
可以购买各种带宽的 ExpressRoute 线路。 支持的带宽列表如下。 请务必咨询连接服务提供商，以获取他们支持的带宽列表。

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>动态调整带宽
用户可以在不中断连接的情况下增大 ExpressRoute 线路带宽（尽最大努力）。 

### <a name="flexible-billing-models"></a>弹性计费模式
可以选择最适合自己的计费模式。 请从以下计费模式中选择。 有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

* **无限制数据**。 ExpressRoute 线路按月计费，所有入站和出站数据传输不收取费用。 
* **计量数据**。 ExpressRoute 线路按月计费。 所有入站数据传输免费。 出站数据传输按每 GB 数据传输计费。 数据传输费率根据区域不同而异。
* **ExpressRoute 高级版附加组件**。 ExpressRoute 高级版是 ExpressRoute 线路上的附加组件。 ExpressRoute 高级版附加组件提供以下功能： 
  * 提高 Azure 公共和 Azure 专用对等互连的路由限制，从4,000 路由提升至 10,000 路由。
  * 服务的全球连接。 在任何区域（国家/地区云除外）创建的 ExpressRoute 线路都会能够访问位于全球其他区域的资源。 例如，创建于欧洲西部的虚拟网络可以通过在硅谷设置的 ExpressRoute 线路进行访问。
  * 增加了每个 ExpressRoute 线路的 VNet 链接数量，从 10 增加至更大的限制，具体取决于线路的带宽。

## <a name="faq"></a>常见问题

有关 ExpressRoute 的常见问题，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

## <a name="next-steps"></a>后续步骤

* 了解 [ExpressRoute 连接模型](expressroute-connectivity-models.md)。
* 了解 ExpressRoute 连接和路由域。 请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 查找服务提供商。 请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
* 请参阅[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的要求。
* 配置 ExpressRoute 连接。
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)
  * [配置 ExpressRoute 线路的对等互连](expressroute-howto-routing-portal-resource-manager.md)
  * [将虚拟网络连接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)
* 了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。
