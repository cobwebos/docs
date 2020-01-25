---
title: '위치 및 연결 공급자: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 위치에 따라 정렬됩니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: cherylmc
ms.openlocfilehash: cbddd912831247e30f2913414c6dfa13131dde99
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719851"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


本文中的表格提供有关 ExpressRoute 地理覆盖范围和位置、ExpressRoute 连接提供商和 ExpressRoute 系统集成商（Si）的信息。

> [!Note]
> Azure 区域和 ExpressRoute 位置是两个不同的不同概念，了解这两者之间的区别对于探索 Azure 混合网络连接至关重要。 
>
>

## <a name="azure-regions"></a>Azure 지역
Azure 区域是 Azure 计算、网络和存储资源所在的全球数据中心。 创建 Azure 资源时，客户需要选择资源位置。 资源位置确定在哪个 Azure 数据中心（或可用性区域）中创建资源。

## <a name="expressroute-locations"></a>ExpressRoute 位置
ExpressRoute 位置（有时称为 "对等互连位置" 或 "满足我的位置"）是 Microsoft 企业边缘（MSEE）设备所在的归置设施。 ExpressRoute 位置是 Microsoft 网络的入口点，是全球分布的，为客户提供了连接到全球各地 Microsoft 网络的机会。 这些位置是 ExpressRoute 合作伙伴和 ExpressRoute 直销客户在与 Microsoft 网络之间建立交叉连接的地方。 通常，ExpressRoute 位置不需要匹配 Azure 区域。 例如，客户可以在*西雅图*对等位置使用资源位置 "*美国东部*" 创建 ExpressRoute 线路。

지역 내 하나 이상의 ExpressRoute 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 

## <a name="locations"></a>地缘政治区域中 ExpressRoute 位置的 Azure 区域
다음 표에서 지역 내 ExpressRoute 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **오스트레일리아 정부** | 오스트레일리아 중부, 오스트레일리아 중부 2 |캔버라, 캔버라2 |
| **유럽** | 法国中部、法国南部、德国北部、德国中西部、北欧、挪威东部、挪威西部、瑞士北部、瑞士西部、英国西部、英国南部、西欧 |阿姆斯特丹，Amsterdam2，哥本哈根，都柏林，法兰克福，Geneva，伦敦，London2，马赛，Milan，慕尼黑，纽波特（威尔士），Oslo，巴黎，Stavanger，斯德哥尔摩，苏黎世，慕尼黑 |
| **북아메리카** | 미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |亚特兰大、芝加哥、达拉斯、丹佛、拉斯维加斯、洛杉矶、迈阿密、纽约、San Antonio、西雅图、硅谷、硅 Valley2、华盛顿特区、华盛顿 DC2、蒙特利尔、魁北克市、多伦多 |
| **아시아** | 동아시아, 동남 아시아 |香港特别行政区，雅加达，吉隆坡，新加坡，新加坡2，台北 |
| **인도** | 인도 서부, 인도 중부, 인도 남부 |첸나이, 첸나이2, 뭄바이, 뭄바이2 |
| **일본** | 일본 서부, 일본 동부 |오사카, 도쿄 |
| **오세아니아** | 오스트레일리아 남동부, 오스트레일리아 동부 |奥克兰，墨尔本，Sydney2，悉尼， | 
| **대한민국** | 한국 중부, 한국 남부 |부산, 서울|
| **阿拉伯联合酋长国** | 阿拉伯联合酋长国中部，阿拉伯联合酋长国北部 | Dubai、Dubai2 |
| **남아프리카 공화국** | 南非西部、南非北部 |케이프타운, 요하네스버그 |
| **남미** | 브라질 남부 |상파울루 |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>适用于全国云的 Azure 区域和地缘政治边界
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **미국 정부 클라우드** |US Gov 애리조나, US Gov 아이오와, US Gov 텍사스, US Gov 버지니아, US DoD 중부, US DoD 동부  |시카고, 댈러스, 뉴욕, 피닉스, 샌안토니오, 시애틀, 실리콘밸리, 워싱턴 DC |
| **중국 동부** |중국 동부, 중국 동부2 |상하이, 상하이2 |
| **중국 북부** |중국 북부, 중국 북부2 |베이징, 베이징2 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 ExpressRoute SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 ExpressRoute 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="partners"></a>ExpressRoute 연결 공급자

다음 테이블에서는 각 위치에 대한 연결 위치 및 서비스 공급자를 보여 줍니다. 서비스를 제공할 수 있는 서비스 공급자 및 위치를 보려면 [서비스 공급자별 위치](expressroute-locations.md)를 참조하세요.

