---
title: '연결 공급자 및 위치: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 연결 공급자에 따라 정렬됩니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 01/17/2020
ms.author: cherylmc
ms.openlocfilehash: da84bea51271829e7d3e513ada21c69dbd61916c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719953"
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

## <a name="locations"></a>地缘政治区域中的 ExpressRoute 位置的 Azure 区域。
다음 표에서 지역 내 ExpressRoute 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **오스트레일리아 정부** |오스트레일리아 중부, 오스트레일리아 중부 2 |캔버라, 캔버라2 |
| **유럽** | 法国中部、法国南部、德国北部、德国中西部、北欧、挪威东部、挪威西部、瑞士北部、瑞士西部、英国西部、英国南部、西欧 |阿姆斯特丹，Amsterdam2，哥本哈根，都柏林，法兰克福，Geneva，伦敦，London2，马赛，Milan，慕尼黑，纽波特（威尔士），Oslo，巴黎，Stavanger，斯德哥尔摩，苏黎世 |
| **북아메리카** |미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |亚特兰大、芝加哥、达拉斯、丹佛、拉斯维加斯、洛杉矶、迈阿密、纽约、San Antonio、西雅图、硅谷、硅 Valley2、华盛顿特区、华盛顿 DC2、蒙特利尔、魁北克市、多伦多 |
| **아시아** | 동아시아, 동남 아시아 |香港特别行政区，雅加达，吉隆坡，新加坡，新加坡2，台北 |
| **인도** | 인도 서부, 인도 중부, 인도 남부 |첸나이, 첸나이2, 뭄바이, 뭄바이2 |
| **일본** | 일본 서부, 일본 동부 |오사카, 도쿄 |
| **오세아니아** | 오스트레일리아 남동부, 오스트레일리아 동부 |奥克兰，墨尔本，Sydney2，悉尼， |
| **대한민국** | 한국 중부, 한국 남부 |부산, 서울|
| **阿拉伯联合酋长国** | 阿拉伯联合酋长国中部，阿拉伯联合酋长国北部 | Dubai、Dubai2 |
| **남아프리카 공화국** | 南非西部、南非北部 |케이프타운, 요하네스버그 |
| **남미** | 브라질 남부 |상파울루 |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>국내 클라우드에 대한 지역 및 지리적 경계
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **ExpressRoute 위치** |
| --- | --- | --- |
| **미국 정부 클라우드** |US Gov 애리조나, US Gov 아이오와, US Gov 텍사스, US Gov 버지니아, US DoD 중부, US DoD 동부  |시카고, 댈러스, 뉴욕, 피닉스, 샌안토니오, 시애틀, 실리콘밸리, 워싱턴 DC |
| **중국 동부** |중국 동부, 중국 동부2 |상하이, 상하이2 |
| **중국 북부** |중국 북부, 중국 북부2 |베이징, 베이징2 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 ExpressRoute SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 ExpressRoute 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="partners"></a>ExpressRoute 연결 공급자

다음 테이블에서는 서비스 공급자별 위치를 보여 줍니다. 위치별 사용 가능한 공급자를 보려는 경우 [위치별 서비스 공급자](expressroute-locations-providers.md)를 참조하세요.


### <a name="global-commercial-azure"></a>全球商业 Azure

