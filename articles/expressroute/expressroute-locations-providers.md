---
title: 位置和连接提供商：Azure ExpressRoute | Microsoft Docs
description: 本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。 按位置排序。
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: cherylmc
ms.openlocfilehash: 3867e4c59f541fb50c536850e89e03d49a831e42
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162087"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作伙伴和对等位置

> [!div class="op_single_selector"]
> * [按提供商列出的位置](expressroute-locations.md)
> * [按位置列出的提供商](expressroute-locations-providers.md)


本文中的表格提供了有关 ExpressRoute 地理覆盖范围和位置、ExpressRoute 连接提供商和 ExpressRoute 系统集成商 (SI) 的信息。

> [!Note]
> Azure 区域和 ExpressRoute 位置是两个不同的不同概念，了解这两者之间的区别对于探索 Azure 混合网络连接至关重要。 
>
>

## <a name="azure-regions"></a>Azure 区域
Azure 区域是 Azure 计算、网络和存储资源所在的全球数据中心。 创建 Azure 资源时，客户需要选择资源位置。 资源位置确定在哪个 Azure 数据中心（或可用性区域）中创建资源。

## <a name="expressroute-locations"></a>ExpressRoute 位置
ExpressRoute 位置（有时称为 "对等互连位置" 或 "满足我的位置"）是 Microsoft 企业边缘（MSEE）设备所在的归置设施。 ExpressRoute 位置是 Microsoft 网络的入口点，是全球分布的，为客户提供了连接到全球各地 Microsoft 网络的机会。 这些位置是 ExpressRoute 合作伙伴和 ExpressRoute 直销客户在与 Microsoft 网络之间建立交叉连接的地方。 通常，ExpressRoute 位置不需要匹配 Azure 区域。 例如，客户可以在*西雅图*对等位置使用资源位置 "*美国东部*" 创建 ExpressRoute 线路。

如果至少与地缘政治区域内的一个 ExpressRoute 位置连接，将有权访问地缘政治区域内所有区域中的 Azure 服务。 

## <a name="locations"></a>地缘政治区域中 ExpressRoute 位置的 Azure 区域
下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

| **地缘政治区域** | **区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- | --- |
| **澳大利亚政府** | 1 | 澳大利亚中部、澳大利亚中部 2 |堪培拉、堪培拉 2 |
| **欧洲** | 1 |法国中部、法国南部、北欧、西欧、英国西部、英国南部 |阿姆斯特丹，Amsterdam2，哥本哈根，都柏林，法兰克福，伦敦，London2，马赛，纽波特（威尔士），巴黎，斯德哥尔摩，苏黎世，慕尼黑 |
| **北美** | 1 |美国东部、美国西部、美国东部 2 区、美国西部 2 区、美国中部、美国中南部、美国中北部、美国中西部、加拿大中部、加拿大东部 |亚特兰大、芝加哥、达拉斯、丹佛、拉斯维加斯、洛杉矶、迈阿密、纽约、San Antonio、西雅图、硅谷、硅 Valley2、华盛顿特区、华盛顿 DC2、蒙特利尔、魁北克市、多伦多 |
| **亚洲** | 2 |东亚、东南亚 |香港特别行政区, 吉隆坡, 新加坡, 新加坡 2, 台北 |
| **印度** | 2 |印度西部、印度中部、印度南部 |金奈、金奈 2、孟买、孟买 2 |
| **日本** | 2 |日本西部、日本东部 |大坂、东京 |
| **大洋洲** | 2 |澳大利亚东南部、澳大利亚东部 |奥克兰, 墨尔本, | 
| **韩国** | 2 |韩国中部、韩国南部 |釜山、首尔|
| **阿拉伯联合酋长国** | 3 | 阿拉伯联合酋长国中部, 阿拉伯联合酋长国北部 | Dubai、Dubai2 |
| **南非** | 3 |南非西部、南非北部 |开普敦、约翰内斯堡 |
| **南美洲** | 3 |巴西南部 |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>适用于全国云的 Azure 区域和地缘政治边界
下表提供了国家/地区云的区域和地缘政治边界的信息。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **美国政府云** |US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、US DoD 中部、US DoD 东部  |芝加哥、达拉斯、纽约、凤凰城、圣安东尼奥、西雅图、硅谷、华盛顿特区 |
| **中国东部** |中国东部、中国东部 2 |上海、上海 2 |
| **中国北部** |中国北部、中国北部 2 |北京、北京 2 |
| **德国** |德国中部、德国东部 |柏林、法兰克福 |