* **本地 Azure 区域**是每个对等位置的[ExpressRoute 本地](expressroute-faqs.md)可访问的区域。 **n/a**表示 ExpressRoute 本地在该对等位置不可用。

* **区域**是指[定价](https://azure.microsoft.com/pricing/details/expressroute/)。


### <a name="global-commercial-azure"></a>全球商业 Azure
| **위치** | **주소** | **영역** | **本地 Azure 区域** | **ER 直接** | **서비스 공급자** |
| --- | --- | --- | --- | --- | --- |
| **암스테르담** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 서유럽 | 10G，100G | Aryaka networks、网络，& T NetBond，英国电信，Colt，Equinix，euNetworks，GÉANT，InterCloud，Interxion，KPN，IX 到，Level 3 通信，Megaport，NTT 通信，橙色，Tata 通信，Telefonica，Telenor，Telia 载波，Verizon，Zayo |
| **암스테르담2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 서유럽 | 10G，100G | CenturyLink Cloud Connect、Colt、CIX、euNetworks、Interxion、橙色、Vodafone |
| **애틀랜타** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/a | n/a | Equinix, Megaport |
| **奥克兰** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/a | 10G | Devoli、Kordia、Megaport、Spark NZ、Vocus Group NZ |
| **부산** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | 한국 남부 | n/a | LG CNS |
| **캔버라** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 | 10G，100G | CDC |
| **캔버라2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 2| 10G，100G | CDC |
| **케이프타운** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 남아프리카 공화국 서부 | 10G | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **첸나이** | Tata Communications | 2 | 인도 남부 | 10G | Global CloudXchange(GCX), SIFY, Tata Communications |
| **첸나이2** | Airtel | 2 | 인도 남부 | n/a | Airtel |
| **시카고** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 미국 중북부 | 10G，100G | Aryaka networks、网络，在 & T NetBond，CenturyLink Cloud Connect，Cologix，Colt，Comcast，Coresite，Equinix，InterCloud，Internet2，Level 3 通信，Megaport，PacketFabric，PCCW 全局有限，，Telia 载波，Verizon，Zayo |
| **哥本哈根** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/a | 10G | Interxion |
| **댈러스** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/a | 10G，100G | Aryaka networks、网络，在 & T NetBond，Cologix，Equinix，Internet2，Level 3 通信，Megaport，Neutrona 网络，Telmex Uninet，Telia 载波，Transtelco，Verizon，Zayo|
| **덴버** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 미국 중서부 | n/a | CoreSite、Megaport、Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 아랍에미리트 북부 | n/a | Etisalat 阿拉伯联合酋长国 |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | 아랍에미리트 북부 | n/a | du datamena，Megaport，橙色，Orixcom |
| **더블린** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 북유럽 | 10G，100G | Colt, eir, Equinix, Interxion, Megaport |
| **프랑크푸르트** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 독일 중서부 | 10G，100G | Colt，CIX，GEANT，Interxion，Megaport，橙色，Telia 载波 |
| **Geneva** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 스위스 서부 | 10G，100G | Equinix |
| **香港特别行政区** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 동아시아 | n/a | Aryaka networks、网络，英国电信，CenturyLink 云连接，首席电信，中国电信全局，Equinix，InterCloud，Megaport，NTT 通信，橙色，PCCW 全局有限，Tata 通信，Telia 载波，Verizon |
| **雅加达** | Telkom 印度尼西亚 | 4 | n/a | 10G | |
| **요하네스버그** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 남아프리카 공화국 북부 | 10G | 英国电信，Internet 解决方案-云连接，液体电信，橙色，Teraco |
| **콸라룸푸르** | [时间 dotCom Menara 目标](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/a | n/a | TIME dotCom |
| **라스베이거스** | [切换 LV](https://www.switch.com/las-vegas) | 1 | n/a | n/a | CenturyLink Cloud Connect, Megaport |
| **런던** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 영국 남부 | 10G，100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **런던2** | [Telehouse 北2](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 영국 남부 | 10G，100G | Colt、IX 范围、Equinix、Megaport、Telehouse-KDDI |
| **로스앤젤레스** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/a | n/a | CoreSite、Equinix、Megaport、Neutrona Networks、NTT、Transtelco、Zayo |
| **마르세유** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 프랑스 남부 | n/a | CIX、GEANT、Interxion、Jaguar 网络 |
| **멜버른** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 오스트레일리아 남동부 | 10G，100G | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG 电信 |
| **마이애미** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/a | 10G | C3ntro、Equinix、Megaport、Neutrona 网络 |
| **Milan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/a | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/a | n/a | 铃加拿大、Cologix、Megaport、Telus、Zayo |
| **뭄바이** | Tata Communications | 2 | 인도 서부 | n/a | Global CloudXchange （GCX），依赖 Jio，Sify，Tata 通信，Verizon |
| **뭄바이2** | Airtel | 2 | 인도 서부 | n/a | Airtel, Sify, Vodafone Idea |
| **慕尼黑工厂** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/a | 10G，100G | |
| **뉴욕** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/a | n/a | CenturyLink Cloud Connect、Colt、Coresite、Equinix、InterCloud、Megaport、Packet、Zayo |
| **뉴포트(웨일스)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | 영국 서부 | n/a | 英国电信，Colt，级别3通信，下一代数据 |
| **오사카** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 일본 서부 | 10G，100G | Colt，Equinix，Internet 倡议日本 Inc.-IIJ，NTT 通信，NTT SmartConnect，Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 노르웨이 동부 | 10G，100G | Megaport、Telenor、Telia 载波 |
| **파리** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | 프랑스 중부 | n/a | CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Interxion、橙色、Telia 承运商、Zayo |
| **퍼스** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/a | 10G | Megaport、NextDC |
| **퀘벡 시티** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 캐나다 동부 | n/a | Bell Canada, Megaport |
| **샌안토니오** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 미국 중남부 | 10G，100G | CenturyLink Cloud Connect, Megaport |
| **상파울루** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 브라질 남부 | n/a | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **시애틀** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 미국 서부 2 | 10G，100G | Aryaka networks、网络，Equinix，Level 3 通信，Megaport，Telus，Zayo |
| **서울** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | 한국 중부 | 10G，100G | KINX、KT、LG 和 Sejong 电信 |
| **실리콘밸리** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 미국 서부 | 10G，100G | Aryaka networks、网络，在 & T NetBond，英国电信，CenturyLink Cloud Connect，Colt，Comcast，Coresite，Equinix，InterCloud，Internet2，IX 覆盖，数据包，PacketFabric，Level 3 通信，Megaport，橙色，冲刺（Sprint），Tata，Telia，Verizon、Zayo |
| **硅 Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 미국 서부 | 10G，100G | Colt、Coresite | 
| **싱가포르** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 동남아시아 | 10G，100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [全局交换器 Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 동남아시아 | 10G，100G | Colt、Epsilon Global communication、Megaport、SingTel |
| **Stavanger** | [绿色山地 DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 노르웨이 서부 | 10G，100G | |
| **斯德哥尔摩** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/a | 10G | Equinix，Telia 电信公司 |
| **시드니** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 오스트레일리아 동부 | 10G，100G | AARNet，AT & T NetBond，英国电信，Devoli，Equinix，Kordia，Megaport，NEXTDC，NTT 通信，Optus，橙色，Spark NZ，Telstra Corporation，TPG 电信，Verizon，Vocus 组 NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 오스트레일리아 동부 | 10G，100G | NextDC |
| **타이베이** | Chief Telecom | 2 | n/a | 10G | 首席电信，FarEasTone |
| **도쿄** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 일본 동부 | 10G，100G | Aryaka networks、网络，在 & T NetBond，英国电信，CenturyLink Cloud Connect，Colt，Equinix，Internet 计划日本 Inc.-IIJ，Megaport，NTT 通信，NTT EAST，橙色，Softbank，Verizon |
| **토론토** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 캐나다 중부 | 10G，100G | & T NetBond、钟形加拿大、CenturyLink Cloud Connect、Cologix、Equinix、IX 到 Megaport、Telus、Verizon、Zayo |
| **워싱턴 DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 美国东部、美国东部2 | 10G，100G | Aryaka networks、网络，在 & T NetBond，英国电信，CenturyLink Cloud Connect，Cologix，Colt，Comcast，Coresite，Equinix，Internet2，InterCloud，IX 到，Level 3 通信，Megaport，Neutrona 网络，NTT 通信，橙色，PacketFabric，SES，冲刺（Sprint），Tata 通信，Telia 电信公司，Verizon，Zayo |
| **워싱턴 DC2** | [Coresite 场](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 美国东部、美国东部2 | 10G，100G | CenturyLink Cloud Connect、Coresite、Intelsat、Viasat、Zayo | 
| **苏黎世** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 스위스 북부 | n/a | Intercloud、Interxion、Megaport、Swisscom |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environments"></a>국가별 클라우드 환경

Azure 国内云彼此独立，并来自全球商业 Azure。 一个 Azure 云的 ExpressRoute 无法连接到其他 azure 云区域。

### <a name="us-government-cloud"></a>미국 정부 클라우드
| **위치** | **주소** | **本地 Azure 区域**| **ER 直接** | **서비스 공급자** |
| --- | --- | --- | --- | --- |
| **시카고** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/a | 10G，100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **댈러스** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | 10G，100G | Equinix, Megaport, Verizon |
| **뉴욕** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/a | 10G，100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 애리조나 | n/a | 在 & T NetBond，CenturyLink Cloud Connect，Megaport |
| **샌안토니오** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 텍사스 | n/a | CenturyLink Cloud Connect, Megaport |
| **실리콘밸리** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/a | 10G，100G | 在 & T，Equinix，Level 3 通信，Verizon |
| **시애틀** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/a | n/a | Equinix, Megaport |
| **워싱턴 DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD 东部，US Gov 弗吉尼亚州 | 10G，100G | & T NetBond，CenturyLink Cloud Connect，Equinix，Level 3 通信，Megaport，Verizon |

### <a name="china"></a>중국
| **위치** | **서비스 공급자** |
| --- | --- |
| **베이징** |China Telecom |
| **베이징2** | 中国电信，GDS |
| **상하이** |China Telecom |
| **상하이2** | 中国电信，GDS |

자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>독일
| **위치** | **서비스 공급자** |
| --- | --- |
| **베를린** |e-shelter, Megaport+, T-Systems |
| **프랑크푸르트** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>通过 Exchange 提供商的连接
연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix 클라우드 Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

## <a name="connectivity-through-satellite-operators"></a>通过附属运算符连接
如果你是远程的并且没有光纤连接，或者想要浏览其他连接选项，则可以检查以下附属运算符。 

* Intelsat
* [工程师](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>通过其他服务提供商的连接
| **위치** | **Exchange** | **连接提供程序** |
| --- | --- | --- |
| **암스테르담** | Equinix，Interxion，Level 3 通信 | BICS、CloudXpress、Eurofiber、Fastweb s.p.a、、、Kalaam 电信巴林 b. C、MainOne、Nianet、电信 Proximus、Erhverv、TDC Italia、电信 Telekom 火花、Deutschland GmbH Telia、 |
| **애틀랜타** | Equinix| 王冠城堡
| **케이프타운** | Teraco | MTN |
| **시카고** | Equinix| 王冠城堡，Windstream |
| **댈러스** | Equinix, Megaport | Axtel、C3ntro 电信、Cox Business、王冠城堡、Data Foundry、彩虹 Enterprise、Transtelco |
| **프랑크푸르트** | Interxion | BICS、Cinia、Nianet、QSC AG、Telekom Deutschland GmbH |
| **함부르크** | Equinix | 중국 |
| **香港特别行政区** | Equinix | Chief, Macroview Telecom |
| **요하네스버그** | Teraco | MTN |
| **런던** | BICS、Equinix、euNetworks| Bezeq 国际有限公司，CoreAzure，Epsilon 电信有限，指数 E，HSO，NexGen 网络，Proximus，Tamares 电信，Zain |
| **로스앤젤레스** | Equinix |王冠城堡，Transtelco |
| **마드리드** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate 技术，Inc. Aptum 技术，Rogers，Zirro |
| **뉴욕** |Equinix, Megaport | Altice Business、王冠城堡、彩虹 Enterprise、Webair |
| **파리** | Equinix | Proximus |
| **퀘벡 시티** | Megaport | Fibrenoire |
| **상파울루** | Equinix | Venha Pra Nuvem |
| **시애틀** |Equinix | Alaska Communications |
| **실리콘밸리** |Coresite、Equinix | Cox Business，频谱企业，Windstream，X2nsat Inc。 |
| **싱가포르** |Equinix |1CLOUDSTAR，BICS，CMC 电信，Epsilon 电信有限，LGA 电信，美国（UIH） |
| **슬라우** | Equinix | HSO|
| **시드니** | Megaport | Macquarie Telecom Group|
| **도쿄** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **토론토** | Equinix, Megaport | Airgate 技术 Inc.，Beanfield Metroconnect，Aptum 技术，IVedha Inc.，Rogers，Thinktel，Zirro|
| **워싱턴 DC** |Equinix | Altice Business、BICS、Cox Business、王冠城堡、Gtt communication Inc.、Epsilon 电信有限、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 시스템 통합업체
사용자 요구에 맞도록 프라이빗 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 ExpressRoute에 등록할 수 있습니다.

| **Continent** | **시스템 통합 업체** |
| --- | --- |
| **아시아** |Avanade Inc., OneAs1a |
| **오스트레일리아** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **유럽** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **북아메리카** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **남미** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"