| **서비스 공급자** | **Microsoft Azure** | **Office 365**  | **위치** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |지원됨 |지원됨 |멜버른, 시드니 |
| **[Airtel](https://www.airtel.in/business/#/)** | 지원됨 | 지원됨 | 첸나이2, 뭄바이2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |지원됨 |지원됨 |阿姆斯特丹、芝加哥、达拉斯、中国香港特别行政区、圣保罗、西雅图、硅谷、新加坡、东京、华盛顿特区 |
| **[Ascenty Data Cente](https://www.ascenty.com/en/cloud/microsoft-express-route)** |지원됨 |지원됨 |상파울루 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 런던, 실리콘밸리, 싱가포르, 시드니, 도쿄, 토론토, 워싱턴 DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |지원됨 |지원됨 |몬트리올, 토론토, 퀘벡 시티 |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |지원됨 |지원됨 |阿姆斯特丹、香港特别行政区、约翰内斯堡、伦敦、纽波特（威尔士）、圣保罗、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |지원됨 |지원됨 |마이애미 |
| **CDC** | 지원됨 | 지원됨 | 캔버라, 캔버라2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |지원됨 |지원됨 |Amsterdam2，芝加哥，香港特别行政区，拉斯维加斯，纽约，巴黎，San Antonio，硅谷，东京，多伦多，华盛顿特区，华盛顿特区 |
| **[首席电信](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |지원됨 |지원됨 |香港特别行政区，台北 |
| **China Telecom Global** |지원됨 |지원됨 |홍콩 특별 행정구 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |지원됨 |지원됨 |시카고, 댈러스, 몬트리올, 토론토, 워싱턴 DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |지원됨 |지원됨 |阿姆斯特丹、Amsterdam2、芝加哥、都柏林、法兰克福、伦敦、London2、纽波特、纽约、大阪、巴黎、硅谷、硅 Valley2、新加坡2、东京、华盛顿特区 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |지원됨 |지원됨 |芝加哥、丹佛、洛杉矶、纽约、硅谷、硅 Valley2、华盛顿特区、华盛顿特区 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | 지원됨 |지원됨 |Amsterdam2、法兰克福、马赛|
| **[Devoli](https://devoli.com/expressroute)** | 지원됨 |지원됨 | 奥克兰，墨尔本，悉尼 |
| **du datamena** |지원됨 |지원됨 | Dubai2 |
| **eir** |지원됨 |지원됨 |더블린|
| **[Epsilon 全局通信](https://www.epsilontel.com/solutions/direct-cloud-connect)** |지원됨 |지원됨 |싱가포르, 싱가포르 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원됨 |阿姆斯特丹、亚特兰大、芝加哥、达拉斯、都柏林、Geneva、香港特别行政区、伦敦、London2、洛杉矶、墨尔本、迈阿密、纽约、大阪、巴黎、圣保罗、西雅图、硅谷、新加坡、斯德哥尔摩、悉尼、东京、多伦多和华盛顿特区 |
| **Etisalat 阿拉伯联合酋长国** |지원됨 |지원됨 |두바이|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |지원됨 |지원됨 |阿姆斯特丹、Amsterdam2、都柏林、伦敦 |
| **FarEasTone** |지원됨 |지원됨 |타이베이|
| **GÉANT** |지원됨 |지원됨 |阿姆斯特丹、法兰克福、马赛 |
| **[GCX(Global Cloud Xchange)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | 지원됨| 지원됨 | 첸나이, 뭄바이 |
| **Intelsat** | 지원됨 | 지원됨 | 워싱턴 DC2 |
| **[InterCloud](https://www.intercloud.com/)** |지원됨 |지원됨 |阿姆斯特丹、芝加哥、香港特别行政区、伦敦、纽约、巴黎、硅谷、新加坡、华盛顿特区、瑞士 |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |지원됨 |지원됨 |芝加哥、达拉斯、硅谷、华盛顿特区 |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |지원됨 |지원됨 |오사카, 도쿄 |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |지원됨 |지원됨 |케이프타운, 요하네스버그, 런던 |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |지원됨 |지원됨 |阿姆斯特丹，Amsterdam2，哥本哈根，都柏林，法兰克福，伦敦，马赛，巴黎，苏黎世 |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|지원됨 |지원됨 | 阿姆斯特丹、London2、硅谷、多伦多、华盛顿特区 |
| **Jaguar 网络** |지원됨 |지원됨 |마르세유|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |지원됨 |지원됨 |런던 |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |지원됨 |지원됨 |서울 |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | 지원됨 |지원됨 |奥克兰，悉尼 |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | 지원됨 | 지원됨 | 암스테르담 |
| **[KT](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | 지원됨 | 지원됨 | 서울 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 런던, 뉴포트(웨일스), 상파울루, 시애틀, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **LG CNS** |지원됨 |지원됨 |부산, 서울 |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |지원됨 |지원됨 |케이프타운, 요하네스버그 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 |지원됨 |阿姆斯特丹、亚特兰大、奥克兰、芝加哥、达拉斯、丹佛、Dubai2、都柏林、法兰克福、香港特别行政区、拉斯维加斯、伦敦、London2、洛杉矶、墨尔本、迈阿密、蒙特利尔、纽约、Oslo、、魁北克城、San Antonio、西雅图、硅谷、新加坡新加坡2、悉尼、东京、多伦多、华盛顿特区、苏黎世 |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |지원됨 |지원됨 |런던 |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |지원됨 |지원됨 |达拉斯，洛杉矶，迈阿密，圣保罗，华盛顿特区 |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |지원됨 |지원됨 |뉴포트(웨일스) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |지원됨 |지원됨 |墨尔本，Sydney2，悉尼， |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |지원됨 |지원됨 |阿姆斯特丹、香港特别行政区、伦敦、洛杉矶、大阪、新加坡、悉尼、东京、华盛顿特区 |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |지원됨 |지원됨 |도쿄 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |지원됨 |지원됨 |오사카 |
| **[Optus](https://www.optus.com.au/enterprise/)** |지원됨 |지원됨 |멜버른, 시드니 |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |지원됨 |지원됨 |阿姆斯特丹、Amsterdam2、Dubai2、法兰克福、香港特别行政区、约翰内斯堡、伦敦、巴黎、圣保罗、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | 지원됨 | 지원됨 | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |지원됨 |지원됨 |芝加哥、香港特别行政区、伦敦 |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |지원됨 |지원됨 |서울 |
| **[工程师](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | 지원됨 |지원됨 | 워싱턴 D.C. |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |지원됨 |지원됨 |첸나이, 뭄바이2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |지원됨 |지원됨 |싱가포르, 싱가포르 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |지원됨 |지원됨 |오사카, 도쿄 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |지원됨 |지원됨 |奥克兰，悉尼 |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | 지원됨 | 지원됨 | 취리히 |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |지원됨 |지원됨 |阿姆斯特丹、钦奈、中国香港特别行政区、伦敦、孟买、圣保罗、硅谷、新加坡、华盛顿特区 |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |지원됨 |지원됨 |암스테르담, 상파울루 |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |지원됨 |지원됨 |伦敦、London2 |
| **Telenor** |지원됨 |지원됨 |阿姆斯特丹、伦敦、Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | 지원됨 | 지원됨 |阿姆斯特丹，芝加哥，达拉斯，法兰克福，香港特别行政区，伦敦，Oslo，巴黎，硅谷，斯德哥尔摩，华盛顿特区 |
| **Telmex Uninet**| 지원됨 | 지원됨 | 댈러스 |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |지원됨 |지원됨 |멜버른, 싱가포르, 시드니 |
| **[Telus](https://www.telus.com)** |지원됨 |지원됨 |蒙特利尔、西雅图、多伦多 |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |지원됨 |지원됨 |케이프타운, 요하네스버그 |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | 지원됨 | 지원됨 | 콸라룸푸르 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |지원됨 |지원됨 |댈러스, 로스앤젤레스|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |지원됨 |지원됨 |상파울루 |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |지원됨 |지원됨 |阿姆斯特丹、芝加哥、达拉斯、中国香港特别行政区、伦敦、孟买、硅谷、新加坡、悉尼、东京、多伦多、华盛顿特区 |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | 지원됨 | 지원됨 | 워싱턴 DC2 |
| **[Vocus 组 NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | 지원됨 | 지원됨 | 奥克兰，悉尼 |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |지원됨 |지원됨 |Amsterdam2、伦敦、新加坡 |
| **[Vodafone](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | 지원됨 | 지원됨 | 뭄바이, 뭄바이2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |지원됨 |지원됨 |阿姆斯特丹、芝加哥、达拉斯、丹佛、伦敦、洛杉矶、蒙特利尔、纽约、巴黎、西雅图、硅谷、多伦多、华盛顿特区、华盛顿特区 |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environment"></a>국가별 클라우드 환경

Azure 国内云彼此独立，并来自全球商业 Azure。 一个 Azure 云的 ExpressRoute 无法连接到其他 azure 云区域。 

### <a name="us-government-cloud"></a>미국 정부 클라우드

| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |芝加哥、Phoenix、硅谷、华盛顿特区 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |지원됨 |지원됨 |纽约、Phoenix、San Antonio、华盛顿特区 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원됨 |시카고, 댈러스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 | 지원됨 | 芝加哥，达拉斯，San Antonio，西雅图，华盛顿特区 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |지원됨 |지원됨 |시카고, 댈러스, 뉴욕, 실리콘밸리, 워싱턴 DC |

### <a name="china"></a>중국

| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **China Telecom** |지원됨 |지원되지 않음 |北京、Beijing2、上海、Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |지원됨 |지원되지 않음 |베이징2, 상하이2 |

자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)를 참조하세요.

### <a name="germany"></a>독일

| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |지원됨 |지원되지 않음 |베를린 |
| **Interxion** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨  | 지원되지 않음 | 베를린 |
| **T 시스템** |지원됨 |지원되지 않음 |베를린 |

## <a name="connectivity-through-exchange-providers"></a>通过 Exchange 提供商的连接

연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix 클라우드 Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

## <a name="connectivity-through-satellite-operators"></a>通过附属运算符连接
如果你是远程的并且没有光纤连接，或者想要浏览其他连接选项，则可以检查以下附属运算符。 

* Intelsat
* [工程师](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>通过其他服务提供商的连接

| **연결 공급자** | **Exchange** | **위치** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |싱가포르 |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | 토론토, 몬트리올 |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |뉴욕, 워싱턴 DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |도쿄 |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |댈러스|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |토론토|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | 런던 |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | 암스테르담, 프랑크푸르트, 런던, 싱가포르, 워싱턴 DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | 도쿄 |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | 댈러스 |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | 홍콩 특별 행정구 |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | 프랑크푸르트, 함부르크 |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | 암스테르담 | 
| **[CMC 电信](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | 싱가포르 | 
| **[Aptum 技术](https://aptum.com/services/cloud/managed-azure/)**| Equinix | 몬트리올, 토론토 |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | 런던 |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | 댈러스, 실리콘밸리, 워싱턴 DC |
| **[王冠城堡](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | 亚特兰大、芝加哥、达拉斯、洛杉矶、纽约、华盛顿特区 |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | 댈러스 |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | 런던, 싱가포르, 워싱턴 DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 암스테르담 |
| **[지수 E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | 런던 |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | 암스테르담 |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | 퀘벡 시티 |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | 워싱턴 D.C. |
| **[걸프 브리지 인터내셔널](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | 암스테르담 |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | 런던, 슬라우 |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | 토론토 |
| **[Kaalam 电信巴林 b. C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| 3级通信 |암스테르담 |
| **LGA Telecom** |Equinix |싱가포르|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |홍콩 특별 행정구 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 시드니 |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | 암스테르담 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 워싱턴 D.C. |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | 케이프타운, 요하네스버그 |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | 런던 |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | 암스테르담, 프랑크푸르트 |
| **[后期电信卢森堡](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | 암스테르담 |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | 암스테르담, 더블린, 런던, 파리 |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | 프랑크푸르트 |  
| **Rogers** | Cologix, Equinix | 몬트리올, 토론토 |
| **[彩虹企业](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | 芝加哥、达拉斯、洛杉矶、纽约、硅谷 | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | 런던 | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | 암스테르담 | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | 암스테르담 |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | 암스테르담, 프랑크푸르트 |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | 암스테르담 |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | 토론토 | 
| **[United Information Highway(UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | 싱가포르 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | 상파울루 |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | 뉴욕 |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | 시카고, 실리콘밸리, 워싱턴 DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |硅谷，硅谷2|
| **Zain** |Equinix |런던|
| **[Zertia](https://www.zertia.es)**| Level 3 | 마드리드 |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | 몬트리올, 토론토 |

## <a name="connectivity-through-datacenter-providers"></a>通过数据中心提供程序的连接

| **공급자** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport、PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport、PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX 到，Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport、PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX 到，Megaport，PacketFabric |
| **[QTS 数据中心](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport、PacketFabric |
| **[流数据中心]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire 데이터 센터](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX 到，Megaport，PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX 覆盖，Megaport |
| **[T5 데이터 센터](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>通过国家研究和教育网络（NREN）的连接

| **공급자**|
| --- |
| **AARNET**| 
| **DeIC(GÉANT를 통해)**|
| **GARR(GÉANT를 통해)**|
| **GÉANT**|
| **HEAnet(GÉANT를 통해)**|
| **Internet2**|
| **JISC**|
| **RedIRIS(GÉANT를 통해)**|
| **SINET**|
| **Surfnet(GÉANT를 통해)**|

* 연결 공급자가 목록에 없는 경우 위에 나열된 ExpressRoute Exchange 파트너 중 하나에 연결되어 있는지 확인해 주세요.

## <a name="expressroute-system-integrators"></a>ExpressRoute 시스템 통합업체
사용자 요구에 맞도록 프라이빗 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 ExpressRoute에 등록할 수 있습니다.

| **시스템 통합 업체** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | 유럽 |
| **[Avanade Inc.](https://www.avanade.com/)** | 아시아, 유럽, 북아메리카, 남미 |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | 유럽
| **[Ensyst](https://www.ensyst.com.au)** | 아시아
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | 북미 |
| **[FlexManage](https://www.flexmanage.com/cloud)** | 북미 |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | 북미 |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | 오스트레일리아 |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | 오스트레일리아 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 유럽(독일) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | 유럽 |
| **[새 서명](https://newsignature.com/technologies/express-route/)** | 유럽 |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | 아시아 |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | 유럽 |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 북미 |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | 북미 |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | 유럽 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | 남아메리카 |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | 오스트레일리아 |

## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"
