---
title: 位置和连接服务提供商：Azure ExpressRoute | Microsoft 文档
description: 本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。 按位置排序。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b8c8a0baba5a80c6a383e6f8f14e29b482dbc82
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878606"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作伙伴和对等位置

> [!div class="op_single_selector"]
> * [按提供商列出的位置](expressroute-locations.md)
> * [按位置列出的提供商](expressroute-locations-providers.md)


本文中的表格提供了有关 ExpressRoute 地理覆盖范围和位置、ExpressRoute 连接提供商和 ExpressRoute 系统集成商 (SI) 的信息。

> [!Note]
> Azure 区域和 ExpressRoute 位置是两个不同概念，了解两者之间的区别对于探索 Azure 混合网络连接至关重要。 
>
>

## <a name="azure-regions"></a>Azure 区域
Azure 区域是 Azure 计算、网络和存储资源所在的全局数据中心。 创建 Azure 资源时，客户需要选择资源位置。 资源位置确定在其中创建资源的 Azure 数据中心（或可用性区域）。

## <a name="expressroute-locations"></a>快速路线位置
ExpressRoute 位置（有时称为对等位置或 Meet-me 位置）是 Microsoft 企业边缘 （MSEE） 设备所在的同地位置设施。 ExpressRoute 位置是 Microsoft 网络的入口点，并且在全球范围内进行分发，为客户提供连接到 Microsoft 全球网络的机会。 这些位置是 ExpressRoute 合作伙伴和 ExpressRoute Direct 客户向 Microsoft 网络发出交叉连接的位置。 通常，ExpressRoute 位置不需要与 Azure 区域匹配。 例如，客户可以在*西雅图*对等位置创建具有资源位置 *"美国东部*"的 ExpressRoute 电路。

如果至少与地缘政治区域内的一个 ExpressRoute 位置连接，将有权访问地缘政治区域内所有区域中的 Azure 服务。 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure 区域到地缘政治区域内的 ExpressRoute 路由位置
下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

| **地缘政治区域** | **Azure 区域** | **快速路线位置** |
| --- | --- | --- |
| **澳大利亚政府** | 澳大利亚中部、澳大利亚中部 2 |堪培拉、堪培拉 2 |
| **欧洲** | 法国中部、法国南部、德国北部、德国中西部、北欧、挪威东部、挪威西部、瑞士北部、瑞士西部、英国西部、英国南部、西欧 |阿姆斯特丹、阿姆斯特丹2、哥本哈根、都柏林、法兰克福、日内瓦、伦敦、伦敦2、马赛、米兰、慕尼黑、纽波特（威尔士）、奥斯陆、巴黎、斯塔万格、斯德哥尔摩、苏黎世、慕尼黑 |
| **北美** | 美国东部、美国西部、美国东部 2 区、美国西部 2 区、美国中部、美国中南部、美国中北部、美国中西部、加拿大中部、加拿大东部 |亚特兰大、芝加哥、达拉斯、丹佛、拉斯维加斯、洛杉矶、迈阿密、纽约、圣安东尼奥、西雅图、硅谷、硅谷2、华盛顿特区、华盛顿特区、蒙特利尔、魁北克城、多伦多、温哥华 |
| **亚洲** | 东亚、东南亚 | 曼谷， 香港， 香港2， 雅加达， 吉隆坡， 新加坡， 新加坡2， 台北 |
| **印度** | 印度西部、印度中部、印度南部 |金奈、金奈 2、孟买、孟买 2 |
| **日本** | 日本西部、日本东部 |大阪， 东京， 东京2 |
| **大洋洲** | 澳大利亚东南部、澳大利亚东部 |奥克兰、 墨尔本、 珀斯、 悉尼、 悉尼2 | 
| **韩国** | 韩国中部、韩国南部 |釜山、首尔|
| **阿拉伯联合酋长国** | 阿联酋中部， 阿联酋北部 | 迪拜， 迪拜2 |
| **南非** | 南非西部，南非北部 |开普敦、约翰内斯堡 |
| **南美洲** | 巴西南部 |圣保罗 |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>国家云的 Azure 区域和地缘政治边界
下表提供了国家/地区云的区域和地缘政治边界的信息。

| **地缘政治区域** | **Azure 区域** | **快速路线位置** |
| --- | --- | --- |
| **美国政府云** |US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、US DoD 中部、US DoD 东部  |亚特兰大、芝加哥、达拉斯、纽约、凤凰城、圣安东尼奥、西雅图、硅谷、华盛顿特区 |
| **中国东部** |中国东部、中国东部 2 |上海、上海 2 |
| **中国北部** |中国北部、中国北部 2 |北京、北京 2 |
| **德国** |德国中部、德国东部 |柏林、法兰克福 |

