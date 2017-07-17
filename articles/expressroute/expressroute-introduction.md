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
ms.date: 07/05/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: d1e513933ea647a1afe9a4eb214bb9216d3bb20a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# ExpressRoute 概述
<a id="expressroute-overview" class="xliff"></a>
Microsoft Azure ExpressRoute 可让你通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。

可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在共置设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。 ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。 若要了解如何使用 ExpressRoute 将网络连接到 Microsoft，请参阅 [ExpressRoute 连接模型](expressroute-connectivity-models.md)。

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## 主要优点
<a id="key-benefits" class="xliff"></a>

* 通过连接服务提供商在本地网络与 Microsoft 云之间建立第 3 层连接。 可以从任意位置之间的 (IPVPN) 网络、点到点以太网，或通过以太网交换经由虚拟交叉连接来建立这种连接。
* 跨地缘政治区域中的所有区域连接到 Microsoft 云服务。
* 通过 ExpressRoute 高级版附加组件从全球连接到所有区域的 Microsoft 服务。
* 通过行业标准协议 (BGP) 在你的网络与 Microsoft 之间进行动态路由。
* 在每个对等位置提供内置冗余以提高可靠性。
* 连接运行时间 [SLA](https://azure.microsoft.com/support/legal/sla/)。
* Skype for Business 的 QoS 支持。

有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

## 功能
<a id="features" class="xliff"></a>

### 第 3 层连接
<a id="layer-3-connectivity" class="xliff"></a>
Microsoft 采用行业标准动态路由协议 (BGP)，在本地网络、Azure 中的实例和 Microsoft 公共地址之间交换路由。  我们根据不同的流量配置文件来与网络建立多个 BGP 会话。 有关详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md) 一文。

### 冗余
<a id="redundancy" class="xliff"></a>
每个 ExpressRoute 线路有两道连接，用于从连接服务提供商/你的网络边缘连接到两个 Microsoft 企业边缘路由器 (MSEE)。 Microsoft 要求从连接服务提供商/你的一端建立双重 BGP 连接 – 各自连接到每个 MSEE。 你可以选择不要在你的一端部署冗余设备/以太网路线。 但是，连接服务提供商会使用冗余设备，确保以冗余方式将你的连接移交给 Microsoft。 冗余的第 3 层连接配置是 Microsoft [SLA](https://azure.microsoft.com/support/legal/sla/) 生效的条件。

### 与 Microsoft 云服务建立连接
<a id="connectivity-to-microsoft-cloud-services" class="xliff"></a>
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

通过 ExpressRoute 连接可访问以下服务：

* Microsoft Azure 服务
* Microsoft Office 365 服务
* Microsoft Dynamics 365

你可以访问 [ExpressRoute 常见问题](expressroute-faqs.md) 页，以获取通过 ExpressRoute 支持的服务的详细列表。

### 与地缘政治区域中的所有区域建立连接
<a id="connectivity-to-all-regions-within-a-geopolitical-region" class="xliff"></a>
可以在我们的某个 [对等位置](expressroute-locations.md) 连接到 Microsoft，然后访问该地缘政治区域中的所有区域。 

例如，如果你在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft，则就能够访问在欧洲北部和欧洲西部托管的所有 Microsoft 云服务。 有关地缘政治地区、关联的 Microsoft 云区域和对应的 ExpressRoute 对等位置的概述，请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。

### 使用 ExpressRoute 高级版附加组件建立全球连接
<a id="global-connectivity-with-expressroute-premium-add-on" class="xliff"></a>
你可以启用 ExpressRoute 高级版附加功能，将连接扩展为跨越地缘政治边界。 例如，如果你在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft，则就能够访问全球所有区域托管的所有 Microsoft 云服务（不包括国家/地区云）。 就像访问欧洲北部和西部区域一样，你还可以访问部署在南美洲或澳大利亚的服务。

### 丰富的连接合作伙伴生态系统
<a id="rich-connectivity-partner-ecosystem" class="xliff"></a>
ExpressRoute 的连接服务提供商和 SI 合作伙伴生态系统不断发展。 有关最新信息，请参阅 [ExpressRoute 提供商和位置](expressroute-locations.md) 一文。

### 与国家/地区云建立连接
<a id="connectivity-to-national-clouds" class="xliff"></a>
Microsoft 为特殊的地缘政治地区和客户群提供隔离的云环境。 有关国家/地区云和提供商的列表，请参阅 [ExpressRoute 提供商和位置](expressroute-locations.md) 页。

### 带宽选项
<a id="bandwidth-options" class="xliff"></a>
你可以购买各种带宽的 ExpressRoute 线路。 支持的带宽列表如下。 请务必咨询连接服务提供商，以获取他们支持的带宽列表。

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### 动态调整带宽
<a id="dynamic-scaling-of-bandwidth" class="xliff"></a>
用户可以在不中断连接的情况下增大 ExpressRoute 线路带宽（尽最大努力）。 

### 弹性计费模式
<a id="flexible-billing-models" class="xliff"></a>
你可以选择最适合自己的计费模式。 请从以下计费模式中选择。 有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

* **无限制数据**。 ExpressRoute 线路按月计费，所有入站和出站数据传输不收取费用。 
* **计量数据**。 ExpressRoute 线路按月计费。 所有入站数据传输免费。 出站数据传输按每 GB 数据传输计费。 数据传输费率根据区域不同而异。
* **ExpressRoute 高级版附加组件**。 ExpressRoute 高级版是 ExpressRoute 线路上的附加组件。 ExpressRoute 高级版附加组件提供以下功能： 
  * 提高 Azure 公共和 Azure 专用对等互连的路由限制，从4,000 路由提升至 10,000 路由。
  * 服务的全球连接。 在任何区域（国家/地区云除外）创建的 ExpressRoute 线路都将能够访问位于全球其他区域的资源。 例如，创建于欧洲西部的虚拟网络可以通过在硅谷设置的 ExpressRoute 线路进行访问。
  * 增加了每个 ExpressRoute 线路的 VNet 链接数量，从 10 增加至更大的限制，具体取决于线路的带宽。

## 常见问题
<a id="faq" class="xliff"></a>

有关 ExpressRoute 的常见问题，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

## 后续步骤
<a id="next-steps" class="xliff"></a>

* 了解 [ExpressRoute 连接模型](expressroute-connectivity-models.md)。
* 了解 ExpressRoute 连接和路由域。 请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 查找服务提供商。 请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
* 请参阅[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的要求。
* 配置 ExpressRoute 连接。
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)
  * [配置 ExpressRoute 线路的对等互连](expressroute-howto-routing-portal-resource-manager.md)
  * [将虚拟网络连接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)