标准 ExpressRoute SKU 不支持跨地缘政治区域的连接。 需要启用 ExpressRoute 高级版附加组件才能支持全球连接。 不支持连接到国家/地区云环境。 如有需要，请联系连接服务提供商。

## <a name="partners"></a>ExpressRoute 连接服务提供商

下表显示连接位置和每个位置的服务提供商。 若要查看服务提供商及其提供服务的位置，请参阅[按服务提供商列出的位置](expressroute-locations.md)。 

**本地 Azure 区域**是每个对等位置的[ExpressRoute 本地](expressroute-faqs.md)可访问的区域。 **n/a**表示 ExpressRoute 本地在该对等位置不可用。


### <a name="production-azure"></a>生产 Azure
| **Location** | **所有者和地址** | **本地 Azure 区域** | **服务提供商** |
| --- | --- | --- | --- |
| **阿姆斯特丹** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 西欧 | Aryaka networks、网络，& T NetBond，英国电信，Colt，Equinix，euNetworks，GÉANT，InterCloud，Interxion，KPN，IX 到，Level 3 通信，Megaport，NTT 通信，橙色，Tata 通信，Telefonica，Telenor，Telia 载波，Verizon，Zayo |
| **阿姆斯特丹 2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 西欧 | CenturyLink Cloud Connect、CIX、Colt、Interxion、Vodafone |
| **亚特兰大** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 不适用 | Equinix、Megaport |
| **奥克兰** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 不适用 | Devoli、Kordia、Megaport、Spark NZ、Vocus Group NZ |
| **釜山** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | 韩国 | LG CNS |
| **堪培拉** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 澳大利亚中部 | CDC |
| **堪培拉 2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 澳大利亚中部 2| CDC |
| **开普敦** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 南非西部 | Internet Solutions - Cloud Connect、Liquid Telecom、Teraco |
| **金奈** | Tata Communications | 印度南部 | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **金奈 2** | Airtel | 印度南部 | Airtel |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 美国中北部 | Aryaka networks、网络, 在 & T NetBond, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 通信, Megaport, PacketFabric, PCCW 全局受限, 冲刺 (Sprint), Telia, Verizon, Zayo |
| **哥本哈根** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 不适用 | Interxion |
| **达拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 不适用 | Aryaka networks、网络, 在 & T NetBond, Cologix, Equinix, Internet2, Level 3 通信, Megaport, Neutrona 网络, Telmex Uninet, Telia 载波, Transtelco, Verizon, Zayo|
| **丹佛** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 美国中西部 | CoreSite、Megaport、Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 阿拉伯联合酋长国北部 | Etisalat 阿拉伯联合酋长国 |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 阿拉伯联合酋长国北部 | du datamena, Orixcom |
| **都柏林** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 北欧 | Colt、eir、Equinix、Interxion、Megaport |
| **法兰克福** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 不适用 | 反 CIX，Interxion，橙色 |
| **香港特别行政区** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 东亚 | Aryaka networks、网络, 英国电信, CenturyLink 云连接, 首席电信, 中国电信全局, Equinix, Megaport, NTT 通信, 橙色, PCCW 全局有限, Tata 通信, Telia 载波, Verizon |
| **约翰内斯堡** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 南非北部 | 英国电信，Internet 解决方案-云连接，液体电信，橙色，Teraco |
| **吉隆坡** | [时间 dotCom Menara 目标](https://www.aims.com.my/co-location/points-of-presence.html) | 不适用 | TIME dotCom |
| **拉斯维加斯** | [切换 LV](https://www.switch.com/las-vegas) | 不适用 | CenturyLink Cloud Connect、Megaport |
| **伦敦** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 英国南部 | AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | [Telehouse 北2](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 英国南部 | IX 覆盖, Equinix |
| **洛杉矶** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 不适用 | CoreSite、Equinix、Megaport、Neutrona Networks、NTT、Zayo |
| **马赛** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 法国南部 | CIX、Interxion、Jaguar 网络 |
| **墨尔本** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 澳大利亚东南部 | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG 电信 |
| **迈阿密** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 不适用 | C3ntro+、Equinix、Megaport、Neutrona Networks |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 不适用 | 铃加拿大、Cologix、Megaport、Telus、Zayo |
| **Mumbai** | Tata Communications | 印度西部 | Global CloudXchange (GCX), 依赖 Jio, Sify, Tata 通信, Verizon |
| **孟买 2** | Airtel | 印度西部 | Airtel、Sify、Vodafone Idea |
| **纽约** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 不适用 | CenturyLink Cloud Connect、Coresite、Equinix、InterCloud、Megaport、Packet、Zayo |
| **Newport(Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 英国西部 | 英国电信, Colt, 级别3通信, 下一代数据 |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 日本西部 | Colt, Equinix, Internet 倡议日本 Inc.-IIJ, NTT 通信, NTT SmartConnect, Softbank |
| **巴黎** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 法国中部 | CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Interxion、橙色、Telia 承运商、Zayo |
| **珀斯** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 不适用 | Megaport、NextDC |
| **魁北克市** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 加拿大东部 | Bell Canada、Megaport |
| **圣安东尼奥** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 美国中南部 | CenturyLink Cloud Connect、Megaport |
| **圣保罗** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 巴西南部 | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEO |
| **西雅图** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 美国西部 2 | Aryaka networks、网络, Equinix, Level 3 通信, Megaport, Telus, Zayo |
| **首尔** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 韩国中部 | KINX、LG CNS、Sejong Telecom |
| **硅谷** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 美国西部 | Aryaka networks、网络，在 & T NetBond，英国电信，CenturyLink Cloud Connect，Comcast，Coresite，Equinix，InterCloud，Internet2，IX 到，数据包，PacketFabric，Level 3 通信，Megaport，橙色，冲刺，Tata 通信，Telia 运营商，Verizon、Zayo |
| **硅 Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 美国西部 | Coresite | 
| **新加坡** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 东南亚 | Aryaka Networks、AT&T NetBond、British Telecom、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **新加坡 2** | [全局交换器 Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 东南亚 | Colt、Epsilon Global communication、Megaport、SingTel |
| **斯德哥尔摩** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 不适用 | Telia 承运商 |
| **悉尼** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 澳大利亚东部 | AARNet, AT & T NetBond, 英国电信, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT 通信, Optus, 橙色, Spark NZ, Telstra Corporation, TPG 电信, Verizon, Vocus 组 NZ |
| **台北** | Chief Telecom | 不适用 | 首席电信, FarEasTone |
| **东京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 日本东部 | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT EAST、Orange、Softbank、Verizon |
| **多伦多** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 加拿大中部 | & T NetBond、钟形加拿大、CenturyLink Cloud Connect、Cologix、Equinix、IX 到 Megaport、Telus、Verizon、Zayo |
| **华盛顿特区** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 美国东部、美国东部2 | Aryaka networks、网络，在 & T NetBond，英国电信，CenturyLink Cloud Connect，Cologix，Comcast，Coresite，Equinix，Internet2，InterCloud，Level 3 通信，Megaport，Neutrona 网络，NTT 通信，橙色，PacketFabric，SES，冲刺（Sprint），Tata通信，Telia 电信公司，Verizon，Zayo |
| **华盛顿特区 2** | [Coresite 场](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 美国东部、美国东部2 |Coresite、Viasat、Zayo | 
| **苏黎世** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 不适用 | Intercloud、Interxion |

 **+** 表示即将推出

### <a name="national-cloud-environments"></a>国家/地区云环境

### <a name="us-government-cloud"></a>美国政府云
| **Location** | **服务提供商** |
| --- | --- |
| **芝加哥** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **达拉斯** |Equinix、Megaport、Verizon |
| **纽约** |Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | 在 & T NetBond, CenturyLink Cloud Connect, Megaport |
| **圣安东尼奥** | CenturyLink Cloud Connect、Megaport |
| **硅谷** | Equinix、Level 3 Communications、Verizon |
| **西雅图** | Equinix、Megaport |
| **华盛顿特区** |& T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 通信, Megaport, Verizon |

### <a name="china"></a>中国
| **Location** | **服务提供商** |
| --- | --- |
| **北京** |中国电信 |
| **北京 2** | 中国电信, GDS |
| **上海** |中国电信 |
| **上海 2** | 中国电信, GDS |

若要了解详细信息，请参阅 [位于中国的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德国
| **Location** | **服务提供商** |
| --- | --- |
| **柏林** |e-shelter、Megaport+、T-Systems |
| **法兰克福** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>通过 Exchange 提供商的连接
如果前面部分中未列出连接服务提供商，仍可以建立连接。

* 请咨询连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* 让连接提供商将网络扩展到选择的对等互连位置。
  * 确保连接服务提供商以高可用性方式扩展连接，以防出现单点故障。
* 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路以连接到 Microsoft。
  * 根据 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md) 中的步骤来设置连接。

## <a name="connectivity-through-satellite-operators"></a>通过附属运算符连接
如果你是远程的并且没有光纤连接，或者想要浏览其他连接选项，则可以检查以下附属运算符。 

* Intelsat
* [工程师](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>通过其他服务提供商的连接
| **Location** | **Exchange** | **连接提供程序** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix，Interxion，Level 3 通信 | BICS、CloudXpress、Eurofiber、Fastweb s.p.a、、、Kalaam 电信巴林 b. C、MainOne、Nianet、电信 Proximus、Erhverv、TDC Italia、电信 Telekom 火花、Deutschland GmbH Telia、 |
| **亚特兰大** | Equinix| 王冠城堡
| **开普敦** | Teraco | MTN |
| **芝加哥** | Equinix| 王冠城堡, Windstream |
| **达拉斯** | Equinix、Megaport | Axtel、C3ntro 电信、Cox Business、王冠城堡、Data Foundry、彩虹 Enterprise、Transtelco |
| **法兰克福** | Interxion | BICS、Cinia、Nianet、QSC AG、Telekom Deutschland GmbH |
| **汉堡** | Equinix | Cinia |
| **香港特别行政区** | Equinix | Chief、Macroview Telecom |
| **约翰内斯堡** | Teraco | MTN |
| **伦敦** | BICS、Equinix、euNetworks| Bezeq 国际有限公司, CoreAzure, Epsilon 电信有限, 指数 E, HSO, NexGen 网络, Proximus, Tamares 电信, Zain |
| **洛杉矶** | Equinix |王冠城堡, Transtelco |
| **马德里** | Level3 | Zertia |
| **Montreal** | Cologix、Equinix | Airgate Technologies, Inc.Aptum 技术、Rogers、Zirro |
| **纽约** |Equinix、Megaport | Altice Business、王冠城堡、彩虹 Enterprise、Webair |
| **巴黎** | Equinix | Proximus |
| **魁北克市** | Megaport | Fibrenoire |
| **圣保罗** | Equinix | Venha Pra Nuvem |
| **西雅图** |Equinix | Alaska Communications |
| **硅谷** |Coresite、Equinix | Cox Business, 频谱企业, Windstream, X2nsat Inc。 |
| **新加坡** |Equinix |1CLOUDSTAR，BICS，CMC 电信，Epsilon 电信有限，LGA 电信，美国（UIH） |
| **Slough** | Equinix | HSO|
| **悉尼** | Megaport | Macquarie Telecom Group|
| **东京** | Equinix | ARTERIA Networks Corporation、BroadBand Tower, Inc. |
| **多伦多** | Equinix、Megaport | Airgate 技术 Inc.，Beanfield Metroconnect，Aptum 技术，IVedha Inc.，Rogers，Thinktel，Zirro|
| **华盛顿特区** |Equinix | Altice Business、BICS、Cox Business、王冠城堡、Gtt communication Inc.、Epsilon 电信有限、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。 可以与下表中列出的任一系统集成商合作，以帮助将你加入 ExpressRoute。

| **所在洲** | **系统集成商** |
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
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地图"