标准 ExpressRoute SKU 不支持跨地缘政治区域的连接。 需要启用 ExpressRoute 高级版附加组件才能支持全球连接。 不支持连接到国家/地区云环境。 如有需要，请联系连接服务提供商。

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 连接服务提供商

下表显示连接位置和每个位置的服务提供商。 若要查看服务提供商及其提供服务的位置，请参阅[按服务提供商列出的位置](expressroute-locations.md)。

* **本地 Azure 区域**是在每个对等位置[快速路由本地](expressroute-faqs.md)区域可以访问的区域。 **n/a**表示 ExpressRoute Local 在该对等位置不可用。

* **区域**是指[定价](https://azure.microsoft.com/pricing/details/expressroute/)。


### <a name="global-commercial-azure"></a>全球商业 Azure
| **位置** | **地址** | **区** | **本地 Azure 区域** | **ER 直接** | **服务提供商** |
| --- | --- | --- | --- | --- | --- |
| **阿姆斯特丹** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 西欧 | 10G， 100G | 阿里亚卡网络、美国电话电报公司&amp;T NetBond、英国电信、科尔特、Equinix、euNetworks、G&ANT、InterCloud、Interxion、KPN、IX 覆盖、3 级通信、兆端口、NTT 通信、奥兰治、塔塔通信、电信、电信、泰利亚运营商、Verizon、Zayo |
| **阿姆斯特丹 2** | [跨号 AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 西欧 | 10G， 100G | 世纪链接云连接， 科尔特， DE-CIX， 欧网， 互连， 橙色， 沃达丰 |
| **亚特兰大** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | 不适用 | 不适用 | Equinix、Megaport |
| **奥克兰** | [沃库斯集团新西兰奥尔巴尼](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | 不适用 | 10G | 德沃利，科尔迪亚，兆港，火花新西兰，Vocus集团新西兰 |
| **曼谷** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | 不适用 | 10G | AIS |
| **釜山** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | 韩国南部 | 不适用 | LG CNS |
| **堪培拉** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 澳大利亚中部 | 10G， 100G | CDC |
| **堪培拉 2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 澳大利亚中部 2| 10G， 100G | CDC |
| **开普敦** | [特拉科 CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 南非西部 | 10G | BCX， 互联网解决方案 - 云连接， 液体电信， 特拉科 |
| **金奈** | Tata Communications | 2 | 印度南部 | 10G | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **金奈 2** | Airtel | 2 | 印度南部 | 不适用 | Airtel |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 美国中北部 | 10G， 100G | 阿里亚卡网络， AT&T NetBond， 世纪链接云连接， 科洛尼克斯， 科尔特， 康卡斯特， 核心网站， Equinix， InterCloud， Internet2， 3 级通信， 兆波特， 包布，电讯盈科全球有限公司， 冲刺， 泰利亚运营商， Verizon， Zayo |
| **哥本哈根** | [锡龙 CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | 不适用 | 10G | Interxion |
| **达拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | 不适用 | 10G， 100G | 阿里亚卡网络，美国电话&T NetBond，科洛尼克斯，Equinix，Internet2，3级通信，兆波特，中子网络，Telmex Uninet，Telia运营商，Transtelco，Verizon，Zayo|
| **丹佛** | [核心站点 DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 美国中西部 | 不适用 | 核心网站， 兆港， 扎约 |
| **迪拜** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 阿拉伯联合酋长国北部 | 不适用 | 埃泰萨尔特 阿联酋 |
| **迪拜2** | [杜德梅纳](http://datamena.com/solutions/data-centre) | 3 | 阿拉伯联合酋长国北部 | 不适用 | 杜戈梅纳， 兆港， 奥兰治， 奥里克斯康 |
| **都柏林** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 北欧 | 10G， 100G | 科尔特， eir， Equinix， euNetworks， 互通， 兆端口 |
| **法兰克福** | [锡龙 FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 德国中西部 | 10G， 100G | 世纪链接云连接， 科尔特， DE-CIX， Equinix， GEANT， 互连， 兆港， 橙色， 泰利亚运营商 |
| **日内瓦** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 瑞士西部 | 10G， 100G | Equinix、Megaport |
| **香港特别行政区** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 东亚 | 不适用 | 亚里卡网络、英国电信、世纪通云连接、首席电信、中国电信全球、Equinix、InterCloud、Megaport、NTT通信、奥兰治、电讯盈科全球有限公司、塔塔通信、泰利亚运营商、Verizon |
| **香港2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | 不适用 | 10G | |
| **雅加达** | 印度尼西亚特尔科姆 | 4 | 不适用 | 10G | |
| **约翰内斯堡** | [特拉科JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 南非北部 | 10G | 英国电信， 互联网解决方案 - 云连接， 液体电信， 橙色， 特拉科 |
| **吉隆坡** | [时代点梅纳拉AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | 不适用 | 不适用 | TIME dotCom |
| **拉斯维加斯** | [开关 LV](https://www.switch.com/las-vegas) | 1 | 不适用 | 不适用 | CenturyLink Cloud Connect、Megaport |
| **伦敦** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 英国南部 | 10G， 100G | 美国电话电报公司&T NetBond、 英国电信、 科尔特、 Equinix、 euNetworks、 InterCloud、 互联网解决方案 - 云连接、Interxion、Jisc、3 级通信、兆波特、MTN、NTT 通信、奥兰治、电讯盈科全球有限公司、塔塔通信、电信总部 - KDDI、Telenor、Telia 运营商、Verizon、沃达丰、Zayo |
| **London2** | [电信大楼北二号](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 英国南部 | 10G， 100G | 世纪链接云连接， 科尔特， 九到达， Equinix， 兆端口， 电房 - KDDI |
| **Los Angeles** | [核心网站 LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | 不适用 | 不适用 | 核心站点， Equinix， Megaport， 中子网络， NTT， Transtelco， Zayo |
| **马赛** |[插曲 MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 法国南部 | 不适用 | DE-CIX， GEANT， Interxion， 捷豹网络， 奥雷多云连接 |
| **墨尔本** | [下一个DC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 澳大利亚东南部 | 10G， 100G | AARNet、 德沃利、 Equinix、 兆港、 NEXTDC、 Optus、 Telstra 公司、 TPG 电信 |
| **迈阿密** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | 不适用 | 10G | C3ntro， Equinix， 兆港， 中子网络 |
| **Milan** | [伊里多斯](https://irideos.it/en/data-centers/) | 1 | 不适用 | 10G | |
| **Montreal** | [科洛尼克斯 MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | 不适用 | 10G， 100G | 贝尔加拿大， 科洛威斯， 兆港， 泰鲁斯， 扎约 |
| **Mumbai** | Tata Communications | 2 | 印度西部 | 不适用 | 全球云X变更 （GCX）、信实、Sify、塔塔通信、Verizon |
| **孟买 2** | Airtel | 2 | 印度西部 | 不适用 | Airtel、Sify、Vodafone Idea |
| **慕尼黑** | [边缘康奈克斯](https://www.edgeconnex.com/locations/europe/) | 1 | 不适用 | 10G， 100G | |
| **纽约** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | 不适用 | 不适用 | 世纪链接云连接， 科尔特， 核心网站， Equinix， 云间， 兆端口， 包， Zayo |
| **纽波特（威尔士）** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | 英国西部 | 不适用 | 英国电信， 科尔特， 3 级通信， 下一代数据 |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 日本西部 | 10G， 100G | Colt， Equinix， 互联网倡议日本公司 - IIJ， 兆港， NTT 通信， NTT 智能连接， 软银 |
| **奥斯陆** | [迪吉普·乌尔文](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 挪威东部 | 10G， 100G | 特莱诺兆港，泰利亚航空公司 |
| **巴黎** | [跨号 PAR5](https://www.interxion.com/Locations/paris/) | 1 | 法国中部 | 10G， 100G | 世纪链接云连接， 科尔特， Equinix， 云间， 插曲， 橙色， 泰利亚运营商， 扎约 |
| **珀斯** | [下一个DC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | 不适用 | 10G | 兆端口，下一个DC |
| **魁北克市** | [优势](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 加拿大东部 | 不适用 | Bell Canada、Megaport |
| **圣安东尼奥** | [赛勒斯内 SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 美国中南部 | 10G， 100G | CenturyLink Cloud Connect、Megaport |
| **圣保罗** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 巴西南部 | 不适用 | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEO |
| **西雅图** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 美国西部 2 | 10G， 100G | 阿里亚卡网络， Equinix， 3 级通信， 兆港， 泰鲁斯， 扎约 |
| **首尔** | [KINX加桑IDC](https://www.kinx.net/support/location/?lang=en) | 2 | 韩国中部 | 10G， 100G | KINX， KT， LG CNS， 世宗电信 |
| **硅谷** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 美国西部 | 10G， 100G | Aryaka网络， AT&T NetBond， 英国电信， 世纪链接云连接， 科尔特， 康卡斯特， 核心网站， Equinix， InterCloud， 互联网2， IX 覆盖， 数据包， 数据包Fabric， 3级通信， 兆端口， 橙色， 冲刺， 塔塔通信， 泰利亚运营商， Verizon， Zayo |
| **硅谷2** | [核心网站 SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 美国西部 | 10G， 100G | 科尔特， 核心网站 | 
| **新加坡** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 东南亚 | 10G， 100G | 阿里亚卡网络、美国电话电报公司&T NetBond、英国电信、中国移动国际、爱普西隆全球通信、Equinix、InterCloud、3级通信、兆丰、NTT通信、奥兰治、新加坡电信、塔塔通信、电信公司、Verizon、沃达丰 |
| **新加坡 2** | [全球开关泰森](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 东南亚 | 10G， 100G | 中国联通全球、科尔特、爱普西隆全球通信、兆港、新加坡电信 |
| **斯塔万格** | [绿山DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 挪威西部 | 10G， 100G | |
| **斯德哥尔摩** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | 不适用 | 10G | 埃奎尼克斯， 泰利亚航空公司 |
| **悉尼** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 澳大利亚东部 | 10G， 100G | AARNet、 AT&T NetBond、 英国电信、 德沃利、 Equinix、 科尔迪亚、 兆港、 NEXTDC、 NTT 通信、 Optus、 奥兰治、 Spark NZ、 Telstra 公司、 TPG 电信、 Verizon、 Vocus Group NZ |
| **悉尼2** | [下一个DC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 澳大利亚东部 | 10G， 100G | 兆端口，下一个DC |
| **台北** | Chief Telecom | 2 | 不适用 | 10G | 法里亚斯通首席电信 |
| **东京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 日本东部 | 10G， 100G | Aryaka网络， AT&T NetBond， BBIX， 英国电信， 世纪链接云连接， 科尔特， Equinix， 互联网倡议日本公司 - IIJ， 兆港， NTT通信， NTT EAST， 奥兰治， 软银， Verizon |
| **东京2** | [在东京](https://www.attokyo.com/) | 2 | 日本东部 | 10G， 100G | 在东京 |
| **多伦多** | [科洛尼克斯TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 加拿大中部 | 10G， 100G | AT&T NetBond、贝尔加拿大、世纪链接云连接、科洛尼克斯、Equinix、IX 到达兆港、泰鲁斯、威瑞森、扎约 |
| **温哥华** | [科洛尼克斯范1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | 不适用 | 10G， 100G | |
| **华盛顿** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 美国东部，美国东部 2 | 10G， 100G | 阿里亚卡网络， AT&T NetBond， 英国电信， 世纪链接云连接， 科洛尼克斯， 科尔特， 康卡斯特， 核心网站， Equinix， Internet2， InterCloud， IX 覆盖， 3 级通信， 兆端口， 中子网络， NTT通信， 橙色， 包布，SES， 冲刺， 塔塔通信， 泰利亚运营商， Verizon， Zayo |
| **华盛顿特区 2** | [核心网站雷斯顿](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 美国东部，美国东部 2 | 10G， 100G | 世纪链接云连接， 核心网站， Intelsat， 维亚盛， 扎约 | 
| **苏黎世** | [联交 ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 瑞士北部 | 10G， 100G | 云间， 国际， 兆港， 瑞士 |

 **+** 表示即将推出

### <a name="national-cloud-environments"></a>国家/地区云环境

Azure 国家云彼此隔离，与全局商业 Azure 隔离。 一个 Azure 云的 ExpressRoute 无法连接到其他中的 Azure 区域。

### <a name="us-government-cloud"></a>美国政府云
| **位置** | **地址** | **本地 Azure 区域**| **ER 直接** | **服务提供商** |
| --- | --- | --- | --- | --- |
| **亚特兰大** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | 不适用 | 10G， 100G | Equinix |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 不适用 | 10G， 100G | AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **达拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 不适用 | 10G， 100G | Equinix、Megaport、Verizon |
| **纽约** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 不适用 | 10G， 100G | Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | [赛勒斯·内·钱德勒](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 亚利桑那州 | 不适用 | AT&T NetBond， 世纪链接云连接， 兆端口 |
| **圣安东尼奥** | [赛勒斯内 SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 德克萨斯州 | 不适用 | CenturyLink Cloud Connect、Megaport |
| **硅谷** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | 不适用 | 10G， 100G | AT&T， Equinix， 3 级通信， Verizon |
| **西雅图** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 不适用 | 不适用 | Equinix、Megaport |
| **华盛顿** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 美国国防部东，美国弗吉尼亚州州长 | 10G， 100G | AT&T NetBond、世纪链接云连接、Equinix、3 级通信、兆端口、Verizon |

### <a name="china"></a>中国
| **位置** | **服务提供商** |
| --- | --- |
| **北京** |中国电信 |
| **北京 2** | 中国电信、中国联通、GDS |
| **上海** |中国电信 |
| **上海 2** | 中国电信，GDS |

要了解更多信息，请参阅[中国速递路线](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德国
| **位置** | **服务提供商** |
| --- | --- |
| **柏林** |e-shelter、Megaport+、T-Systems |
| **法兰克福** |Colt、Equinix、Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>通过 Exchange 提供商连接
如果前面部分中未列出连接服务提供商，仍可以建立连接。

* 请咨询连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [国际](https://www.interxion.com/)
  * [下一个DC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* 让连接提供商将网络扩展到选择的对等互连位置。
  * 确保连接提供商以高可用性方式扩展连接，以防出现单点故障。
* 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路以连接到 Microsoft。
  * 根据 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md) 中的步骤来设置连接。

## <a name="connectivity-through-satellite-operators"></a>通过卫星运营商连接
如果您是远程的，并且没有光纤连接，或者想要探索其他连接选项，可以检查以下卫星运营商。 

* 英特尔
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [维亚萨特](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>通过其他服务提供商进行连接
| **位置** | **Exchange 阻止的设备** | **连接提供商** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix， 互通， 3 级通信 | BICS、 CloudXpress、 欧洲光纤、 快速网络 S.p.A、 海湾桥国际、 巴林卡拉姆电信 B.S.C、 美因One、 Nianet、 POST 电信卢森堡、 普罗西穆斯、 RETN、 TDC Erhverv、 意大利电信火花、 电信德国电信有限公司、 泰利亚 |
| **亚特兰大** | Equinix| 皇冠城堡
| **开普敦** | Teraco | MTN |
| **芝加哥** | Equinix| 皇冠城堡， 频谱企业， 风流 |
| **达拉斯** | Equinix、Megaport | 安克斯特， C3ntro电信， 考克斯业务， 皇冠城堡， 数据铸造厂， 频谱企业， Transtelco |
| **法兰克福** | Interxion | BICS、 西尼亚、 Equinix、 尼亚尼特、 QSC AG、 德国电信有限公司 |
| **汉堡** | Equinix | Cinia |
| **香港特别行政区** | Equinix | Chief、Macroview Telecom |
| **约翰内斯堡** | Teraco | MTN |
| **伦敦** | BICS， Equinix， 欧网| 贝泽克国际有限公司， CoreAzure， Epsilon电信有限公司， 指数E， HSO， NexGen网络， Proximus， 塔马拉斯电信， 扎因 |
| **Los Angeles** | Equinix |皇冠城堡， 频谱企业， Transtelco |
| **马德里** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate技术公司阿普图姆技术公司，罗杰斯，齐罗 |
| **纽约** |Equinix、Megaport | 阿尔蒂斯商业， 皇冠城堡， 频谱企业， 网络 |
| **巴黎** | Equinix | Proximus |
| **魁北克市** | Megaport | Fibrenoire |
| **圣保罗** | Equinix | Venha Pra Nuvem |
| **西雅图** |Equinix | Alaska Communications |
| **硅谷** |核心网站， Equinix | 考克斯业务， 频谱企业， 风流， X2nsat公司. |
| **新加坡** |Equinix |1CLOUDSTAR， BICS， CMC电信， Epsilon电信有限公司， LGA电信， 联合信息高速公路 （UIH） |
| **Slough** | Equinix | HSO|
| **悉尼** | Megaport | Macquarie Telecom Group|
| **东京** | Equinix | ARTERIA Networks Corporation、BroadBand Tower, Inc. |
| **多伦多** | Equinix、Megaport | Airgate技术公司，比斯菲尔德地铁公司，阿普图姆技术公司，IVedha公司，罗杰斯，Thinktel，齐罗|
| **华盛顿** |Equinix | Altice 商业， BICS， 考克斯业务， 皇冠城堡， Gtt通信公司， Epsilon电信有限公司， 马森，风流 |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。 可以与下表中列出的任一系统集成商合作，以帮助你将加入 ExpressRoute。

| **Continent** | **系统集成商** |
| --- | --- |
| **亚洲** |Avanade Inc.、OneAs1a |
| **澳大利亚** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **欧洲** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北美** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南美洲** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置图"
