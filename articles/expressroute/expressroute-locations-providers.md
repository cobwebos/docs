---
title: "位置和连接服务提供商：Azure ExpressRoute | Microsoft 文档"
description: "本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。 按位置排序。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: a888593f992913e31bbf04834c86b1d4a2f1b196
ms.lasthandoff: 04/06/2017

---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作伙伴和对等位置

> [!div class="op_single_selector"]
> * [按提供商列出的位置](expressroute-locations.md)
> * [按位置列出的提供商](expressroute-locations-providers.md)


本文中的表格提供有关 ExpressRoute 连接提供商、ExpressRoute 地理覆盖范围、通过 ExpressRoute 支持的 Microsoft 云服务以及 ExpressRoute 系统集成商 (SI) 的信息。

## <a name="partners"></a>ExpressRoute 连接服务提供商
所有的 Azure 区域和位置都支持 ExpressRoute。 以下地图提供了 Azure 区域和 ExpressRoute 位置的列表。 ExpressRoute 位置是指 Microsoft 与多个服务提供商对等互连的位置。

![位置地图][0]

如果你至少与地缘政治区域内的一个 ExpressRoute 位置连接，你将有权访问地缘政治区域内所有区域中的 Azure 服务。 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>地缘政治区域中 ExpressRoute 位置与 Azure 区域的映射
下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **北美** |美国东部、美国西部、美国东部 2 区、美国西部 2 区、美国中部、美国中南部、美国中北部、美国中西部、加拿大中部、加拿大东部 |亚特兰大、芝加哥、达拉斯、拉斯维加斯、洛杉矶、纽约、西雅图、硅谷、华盛顿特区、蒙特利尔、魁北克市、多伦多 |
| **南美洲** |巴西南部 |圣保罗 |
| **欧洲** |北欧、西欧、英国西部、英国南部 |阿姆斯特丹、都柏林、伦敦、纽波特（威尔士）、巴黎 |
| **亚洲** |东亚、东南亚 |中国香港特别行政区、新加坡 |
| **日本** |日本西部、日本东部 |大坂、东京 |
| **澳大利亚** |澳大利亚东南部、澳大利亚东部 |墨尔本、悉尼 |
| **印度** |印度西部、印度中部、印度南部 |金奈、孟买 |
| **韩国** |韩国中部、韩国南部 |釜山、首尔 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>国家/地区云的区域和地缘政治边界
下表提供了国家/地区云的区域和地缘政治边界的信息。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- | --- |
| **美国政府云** |美国爱荷华州政府、美国弗吉尼亚州政府、美国 DoD 中部、美国 DoD 东部  |芝加哥、达拉斯、纽约、硅谷、华盛顿特区 |
| **中国** |中国北部、中国东部 |北京、上海 |
| **德国** |德国中部、德国东部 |柏林、法兰克福 |

标准 ExpressRoute SKU 不支持跨地缘政治区域的连接。 你需要启用 ExpressRoute 高级版附加组件才能支持全球连接。 不支持连接到国家/地区云环境。 如有需要，请联系连接服务提供商。

## <a name="locations"></a>连接服务提供商位置

下表显示连接位置和每个位置的服务提供商。 若要查看服务提供商及其提供服务的位置，请参阅[按服务提供商列出的位置](expressroute-locations.md#locations)。 


### <a name="production-azure"></a>生产 Azure
| **位置** | **服务提供商** |
| --- | --- |
| **阿姆斯特丹** |Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Internet Solutions - Cloud Connect、Interxion、KPN、Level 3 Communications、Orange、Tata Communications、TeleCity Group、Telefonica+、Telenor、Verizon |
| **亚特兰大** |Equinix |
| **釜山** |LG CNS+ |
| **金奈** |Global CloudXchange (GCX)、SIFY、Tata Communications |
| **芝加哥** |AT&T NetBond、Comcast、Equinix、Level 3 Communications、Verizon、Zayo Group |
| **达拉斯** |Aryaka Networks、AT&T NetBond、Cologix、Equinix、Level 3 Communications、Megaport、Verizon、Zayo Group+ |
| **都柏林** |Colt、Telecity Group |
| **香港** |British Telecom、China Telecom Global、Equinix、Megaport、Orange、PCCW Global Limited、Tata Communications、Verizon |
| **拉斯维加斯** |Level 3 Communications+、Megaport |
| **伦敦** |AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、MTN、NTT Communications、Orange、Tata Communications、Telecity Group、Telehouse - KDDI、Telenor、Verizon、Vodafone、Zayo Group+ |
| **洛杉矶** |CoreSite、Equinix、Megaport、NTT、Zayo Group |
| **墨尔本** |AARNet、Equinix、Megaport、NEXTDC、Telstra Corporation |
| **纽约** |Coresite、Equinix、Megaport、Zayo Group |
| **Newport(Wales)** |Next Generation Data |
| **Montreal** |Bell Canada、Cologix |
| **Mumbai** |Airtel+、Tata Communications |
| **大阪** |Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank |
| **巴黎** |Interxion、Equinix+ |
| **魁北克市** | Megaport |
| **圣保罗** |Equinix、Telefonica |
| **西雅图** |Equinix、Level 3 Communications、Megaport |
| **首尔** |KINX、Sejong Telecom |
| **硅谷** |Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink+、Comcast、Console、Equinix、Level 3 Communications、Orange、Tata Communications、Verizon、Zayo Group |
| **新加坡** |Aryaka Networks、AT&T NetBond、British Telecom、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Verizon |
| **悉尼** |AARNet、AT&T NetBond、British Telecom、Equinix、Megaport、NEXTDC、Orange、Telstra Corporation、Verizon |
| **东京** |Aryaka Networks、British Telecom、Colt, Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank、Verizon |
| **多伦多** |Bell Canada、Cologix、Console、Equinix、Megaport、Zayo Group |
| **华盛顿特区** |Aryaka Networks、AT&T NetBond、British Telecom、Comcast、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、Verizon、Zayo Group |

 **+** 表示即将推出

### <a name="national-cloud-environments"></a>国家/地区云环境

### <a name="us-government-cloud"></a>美国政府云
| **位置** | **服务提供商** |
| --- | --- |
| **芝加哥** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **达拉斯** |Equinix、Megaport、Verizon |
| **纽约** |Equinix、Level 3 Communications+、Verizon |
| **硅谷** | Equinix |
| **西雅图** | Equinix+ |
| **华盛顿特区** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |

### <a name="china"></a>中国
| **位置** | **服务提供商** |
| --- | --- |
| **北京** |中国电信 |
| **上海** |中国电信 |

若要了解详细信息，请参阅 [位于中国的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德国
| **位置** | **服务提供商** |
| --- | --- |
| **柏林** |Colt+、e-shelter、Megaport+ |
| **法兰克福** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>通过未列出的服务提供商建立连接
如果前面部分中未列出你的连接服务提供商，你仍可以建立连接。

* 请咨询你的连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 你可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* 让你的连接提供商将你的网络扩展到选择的对等互连位置。
  * 确保连接提供商以高可用性方式扩展你的连接，以防出现单点故障。
* 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路以连接到 Microsoft。
  * 根据 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md) 中的步骤来设置连接。

| **位置** | **Exchange** | **连接提供商** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix、Telecity | Eurofiber、Fastweb S.p.A、Nianet |
| **芝加哥** | Equinix | Windstream |
| **达拉斯** | Equinix、Megaport | C3ntro、Data Foundry、Transtelco |
| **法兰克福** | Telecity | Nianet、QSC AG |
| **伦敦** | Equinix、euNetworks、Telecity | Bezeq International Ltd.、Exponential E、HSO、NexGen Networks、Tamares Telecom |
| **洛杉矶** | Equinix |Transtelco |
| **马德里** | Level3 | Zertia |
| **Montreal** | Cologix、Equinix | Airgate Technologies Inc、Cogeco Peer 1 |
| **纽约** |Equinix | Lightower |
| **西雅图** |Equinix | Alaska Communications |
| **硅谷** |Equinix | Windstream |
| **新加坡** |Equinix |1CLOUDSTAR、Epsilon Telecommunications Limited |
| **Slough** | Equinix | HSO|
| **悉尼** | Megaport | Macquarie Telecom Group|
| **东京** | Equinix | ARTERIA Networks Corporation |
| **多伦多** | Equinix | Airgate Technologies Inc、Cogeco Peer 1 |
| **华盛顿特区** |Equinix | Lightower、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。 你可以与下表中列出的任一系统集成商合作，以帮助将你加入 ExpressRoute。

| **所在洲** | **系统集成商** |
| --- | --- |
| **亚洲** |Avanade Inc.、OneAs1a |
| **澳大利亚** | IT Consultancy、Vigilant.IT |
| **欧洲** |Avanade Inc.、Altogee、Bright Skies GmbH、Dotnet Solutions、MSG Services、Nelite、Orange Networks、sol-tec |
| **北美** |Avanade Inc.、Equinix Professional Services、Perficient、Presidio、Project Leadership |
| **南美洲** |Avanade Inc. |
## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地图"

